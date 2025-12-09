# Changelog

이 프로젝트의 모든 주요 변경사항은 이 파일에 문서화됩니다.

이 형식은 [Keep a Changelog](https://keepachangelog.com/ko/1.0.0/)를 기반으로 하며,
이 프로젝트는 [Semantic Versioning](https://semver.org/lang/ko/)을 따릅니다.

## [Unreleased] - 2025-12-09

### Added

- **mr-code-review skill**: GitLab MR 코드 리뷰 자동화 스킬 추가
  - **6가지 종합 검증**: 아키텍처, 컨벤션, 이슈 패턴, JIRA 요구사항, 보안, 테스트
  - **MCP 기반 심화 분석**: Sequential Thinking + Serena Context7 + Atlassian 적극 활용
  - **3단계 위험도**: 🔴 Critical, 🟡 High, 🟢 Medium
  - **리포트 생성**: MR_CODE_REVIEW.md 자동 생성
  - **개선 제안**: 각 이슈별 위치, 설명, 개선 방법 제공
  - **맥락 기반 분석**: README, CLAUDE.md, Serena memory, JIRA 종합 활용
  - 파일 위치: `mr-code-review/`

- **Verification Guides**: 6개의 상세 검증 가이드 문서
  - `architecture_check.md`: 아키텍처 일관성 검증 프로세스
  - `convention_check.md`: 컨벤션 준수 확인 프로세스
  - `known_issues_check.md`: 알려진 이슈 패턴 대조 프로세스
  - `jira_validation.md`: JIRA 요구사항 검증 프로세스
  - `security_review.md`: 보안 및 품질 리뷰 프로세스 (OWASP Top 10)
  - `test_coverage.md`: 테스트 커버리지 평가 프로세스

### Changed

- **README.md**: Skills → Plugins 개념으로 확장
  - Plugin 정의 추가 (Skills, Custom Commands, MCP Servers 통합)
  - Repository Structure에 mr-code-review 추가
  - 개요 섹션 업데이트: "Personal Skills Collection" → "Personal Claude Code Plugins"

### Technical Details

- **새 Dependencies**: 없음 (기존 MCP 활용)
- **MCP 사용**:
  - Sequential Thinking: 체계적 분석에 필수
  - Serena Context7: 프로젝트 맥락 및 메모리 관리
  - Atlassian: JIRA/Confluence 통합 (선택적)
- **파일 구조**:
  - SKILL.md: 383줄 (목표 500줄 이하 달성)
  - References: 3개 문서 (template, checklist, inline format)
  - Verification Guides: 6개 상세 가이드

### Development Process

이 기능은 다음 워크플로우로 개발되었습니다:

1. **analyze-issue**: 요구사항 분석 (`MR_CODE_REVIEW_REPORT.md`)
2. **plan-builder**: 구현 계획 수립 (`MR_CODE_REVIEW_PLAN.md`, 3차 검토 완료)
3. **execute-plan**: 계획 실행 (5개 주요 태스크 완료)
4. **document**: 문서화 (현재 단계)

### Related Files

- 분석 리포트: `MR_CODE_REVIEW_REPORT.md`
- 구현 계획: `MR_CODE_REVIEW_PLAN.md`
- Skill 패키지: `mr-code-review.zip`

---

## [1.5.1] - 2025-12-XX (이전 버전)

### Added

- **frontend-designer skill**: Storybook MCP 통합
- **plan-builder skill**: 자동 반복 검토 기능
- **document skill**: 워크플로우 아티팩트 통합 문서화

### Changed

- Marketplace 구조 개선
- Serena MCP 적극 활용 정책 강화

---

## 버전 관리 정책

- **Major (X.0.0)**: 호환성이 깨지는 변경
- **Minor (x.Y.0)**: 새로운 기능 추가 (하위 호환)
- **Patch (x.y.Z)**: 버그 수정 및 문서 업데이트
