# 알려진 이슈 및 해결 방법

## 2025-12-10 - Agents 오버엔지니어링

### 이슈 설명

v2.0.0에서 도입된 Agents 시스템(5개)이 Skills에서 적극적으로 활용되지 않음.
분석 결과 72%가 dead code로 판명.

### 근본 원인

1. **YAGNI 원칙 위반**: "나중에 필요할 것 같아서" 만든 기능들
2. **약한 문서 표현**: "권장", "선택적" 등 강제력 없음
3. **호출 메커니즘 미명시**: Task tool 사용법 없음
4. **기능 중복**: mr-code-review ≈ code-reviewer

### 해결 방법

v3.0.0에서 다음과 같이 해결:

1. **미사용 Agent 삭제** (2개):
   - code-reviewer.md
   - performance-analyzer.md

2. **Skills에 통합** (2개):
   - code-refactorer → analyze-issue Phase 3D
   - test-generator → execute-plan Phase 5

3. **유지** (1개):
   - requirement-validator (4개 Skills에서 활발히 사용)

### 재발 방지

**Agent 추가 전 체크리스트**:
- [ ] 여러 Skills에서 공유되는가?
- [ ] 독립 호출 ROI가 충분한가?
- [ ] Skills Phase로 통합 불가능한가?

**결정 기준**:
- 2개 이상 Skills에서 사용 → Agent 유지
- 1개 Skills에서만 사용 → Phase에 통합
- 독립 호출만 → 삭제 또는 별도 Skill로 전환

### 관련 코드

- `AGENTS_OVERENGINEERING_REPORT.md` - 분석 리포트
- `AGENTS_REFACTOR_PLAN.md` - 리팩토링 계획
- `CLAUDE.md:409-467` - v3.0.0 아키텍처 결정사항
