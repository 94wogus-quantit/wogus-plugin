# Loop Verification Test Cases

이 문서는 plan-builder 스킬의 피드백 루프 동작을 검증하기 위한 테스트 케이스를 정의합니다.

---

## Test Case 1: Minimum Iterations

**목표**: plan-builder가 최소 2-3회 이상 반복하는지 검증

**Given**:
- 새로운 계획을 생성하는 시나리오
- 첫 번째 리뷰에서 일부러 엄격하게 리뷰

**Expected**:
- Iteration 1에서 "Approve" 결과가 나오지 않음
- 최소 2회 이상의 리뷰-수정 사이클 발생
- 각 iteration마다 `[FEATURE]_PLAN_REVIEW_v[N].md` 파일이 생성됨
- 최종적으로 ZERO 이슈가 될 때까지 반복

**Fail Condition**:
- Iteration 1에서 즉시 "Approve" 발생
- 리뷰 파일이 버전 번호 없이 생성됨
- Loop를 건너뛰고 Phase 3로 이동

**Test Commands**:
```bash
# 생성된 리뷰 파일 개수 확인 (최소 2개 이상이어야 함)
ls -la *_PLAN_REVIEW_v*.md | wc -l

# 각 리뷰 파일의 iteration 번호 확인
grep -h "Iteration" *_PLAN_REVIEW_v*.md
```

---

## Test Case 2: Issue Counting

**목표**: Step B에서 이슈 카운트가 정확하게 수행되는지 검증

**Given**:
- 리뷰 파일에 명시적으로 표시된 이슈들:
  - 🔴 Required Changes: 3개
  - 🟡 Suggested Improvements: 2개

**Expected**:
- Step B에서 정확히 카운트:
  - required_changes_count = 3
  - suggested_improvements_count = 2
  - total_issues = 5
- Iteration Status Report에 정확한 숫자 출력
- Step C에서 total_issues > 0이므로 루프 계속

**Fail Condition**:
- 이슈 카운트가 부정확함
- 🔴와 🟡를 구분하지 못함
- total_issues 계산 오류

**Test Commands**:
```bash
# 리뷰 파일에서 Required Changes 개수 카운트
grep -c "🔴" [FEATURE]_PLAN_REVIEW_v1.md

# Suggested Improvements 개수 카운트
grep -c "🟡" [FEATURE]_PLAN_REVIEW_v1.md

# Iteration Status Report에서 숫자 확인
grep "Required Changes:" [FEATURE]_PLAN_REVIEW_v1.md
grep "Suggested Improvements:" [FEATURE]_PLAN_REVIEW_v1.md
grep "Total Issues:" [FEATURE]_PLAN_REVIEW_v1.md
```

---

## Test Case 3: Loop Continuation

**목표**: 이슈가 있을 때 루프가 계속되는지 검증

**Given**:
- Step B에서 total_issues = 5 (ZERO가 아님)
- Overall Assessment = "Good" (NOT "Strong")
- Recommendation = "Needs Iteration"

**Expected**:
- Step C에서 Decision: "❌ NOT READY - Proceeding to Step D"
- Step D로 진행하여 피드백 적용
- N = N + 1 (iteration counter 증가)
- **MANDATORY**: 다시 Step A로 돌아감 (새로운 iteration 시작)
- Iteration Transition Message 출력

**Fail Condition**:
- 이슈가 있는데도 Phase 3로 이동
- Step D 실행 후 Step A로 돌아가지 않음
- Iteration counter가 증가하지 않음

**Test Commands**:
```bash
# Iteration Transition Message 확인
grep "Iteration Transition" [실행 로그 또는 출력]

# Step D 후 Step A 재실행 확인 (새 iteration 시작)
grep "Iteration #2" [실행 로그]
```

---

## Test Case 4: Proper Exit

**목표**: ZERO 이슈일 때만 루프가 종료되는지 검증

**Given**:
- Step B에서 total_issues = 0
- Overall Assessment = "Strong"
- Recommendation = "Approve"
- required_changes_count = 0
- suggested_improvements_count = 0

**Expected**:
- Step C에서 Decision: "✅ READY - ZERO issues found, exiting loop to Phase 3"
- Loop Exit Condition의 모든 조건 충족 확인
- Phase 3 (Finalization)로 이동
- "Plan approved after [N] iterations - ZERO remaining issues!" 메시지 출력

**Fail Condition**:
- ZERO 이슈인데도 루프 계속
- "Good" Assessment로 Approve (should be "Strong")
- Suggested Improvements가 남아있는데 Approve

**Test Commands**:
```bash
# 최종 리뷰 파일의 이슈 카운트 확인 (모두 0이어야 함)
grep "Required Changes: 0" [FEATURE]_PLAN_REVIEW_v[N].md
grep "Suggested Improvements: 0" [FEATURE]_PLAN_REVIEW_v[N].md

# Overall Assessment가 "Strong"인지 확인
grep "Overall Assessment: Strong" [FEATURE]_PLAN_REVIEW_v[N].md

# Phase 3 진입 메시지 확인
grep "Phase 3: Finalization\|Plan approved after" [실행 로그]
```

---

## Test Case 5: Version Tracking

**목표**: 리뷰 파일이 버전 번호와 함께 보존되는지 검증

**Given**:
- 3회 iteration 수행
- 각 iteration마다 리뷰 파일 생성

**Expected**:
- Iteration 1 후: `[FEATURE]_PLAN_REVIEW_v1.md` 존재
- Iteration 2 후: `[FEATURE]_PLAN_REVIEW_v1.md`, `v2.md` 모두 존재
- Iteration 3 후: `v1.md`, `v2.md`, `v3.md` 모두 존재
- **중간 리뷰 파일은 삭제되지 않음** (Phase 2 동안)
- Phase 3에서만 정리됨 (아카이브 또는 삭제)

**Fail Condition**:
- 이전 iteration의 리뷰 파일이 즉시 삭제됨
- 버전 번호 없이 덮어쓰기됨
- 버전 번호 형식이 일관되지 않음 (v1, v2 vs REVIEW_1, REVIEW_2)

**Test Commands**:
```bash
# Iteration 2 시점에 v1, v2 파일 모두 존재하는지 확인
ls -la [FEATURE]_PLAN_REVIEW_v1.md [FEATURE]_PLAN_REVIEW_v2.md

# 버전 번호 형식 일관성 확인
ls [FEATURE]_PLAN_REVIEW_v*.md

# Phase 3에서 정리 후 FINAL 파일만 남았는지 확인
ls [FEATURE]_PLAN_REVIEW_FINAL.md
ls .archive/[FEATURE]_PLAN_REVIEW_v*.md  # 아카이브 옵션 선택 시
```

---

## 종합 검증 체크리스트

실제 plan-builder 스킬 실행 후 다음을 확인:

- [ ] **Minimum Iterations**: 최소 2회 이상 반복됨
- [ ] **Issue Counting**: 이슈 카운트가 정확함
- [ ] **Loop Continuation**: 이슈 있을 때 루프 계속됨
- [ ] **Proper Exit**: ZERO 이슈일 때만 종료됨
- [ ] **Version Tracking**: 리뷰 파일이 버전 번호와 함께 보존됨
- [ ] **Iteration Status Report**: 각 iteration마다 출력됨
- [ ] **Iteration Transition Message**: Step D 후 출력됨
- [ ] **Phase 3 Cleanup**: 최종적으로 리뷰 파일 정리됨

---

## 참고 사항

이 테스트 케이스들은 **plan-builder 스킬 자체가 실행될 때** 자동으로 동작해야 하는 행동을 정의합니다.

실제 검증 방법:
1. plan-builder 스킬로 새로운 계획 생성
2. 실행 로그 및 생성된 파일 확인
3. 위의 5개 테스트 케이스 조건 충족 여부 검증

예상되는 성공 시나리오:
- ✅ Iteration 1: 9 issues found → Step D (apply feedback)
- ✅ Iteration 2: 2 issues found → Step D (apply feedback)
- ✅ Iteration 3: ZERO issues → Exit to Phase 3 ✅
