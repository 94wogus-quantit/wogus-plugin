# Changelog

이 프로젝트의 모든 주요 변경사항은 이 파일에 문서화됩니다.

이 형식은 [Keep a Changelog](https://keepachangelog.com/ko/1.0.0/)를 기반으로 하며,
이 프로젝트는 [Semantic Versioning](https://semver.org/lang/ko/)을 따릅니다.

## [Unreleased]

---

## [2.1.0] - 2025-12-10

### Added

- **requirement-validator Agent (P0)**: JIRA Acceptance Criteria와 코드 자동 매핑 및 검증
  - **4가지 실행 모드**:
    - **Mode 1 (Reverse Tracing)**: 코드 → AC 역매핑 (for analyze-issue)
    - **Mode 2 (Pre-validation)**: 계획 → AC coverage 체크 (for plan-builder)
    - **Mode 3 (Post-validation)**: git diff → AC 구현 확인 (for execute-plan)
    - **Mode 4 (Final Gate)**: MR → AC 최종 검증 (for mr-code-review)
  - **도구**: Serena, Atlassian, Sequential Thinking MCP 통합
  - **출력**: AC 달성률, 미구현 AC 자동 탐지, MR 블로킹 기능
  - 위치: `agents/requirement-validator.md`

### Enhanced

- **analyze-issue skill**: Phase 3E (Requirement Reverse Tracing) 추가
  - 버그와 연관된 JIRA AC 자동 역추적
  - requirement-validator Mode 1 자동 호출
  - 보고서에 "요구사항 추적" 섹션 추가
  - 위치: `analyze-issue/SKILL.md:279-322`

- **plan-builder skill**: Phase 2 Step C-2 (AC Coverage Check) 추가
  - 계획이 모든 JIRA AC를 커버하는지 자동 검증
  - AC Completeness < 100%면 "Needs Iteration" 판정
  - requirement-validator Mode 2 자동 호출
  - STRICT Approval Criteria에 "AC Completeness: 100%" 추가
  - 위치: `plan-builder/SKILL.md:231-273`

- **execute-plan skill**: Phase 6 (AC Achievement Report) 추가
  - 구현 완료 후 AC 달성 여부 자동 검증 및 보고
  - git diff 기반 변경 파일 자동 수집
  - requirement-validator Mode 3 자동 호출
  - 미구현 AC 발견 시 TodoList 자동 추가
  - 위치: `execute-plan/SKILL.md:526-601`

- **mr-code-review skill**: Phase 2-4 (JIRA 요구사항 검증) 자동화
  - 수동 검증 → requirement-validator Mode 4 자동 호출로 교체
  - MR 전체 변경사항 기반 AC 최종 검증
  - 미구현 AC 있을 시 "MR BLOCKED" 판정
  - 코드 품질, 보안, 테스트 품질 통합 검증
  - 위치: `mr-code-review/SKILL.md:201-254`

### Changed

- **marketplace.json**: v2.0.0 → v2.1.0
  - `metadata.version` 업데이트
  - `metadata.description`에 "AC 요구사항 추적" 추가
  - `agents` 배열에 `./agents/requirement-validator.md` 추가
  - 위치: `.claude-plugin/marketplace.json`

### Technical Details

- **아키텍처 확장**: End-to-End AC Traceability
  - 전체 워크플로우(analyze-issue → plan-builder → execute-plan → mr-code-review)에서 JIRA AC 자동 추적
  - AC를 중심 축으로 한 품질 게이트 시스템
  - Graceful Degradation: JIRA/MCP 없어도 정상 작동

- **새 파일**:
  - `agents/requirement-validator.md` (~15KB, 485 lines)

- **수정된 파일**:
  - `.claude-plugin/marketplace.json`: version 2.0.0 → 2.1.0, agent 등록
  - `analyze-issue/SKILL.md`: Phase 3E 추가 (~45 lines)
  - `plan-builder/SKILL.md`: Step C-2 추가 (~45 lines)
  - `execute-plan/SKILL.md`: Phase 6 추가 (~75 lines)
  - `mr-code-review/SKILL.md`: Phase 2-4 자동화 (~55 lines)
  - `README.md`: requirement-validator 섹션 추가 (~35 lines)
  - `CLAUDE.md`: AC Traceability 개념 설명 추가 (~45 lines)

- **검증 지표**:
  - requirement-validator.md YAML frontmatter 검증 통과 ✅
  - 4가지 모드 모두 구현 완료 ✅
  - 4개 Skill 통합 완료 (기존 기능 회귀 테스트 통과) ✅
  - Sequential Thinking 예제 10개 이상 포함 ✅
  - Backward Compatibility 보장 (JIRA 없어도 동작) ✅

### Development Process

이 기능은 다음 워크플로우로 개발되었습니다:

1. **analyze-issue**: AC 추적 Gap 분석 (`REQUIREMENT_VALIDATOR_ANALYSIS_REPORT.md`)
2. **plan-builder**: requirement-validator 구현 계획 수립 (`REQUIREMENT_VALIDATOR_AGENT_PLAN.md`, 2차 검토 완료)
3. **execute-plan**: 9개 태스크 완료 (Phase 0-3)
4. **document**: 문서화 (현재 단계)

### Migration Guide

**기존 사용자 (v2.0.0 → v2.1.0)**:

1. **마켓플레이스 갱신**:
   ```bash
   /marketplace refresh
   ```

2. **자동 업그레이드**:
   - requirement-validator Agent 자동 포함 (별도 설치 불필요)
   - 기존 Skills는 확장된 기능으로 자동 업데이트

3. **새 기능 활용**:
   - **JIRA 이슈 연결 시**: 전체 워크플로우에서 AC 자동 추적
     - analyze-issue → AC 역추적 (Phase 3E)
     - plan-builder → AC coverage 체크 (Step C-2, 100% 강제)
     - execute-plan → AC 달성 보고 (Phase 6)
     - mr-code-review → AC 최종 게이트 (Phase 2-4, MR 블로킹 가능)
   - **JIRA 없을 시**: 기존과 동일하게 작동 (Graceful Degradation)

4. **호환성**:
   - ✅ 완전 하위 호환 (Breaking Change 없음)
   - ✅ JIRA/Atlassian MCP 선택적 (없어도 정상 작동)
   - ✅ 기존 워크플로우 영향 없음

**수동 호출 예시**:
```bash
# Mode 1: 코드 → AC 역추적
"requirement-validator agent로 UserService.ts의 login 함수가 어떤 AC와 관련있는지 찾아줘"

# Mode 2: 계획 → AC coverage 체크
"requirement-validator agent Mode 2로 FEATURE_PLAN.md의 AC coverage 체크해줘"

# Mode 3: git diff → AC 달성률
"requirement-validator agent Mode 3로 현재 git diff 기준 AC 달성률 보고해줘"

# Mode 4: MR → AC 최종 검증
"requirement-validator agent Mode 4로 이 MR이 JIRA-123 AC를 모두 달성했는지 확인해줘"
```

### Related Files

- 분석 리포트: `REQUIREMENT_VALIDATOR_ANALYSIS_REPORT.md`
- 구현 계획: `REQUIREMENT_VALIDATOR_AGENT_PLAN.md`
- 계획 리뷰: `REQUIREMENT_VALIDATOR_AGENT_PLAN_REVIEW_v1.md`, `REQUIREMENT_VALIDATOR_AGENT_PLAN_REVIEW_v2.md`

---

## [2.0.0] - 2025-12-09

### ⚠️ Breaking Changes

**없음**: v2.0.0은 기능 추가 중심이며, 기존 Skills는 하위 호환됩니다.
- 기존 analyze-issue, plan-builder, execute-plan, document, mr-code-review → 정상 작동
- 새로운 Agents는 추가 기능이며 기존 워크플로우를 방해하지 않음
- 버전 Major bump 이유: **새로운 아키텍처 컨셉 도입** (Skills + Agents)

### Added

- **Agents System (NEW)**: 특정 기술 작업 자동화를 위한 Agent 4개 추가
  - **code-refactorer** (P0): 복잡한 레거시 코드 자동 리팩토링
    - Cyclomatic complexity > 10, 함수 길이 > 50줄 탐지
    - Extract Method, Extract Class, Magic Number 상수화 자동 적용
    - SRP 위반 탐지 및 책임 분리
    - 위치: `agents/code-refactorer.md`

  - **test-generator** (P0): 포괄적인 단위 테스트 자동 생성
    - Happy path, Edge cases, Error handling 완전 커버
    - 프로젝트 기존 테스트 패턴 학습 (Jest, pytest)
    - AAA 패턴 (Arrange-Act-Assert) 준수
    - 위치: `agents/test-generator.md`

  - **performance-analyzer** (P2): 성능 병목 지점 자동 탐지
    - N+1 Query 패턴 탐지 (Loop + await)
    - React re-render 최적화 (useMemo, useCallback)
    - Bundle size 분석 (임계값: 500KB)
    - 위치: `agents/performance-analyzer.md`

  - **code-reviewer** (P3): 자동 코드 품질 리뷰
    - SOLID 원칙 위반 탐지 (SRP, OCP, LSP, ISP, DIP)
    - Code Smell 탐지 (Long Method, Large Class, Duplicate Code)
    - 네이밍 규칙 검증 (Magic Number, 헝가리안 표기법)
    - 위치: `agents/code-reviewer.md`

- **plugin.json**: Skills + Agents 통합 매니페스트 파일
  - `"agents": "./agents"` 필드로 Agents 디렉토리 지정
  - `"version": "2.0.0"` 버전 정보
  - 위치: `.claude-plugin/plugin.json`

- **agents/ 디렉토리**: Agent 정의 파일 저장소
  - 4개 Agent .md 파일 포함
  - YAML frontmatter 형식 (name, description, tools, model)

### Changed

- **marketplace.json**: v1.6.0 → v2.0.0
  - `metadata.version` 업데이트
  - `metadata.description`에 "+ Agents" 추가
  - 위치: `.claude-plugin/marketplace.json`

### Enhanced

- **analyze-issue skill**: Phase 3D (Code Complexity Assessment) 추가
  - 영향받는 파일의 복잡도 자동 분석
  - Cyclomatic complexity, 함수 길이, SRP 위반 탐지
  - code-refactorer Agent 권장 메시지 자동 생성
  - 위치: `analyze-issue/SKILL.md:211-278`

- **execute-plan skill**: 6-Phase → **7-Phase** 구조로 확장
  - **Phase 4C**: Database Migration Validation (선택적)
    - 위험한 마이그레이션 패턴 자동 차단 (NOT NULL without DEFAULT, DROP)
    - 성능 문제 패턴 탐지 (ALTER TYPE, non-concurrent INDEX)
    - CRITICAL 위험 시 실행 차단
  - **Phase 5**: Automated Test Generation (선택적)
    - 테스트 누락 파일 자동 탐지
    - test-generator Agent 자동 호출
    - 커버리지 측정 및 보고
  - Phase 6: Testing and Verification (기존 Phase 5)
  - Phase 7: Documentation Updates (기존 Phase 5)
  - 위치: `execute-plan/SKILL.md:283-522`

- **mr-code-review skill**: Phase 4 (Dependency Security Analysis) 추가
  - npm audit 자동 실행
  - CRITICAL/HIGH 취약점 필터링 (jq 활용)
  - Sequential Thinking으로 취약점 분석
  - CVE 추적 및 영향 평가
  - 위치: `mr-code-review/SKILL.md:259-342`

### Technical Details

- **아키텍처 변경**: Skills + Agents 분리
  - **Skills**: 워크플로우 오케스트레이션 (6-9 phases, 복잡도 높음)
  - **Agents**: 기술 작업 자동화 (4-5 phases, 단일 책임)
  - **통합 방식**: Skills가 필요 시 Agents 자동 호출 (Composition over Inheritance)

- **새 파일**:
  - `.claude-plugin/plugin.json` (132 bytes)
  - `agents/code-refactorer.md` (~8KB)
  - `agents/test-generator.md` (~6KB)
  - `agents/performance-analyzer.md` (~7KB)
  - `agents/code-reviewer.md` (~6KB)

- **수정된 파일**:
  - `.claude-plugin/marketplace.json`: version 1.6.0 → 2.0.0
  - `analyze-issue/SKILL.md`: Phase 3D 추가 (~70 lines)
  - `execute-plan/SKILL.md`: Phase 4C, Phase 5 추가, 6→7 Phase 구조 (~240 lines)
  - `mr-code-review/SKILL.md`: Phase 4 추가 (~80 lines)
  - `README.md`: Agents 섹션 추가, v2.0.0 변경사항 반영 (~140 lines)
  - `CLAUDE.md`: Agent Development 가이드 추가 (~150 lines)

- **검증 지표**:
  - plugin.json JSON valid ✅
  - 4개 Agent YAML frontmatter 검증 통과 ✅
  - Phase numbering 일관성 (execute-plan 7-Phase) ✅
  - README/CLAUDE.md v2.0.0 언급 15회+ ✅

### Development Process

이 기능은 다음 워크플로우로 개발되었습니다:

1. **analyze-issue**: Gap 분석 (`AGENT_INTEGRATION_ANALYSIS_REPORT.md`)
2. **plan-builder**: v2.0.0 구현 계획 수립 (`WORKFLOW_SKILLS_V2_PLAN.md`)
3. **execute-plan**: 14개 태스크 완료 (Phase 0-5)
4. **document**: 문서화 (현재 단계)

### Migration Guide

**기존 사용자 (v1.6.0 → v2.0.0)**:

1. **마켓플레이스 갱신**:
   ```bash
   /marketplace refresh
   ```

2. **자동 업그레이드**:
   - Skills는 기존과 동일하게 작동
   - Agents는 자동으로 포함됨 (별도 설치 불필요)

3. **새 기능 활용**:
   - analyze-issue 실행 시 Phase 3D에서 code-refactorer 권장 자동 확인
   - execute-plan 실행 시 Phase 5에서 test-generator 자동 호출
   - mr-code-review 실행 시 Phase 4에서 의존성 보안 체크 자동 실행

4. **호환성**:
   - ✅ 기존 워크플로우 영향 없음
   - ✅ 기존 명령어 그대로 작동
   - ✅ 기존 리포트/계획 파일 형식 동일

**새 Agent 직접 사용**:
```bash
# Agent는 Skills에서 자동 호출되지만, 독립 실행도 가능:
"code-refactorer agent로 src/utils/payment.ts 리팩토링해줘"
"performance-analyzer agent로 성능 분석해줘"
"PR 올리기 전에 code-reviewer agent로 검토해줘"
```

### Related Files

- 분석 리포트: `AGENT_INTEGRATION_ANALYSIS_REPORT_v2.md`
- 구현 계획: `WORKFLOW_SKILLS_V2_PLAN.md`
- 아키텍처 결정사항: `CLAUDE.md` (2025-12-09 섹션)

---

## [1.6.0] - 2025-12-09

### ⚠️ Breaking Changes

- **plan-builder skill**: 피드백 루프가 더 엄격해졌습니다
  - 이전: 1-2회 반복 후 "Good" 평가로 조기 종료 가능
  - 현재: **최소 2-3회 이상 반복**, **ZERO 이슈**가 될 때까지 강제 반복
  - 영향: 계획 생성에 더 많은 시간이 소요되지만 품질이 크게 향상됨
  - 업그레이드 시 주의사항: 더 엄격한 품질 기준을 기대하세요

### Changed

- **plan-builder skill**: Phase 2를 명시적 WHILE 루프 구조로 완전 재작성
  - Phase 2A → Step A (Review), Phase 2B → Step B (Count Issues)
  - Phase 2C → Step C (Decision Gate), Step D (Apply Feedback + Loop Back)
  - Loop Entry Condition, Loop Body, Loop Exit Condition 명확히 정의
  - Iteration Status Report 템플릿 추가 (각 반복 후 출력 강제)

- **plan-builder skill**: "Approve with Changes" 옵션 제거
  - 이전: Approve / **Approve with Changes** / Major Revision
  - 현재: ✅ **Approve** / 🔄 **Needs Iteration** (Binary decision)
  - SKILL.md와 review_checklist.md 승인 기준 통일
  - 모호한 "minor changes" 개념 제거

### Added

- **plan-builder skill**: 반복 추적 메커니즘
  - 리뷰 파일 버전 번호 추적: `*_PLAN_REVIEW_v[N].md`
  - 이전: 즉시 삭제 → 현재: 버전별 보존
  - Phase 3에서 아카이브/삭제 옵션 제공
  - Iteration Transition Message 템플릿 추가

- **plan-builder skill**: Phase 3 리뷰 파일 정리 로직
  - Option A: Archive (audit trail 보존)
  - Option B: Delete (clean workspace)
  - Review Iterations History 템플릿 추가

- **plan-builder skill**: WHILE 루프 시각화 다이어그램
  - ASCII 다이어그램으로 루프 구조 명확화
  - Step A, B, C, D 순환 흐름 표시
  - "LOOP BACK TO STEP A" 명시적 표현

- **plan-builder skill**: 테스트 시나리오 문서
  - `plan-builder/tests/loop_verification.md` 생성
  - 5개 테스트 케이스: Minimum Iterations, Issue Counting, Loop Continuation, Proper Exit, Version Tracking
  - Given/Expected/Fail Condition 형식 준수

### Fixed

- **plan-builder skill**: 피드백 루프 조기 종료 문제 해결
  - 근본 원인: 절차적 지침의 강제력 부족
  - 해결: 명시적 WHILE 루프 구조 + Binary decision
  - 결과: ZERO 이슈까지 강제 반복 보장

- **plan-builder skill**: SKILL.md와 review_checklist.md 승인 기준 불일치 해결
  - 이전: "Approve" (SKILL.md) vs "Approve with Changes" (review_checklist.md)
  - 현재: 양쪽 모두 "Approve / Needs Iteration" 통일
  - 관련 파일: [plan-builder/SKILL.md](plan-builder/SKILL.md), [plan-builder/references/review_checklist.md](plan-builder/references/review_checklist.md)

### Technical Details

- **버전 업데이트**: v1.5.1 → v1.6.0 (Breaking Change)
- **수정된 파일**:
  - `plan-builder/SKILL.md`: Phase 2 완전 재작성 (~300 lines)
  - `plan-builder/references/review_checklist.md`: 승인 기준 섹션 재작성 (~70 lines)
  - `plan-builder/tests/loop_verification.md`: 새 파일 생성 (6.4KB)
  - `.claude-plugin/marketplace.json`: 버전 1.5.1 → 1.6.0
- **검증 지표**:
  - LOOP 키워드: 13개 (≥ 5 요구사항 충족)
  - "Approve with Changes" 완전 제거 (REMOVED 섹션만 남음)
  - 반복 추적: "KEEP" 2회, "MANDATORY" 1회
  - 테스트 케이스: 5개 작성 완료

### Development Process

이 기능은 다음 워크플로우로 개발되었습니다:

1. **analyze-issue**: 피드백 루프 미작동 원인 분석 (`PLAN_BUILDER_FEEDBACK_LOOP_REPORT.md`)
2. **plan-builder**: 수정 계획 수립 (`PLAN_BUILDER_FEEDBACK_LOOP_FIX_PLAN.md`, 2차 검토 완료)
3. **execute-plan**: 계획 실행 (8개 태스크 완료, 100% 성공)
4. **document**: 문서화 (현재 단계)

### Related Files

- 분석 리포트: `PLAN_BUILDER_FEEDBACK_LOOP_REPORT.md`
- 구현 계획: `PLAN_BUILDER_FEEDBACK_LOOP_FIX_PLAN.md`
- 테스트 시나리오: `plan-builder/tests/loop_verification.md`

---

## [1.5.1] - 2025-12-XX

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
