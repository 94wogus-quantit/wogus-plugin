# 아키텍처 결정사항

## 2025-12-10 - v3.0.0 Agents 시스템 축소 리팩토링

### 결정 사항

1. **Agents 축소**: 5개 → 1개
   - 삭제: code-refactorer, test-generator, code-reviewer, performance-analyzer
   - 유지: requirement-validator (유일하게 4개 Skills에서 활발히 사용)

2. **핵심 로직 Skills 통합**:
   - code-refactorer → analyze-issue Phase 3D
   - test-generator → execute-plan Phase 5

3. **Phase 강도 변경**: "선택적" → "조건부 필수"

### 근거

- **Dead Code 분석**: 5개 Agent 중 4개가 Skills에서 미사용 (72%)
- **YAGNI 원칙**: "나중에 필요할 것 같아서" 만든 기능들 제거
- **ROI 평가**: 독립 호출용 Agent는 ROI 낮음

### 영향받는 컴포넌트

- `analyze-issue/SKILL.md`: Phase 3D 강화 (복잡도 분석 + 리팩토링 가이드)
- `execute-plan/SKILL.md`: Phase 5 강화 (AAA 패턴 테스트 생성)
- `.claude-plugin/marketplace.json`: agents 배열 1개로 축소
- `agents/`: 4개 파일 삭제, 1개만 유지

### 주의사항

- **Breaking Change**: 기존 Agent 직접 호출 불가
- **대안 제공**: Skills의 Phase에서 동일 기능 제공
- 버전: v2.4.0 → v3.0.0 (Major bump)

---

## 패턴: Agent → Skill 인라인 통합

### 적용 조건

- Agent가 1개 Skill에서만 사용될 때
- Agent의 핵심 로직이 50줄 이하일 때
- 독립 호출 ROI가 낮을 때

### 적용 방법

1. Agent의 핵심 로직 추출
2. 호출하는 Skill의 Phase에 직접 통합
3. 상세 예제 코드 추가 (AAA 패턴, Sequential Thinking 등)
4. Agent 파일 삭제

### 적용하지 않는 조건

- 여러 Skills에서 공유될 때 (예: requirement-validator)
- 복잡한 상태 관리가 필요할 때
- 외부 MCP 통합이 필수일 때
