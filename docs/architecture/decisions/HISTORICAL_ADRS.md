# Historical Architecture Decision Records

This file contains historical ADRs from v2.0.0 ~ v2.3.0 for reference.

---

## v2.3.0 - Plugin에 MCP 서버 자동 통합

**Date**: 2025-12-10

### Context
v2.2.0까지 workflow-skills plugin을 설치한 사용자는 MCP 서버(sequential-thinking, context7 등)를 별도로 `.mcp.json`에 설정해야 했습니다.

### Problem
- **복잡한 설치**: Plugin 설치 후 `.mcp.json` 수동 설정 필요
- **에러 발생**: MCP 서버 미설정 시 Skills 실행 실패
- **보안 리스크**: API 키를 `.mcp.json`에 하드코딩

### Decision
marketplace.json에 mcpServers 필드 추가:
- sequential-thinking, context7, serena 자동 활성화
- 환경 변수로 API 키 처리
- uvx로 Serena 자동 설치

### Impact
- Plugin 설치만으로 MCP 서버 자동 활성화
- API 키가 Git 히스토리에 노출되지 않음
- Breaking Change: 없음

---

## v2.2.0 - plan-builder 리뷰 iteration의 새로운 탐색 강제

**Date**: 2025-12-10

### Context
plan-builder의 "iterative review loop"에서 각 iteration이 이전 리뷰 포인트만 재확인하고 새로운 문제를 놓칠 위험 발견.

### Problem
- Step A가 이전 리뷰 파일을 읽지 않아 피드백 적용 확인 불가능
- "FULL checklist 재적용" 지시가 없어 카테고리 skip 가능
- CARRYOVER vs NEW 구분 없어 개선 여부 불명확

### Decision
Incremental Review with Strong Mandates:
- Step A에 5단계 프로세스 도입 (Read Previous → FULL FRESH Review → Categorize)
- CRITICAL INSTRUCTION 블록 추가 ("DO NOT assume", "LOOK FOR NEW PROBLEMS")
- CARRYOVER/NEW 태깅 시스템

### Impact
- 모든 리뷰 iteration에서 새로운 문제 탐색 보장
- 계획 품질 향상
- Breaking Change: 없음

---

## v2.1.0 - requirement-validator Agent 및 AC Traceability

**Date**: 2025-12-09

### Context
v2.0.0에서 Agents 시스템을 도입했지만, JIRA Acceptance Criteria (AC)와 코드 간 자동 매핑 및 추적 기능이 부족.

### Problem
- AC 추적 부재: "AC#1이 어디에 구현되었는가?" 수동 탐색 필요
- 품질 게이트 부재: 미구현 AC 자동 탐지 불가
- Skill 간 AC 정보 전달 부족

### Decision
requirement-validator Agent 추가 (5번째 Agent):
- Mode 1 (Reverse): 코드 → AC 역추적
- Mode 2 (Pre-validation): 계획서 AC 완전성 검증
- Mode 3 (Post-validation): 구현 후 AC 달성 검증
- Mode 4 (Final): MR 자동 AC 매핑

4개 Skill 통합:
- analyze-issue Phase 3E
- plan-builder Phase 2 Step C-2
- execute-plan Phase 6
- mr-code-review Phase 2-4

### Impact
- AC 누락 자동 탐지 → merge 전 차단
- 전체 워크플로우에서 AC 중심 축 확립
- Breaking Change: 없음

---

## v1.6.0 - plan-builder 피드백 루프 강제력 확보

**Date**: 2025-12-09

### Context
plan-builder의 핵심 원칙인 "계획서 생성 → 리뷰 → 수정 → ... → ZERO 이슈까지 반복"이 제대로 작동하지 않음.

### Problem
- 절차적 지침의 강제력 부족: "Loop back"이 권장사항으로 해석됨
- 승인 기준 불일치: "Approve with Changes" 옵션이 모호성 발생
- 선형 구조로 오해: Phase 1 → 2 → 3 구조가 "한 번씩만 실행"으로 해석

### Decision
WHILE 루프 구조 + Binary Decision:
```markdown
WHILE (total_issues > 0 OR Overall Assessment != "Strong") DO:
  Step A: Review
  Step B: Count Issues
  Step C: Decision Gate
  Step D: Apply Feedback → LOOP BACK TO STEP A
END WHILE
```

Binary Decision Model:
- 이전: Approve / Approve with Changes / Major Revision
- 현재: ✅ Approve / 🔄 Needs Iteration
- ZERO 이슈일 때만 Approve 가능

### Impact
- 최소 2-3회 이상 반복 보장
- 계획 생성 시간 증가 (품질 향상의 trade-off)
- Breaking Change: v1.5.1 → v1.6.0 (Major version bump)

### Pattern
문서 강제력 확보 패턴:
- 서술적 지침 ("you should") → 약한 강제력
- 명시적 구조 ("WHILE", "MANDATORY") → 강한 강제력
