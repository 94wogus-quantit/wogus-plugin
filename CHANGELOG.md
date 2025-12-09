# Changelog

이 프로젝트의 모든 주요 변경사항은 이 파일에 문서화됩니다.

이 형식은 [Keep a Changelog](https://keepachangelog.com/ko/1.0.0/)를 기반으로 하며,
이 프로젝트는 [Semantic Versioning](https://semver.org/lang/ko/)을 따릅니다.

## [Unreleased]

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
