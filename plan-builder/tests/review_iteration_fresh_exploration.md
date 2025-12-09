# Review Iteration Fresh Exploration 테스트

**목적**: plan-builder의 Step A가 각 iteration마다 새로운 문제를 탐색하는지 검증

**버전**: v2.2.0
**작성 일자**: 2025-12-10

---

## Test Case 1: 새로운 문제 탐색 확인

### 시나리오

plan-builder가 이전 iteration에서 발견한 문제만 재확인하지 않고, 새로운 유형의 문제도 탐색하는지 검증

### 전제 조건 (Given)

- 불완전한 초기 계획 파일 생성:
  - Task A: Testing Strategy 누락
  - Task B: Testing Strategy 누락
  - Task C: Task Independence 문제 존재 (Task C와 Task D가 tightly coupled)
  - Task D: Edge case 처리 누락

### 실행 (When)

1. plan-builder 실행 (Iteration 1)
2. Iteration 1 리뷰에서 Task A, B의 Testing Strategy 누락 발견
3. Feedback 적용: Task A, B에 Testing Strategy 추가
4. Iteration 2 실행

### 기대 결과 (Then)

Iteration 2 리뷰 파일 `*_PLAN_REVIEW_v2.md`에서:

- ✅ [CARRYOVER] Task A, B의 Testing Strategy 수정 확인 (품질 개선)
- ✅ [NEW] Task C의 Task Independence 문제 발견
- ✅ [NEW] Task D의 Edge case 처리 누락 발견
- ❌ "이전 이슈 해결됨, Approve" 조기 종료하지 않음

### 성공 기준

- **Iteration 2에서 [NEW] 태그 1개 이상 존재** ⚠️ **CRITICAL**
  - **If [NEW] count == 0**: Test FAIL - 새 탐색 실패 증거 (Risk 5 발생)
  - **검증 명령**: `grep -c "\[NEW\]" *_PLAN_REVIEW_v2.md` → 결과가 1 이상이어야 함
- Task C, D 관련 이슈가 Iteration 2에서 처음 발견됨
- Iteration 2가 "Needs Iteration" 권고
- (단순히 "이전 이슈 해결됨, Approve" 조기 종료가 아님)

---

## Test Case 2: 전체 체크리스트 재적용 확인

### 시나리오

plan-builder가 이전 iteration에서 확인하지 않은 체크리스트 섹션도 재검토하는지 검증

### 전제 조건 (Given)

- 불완전한 초기 계획 파일 생성:
  - Section 1 (Completeness) 관련 이슈 존재
  - Section 3 (Testing Strategy) 관련 이슈 존재
  - Section 2 (Task Independence) 관련 이슈 존재 (하지만 Iteration 1에서 미발견)

### 실행 (When)

1. plan-builder 실행 (Iteration 1)
2. Iteration 1 리뷰에서 Section 1, 3만 이슈 발견
3. Feedback 적용: Section 1, 3 이슈 수정
4. Iteration 2 실행

### 기대 결과 (Then)

Iteration 2 리뷰에서:

- ✅ Section 1, 3 수정 확인 (CARRYOVER 이슈 해결)
- ✅ Section 2 (Task Independence) 재검토하여 새로운 이슈 발견
- ✅ Section 4, 5도 재검토 (이전에 clean이라고 가정하지 않음)
- ❌ "이전에 확인 안 한 섹션은 OK로 가정" 하지 않음

### 성공 기준

- Iteration 2에서 3개 이상의 체크리스트 섹션 검토 증거
- "FULL checklist를 처음부터 적용" 명시
- Section 2 관련 [NEW] 이슈 발견

---

## 실행 방법

### 수동 테스트

1. 불완전한 샘플 계획 파일 준비:
   ```bash
   cp plan-builder/tests/sample_incomplete_plan.md TEST_PLAN.md
   ```

2. plan-builder 실행:
   ```bash
   # Claude Code에서 plan-builder 스킬 실행
   /skill plan-builder
   # Input: TEST_PLAN.md
   ```

3. 각 iteration의 리뷰 파일 분석:
   ```bash
   # Iteration 1
   cat TEST_PLAN_REVIEW_v1.md | grep -E "(🔴|🟡)"

   # Iteration 2
   cat TEST_PLAN_REVIEW_v2.md | grep -E "\[(CARRYOVER|NEW)\]"
   ```

4. 검증:
   - [ ] Iteration 2에 [NEW] 태그 존재
   - [ ] 새로운 카테고리의 이슈 발견
   - [ ] 전체 체크리스트 섹션 커버리지 증가

### 자동화 (향후)

```bash
# 테스트 스크립트 작성 예정
./test_plan_builder_iteration.sh TEST_PLAN.md
```

---

## 검증 체크리스트

### Iteration 1 검증

- [ ] 리뷰 파일 생성됨: `*_PLAN_REVIEW_v1.md`
- [ ] 이슈 발견됨 (Required Changes 또는 Suggested Improvements)
- [ ] 모든 이슈가 [NEW]로 태깅됨 (N=1이므로 CARRYOVER 없음)
- [ ] Recommendation: "Needs Iteration"

### Iteration 2 검증

- [ ] 리뷰 파일 생성됨: `*_PLAN_REVIEW_v2.md`
- [ ] [CARRYOVER] 태그 존재 (이전 이슈 추적)
- [ ] [NEW] 태그 존재 ⚠️ **CRITICAL** (새 탐색 증거)
- [ ] 이전에 발견 안 한 체크리스트 섹션의 이슈 발견
- [ ] Recommendation: "Needs Iteration" OR "Approve" (진행 상황에 따라)

### Iteration 3+ 검증

- [ ] [CARRYOVER] 이슈 수 감소 추세 (피드백 적용 효과)
- [ ] Iteration N에서 여전히 [NEW] 이슈 발견 가능 (품질 향상)
- [ ] ZERO 이슈 달성 시: Recommendation: "Approve" + Overall Assessment: "Strong"

---

## 실패 시나리오

### Scenario A: 새 탐색 실패

**증상**:
- Iteration 2에서 [NEW] 태그가 0개
- "이전 이슈 해결됨, Approve" 조기 종료

**원인**:
- CRITICAL INSTRUCTION 무시
- 이전 리뷰 맥락에 과도하게 의존
- FULL checklist 재적용 안 함

**해결**:
- SKILL.md Step 3의 CRITICAL INSTRUCTION 블록 재확인
- review_checklist.md Step 2의 MANDATORY 지시사항 재확인

### Scenario B: CARRYOVER 태깅 오류

**증상**:
- 이전 리뷰에 있던 이슈가 [NEW]로 태깅됨
- 또는 새 이슈가 [CARRYOVER]로 태깅됨

**원인**:
- Step 1 (Read Previous Review)에서 이전 리뷰 읽기 실패
- Step 4 (Categorize Findings)의 비교 로직 오류

**해결**:
- 이전 리뷰 파일 경로 확인
- 태깅 로직 재검토

---

## 참고 자료

- [PLAN_BUILDER_REVIEW_ITERATION_ISSUE_REPORT.md](../../PLAN_BUILDER_REVIEW_ITERATION_ISSUE_REPORT.md) - 분석 리포트
- [plan-builder/SKILL.md](../SKILL.md) - Step A 개선 사항
- [plan-builder/references/review_checklist.md](../references/review_checklist.md) - 강화된 체크리스트

---

**마지막 업데이트**: 2025-12-10
**테스트 작성자**: Claude Sonnet 4.5
**버전**: v2.2.0
