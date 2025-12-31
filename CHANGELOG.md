# Changelog

이 프로젝트의 모든 주요 변경사항은 이 파일에 문서화됩니다.

이 형식은 [Keep a Changelog](https://keepachangelog.com/ko/1.0.0/)를 기반으로 하며,
이 프로젝트는 [Semantic Versioning](https://semver.org/lang/ko/)을 따릅니다.

## 아카이브

이전 버전의 변경 이력은 다음 파일에서 확인할 수 있습니다:
- [v3.0.0 - v3.4.1](changelogs/v3.x.md)
- [v2.0.0 - v2.4.0](changelogs/v2.x.md)
- [v1.x](changelogs/v1.x.md)

---

## [3.9.0] - 2025-12-31

### Added

- **slack 플러그인**: Slack 메시지 검색, 히스토리, 스레드 조회 MCP 서버
  - 패키지: `slack-mcp-server` (korotovsky)
  - 환경변수: `SLACK_BOT_TOKEN`
  - 기능: conversations_history, conversations_replies, conversations_search_messages, channels_list
  - 실행: `npx -y slack-mcp-server@latest --transport stdio`

### Technical Details

- **새로운 플러그인**:
  ```json
  {
    "name": "slack",
    "description": "Slack 메시지 검색, 히스토리, 스레드 조회 MCP 서버",
    "mcpServers": {
      "slack": {
        "command": "npx",
        "args": ["-y", "slack-mcp-server@latest", "--transport", "stdio"],
        "env": { "SLACK_MCP_XOXB_TOKEN": "${SLACK_BOT_TOKEN:-}" }
      }
    }
  }
  ```

- **수정된 파일**:
  - `.claude-plugin/marketplace.json`: slack 플러그인 추가, version 3.8.0 → 3.9.0
  - `README.md`: 설치 방법, 환경변수 문서 업데이트
  - `CLAUDE.md`: 플러그인 수 업데이트 (3개 → 4개)

---

## [3.7.0] - 2025-12-19

### Changed

- **Plugins 모듈화**: 단일 플러그인(`workflow-skills`)을 3개 독립 플러그인으로 분리
  - `workflow-bundle`: 5개 skills + 1개 agent + sequential-thinking MCP
  - `terraform`: Terraform MCP 서버만 포함
  - `amplitude`: Amplitude MCP 서버만 포함
  - 사용자가 필요한 기능만 선택적으로 설치 가능

- **외부 MCP 분리**: serena, context7, sentry, atlassian MCP는 별도 플러그인으로 설치하도록 변경
  - marketplace.json에서 제거
  - 외부 플러그인 의존성 명시화

### Removed

- **mcp-config 스킬 제거**: 플러그인 분리로 개별 설치/제거가 가능해져 불필요
  - `mcp-config/SKILL.md` 삭제
  - `mcp-config/references/mcp_tools.md` 삭제
  - `mcp-config/references/settings_template.json` 삭제

- **chrome-devtools MCP 제거**: claude-in-chrome 브라우저 자동화 도구와 중복

### Technical Details

- **삭제된 파일**:
  - `mcp-config/SKILL.md` (746 lines)
  - `mcp-config/references/mcp_tools.md` (519 lines)
  - `mcp-config/references/settings_template.json` (9 lines)

- **수정된 파일**:
  - `.claude-plugin/marketplace.json`: 3개 plugins 구조로 재설계
  - `README.md`: 설치 방법, 구조 설명 업데이트
  - `CLAUDE.md`: ADR v3.7.0 추가

- **새로운 Plugins 구조**:
  ```json
  {
    "plugins": [
      { "name": "workflow-bundle", "skills": [...], "agents": [...], "mcpServers": {...} },
      { "name": "terraform", "mcpServers": {...} },
      { "name": "amplitude", "mcpServers": {...} }
    ]
  }
  ```

---

## [3.6.0] - 2025-12-12

### Added

- **mr-code-review: 비즈니스 로직 정확성 검증 추가**: 7가지 종합 검증으로 확장
  - JIRA 목표 대비 구현 정확성 검증 (잘못된 로직, 엣지케이스 누락 탐지)
  - 경계값 처리, 부정 케이스, 데이터 정합성, 상태 전이, 시간 조건 검증
  - `references/verification_guides/business_logic_check.md` 추가
  - 검증 항목: 6가지 → 7가지

- **mr-code-review: 2개 파일 출력으로 변경**
  - **이전**: `MR_CODE_REVIEW.md` 단일 파일
  - **이후**: `INLINE_DISCUSSION.json` + `SUMMARY_COMMENT.md`
  - `INLINE_DISCUSSION.json`: GitLab Inline Discussion 자동화용 JSON
  - `SUMMARY_COMMENT.md`: 전체 요약 마크다운

- **mr-code-review: Phase별 중간 산출물 저장**
  - Context 손실 방지를 위해 각 Phase 결과를 파일로 저장
  - `.mr-review/1_CONTEXT.md`: 맥락 수집 결과
  - `.mr-review/2_CODE_ANALYSIS.md`: 코드 분석 결과
  - `.mr-review/3_SECURITY_ANALYSIS.md`: 보안 분석 결과
  - Phase 4에서 중간 파일들을 읽어 최종 리포트 생성

- **mr-code-review: 범용 보안 스캔 도구 Trivy 도입**
  - 언어별 도구(npm audit, pip-audit 등) 대신 Trivy로 통합
  - JavaScript, Python, Go, Java, Ruby, Rust, PHP, .NET 등 모든 언어 지원
  - 대안 도구 안내: Snyk, Grype, OSV-Scanner

### Changed

- **mr-code-review: Phase 순서 변경**
  - Phase 3: Dependency Security Analysis (보안 분석 먼저)
  - Phase 4: Report Generation (리포트 생성 마지막)
  - 보안 분석 결과를 리포트에 정확히 반영

- **CHANGELOG 구조 개편**
  - 메인 CHANGELOG.md는 최신 버전(v3.5.0+)만 유지
  - 이전 버전은 `changelogs/` 디렉토리로 분리
  - v3.0-v3.4, v2.x, v1.x 별도 아카이브 파일

### Removed

- `mr-code-review/references/review_template.md`: 새 템플릿으로 대체됨

### Technical Details

- **새로운 파일**:
  - `mr-code-review/references/inline_discussion_template.json`
  - `mr-code-review/references/summary_comment_template.md`
  - `mr-code-review/references/verification_guides/business_logic_check.md`
  - `changelogs/v3.0-v3.4.md`
  - `changelogs/v2.x.md`
  - `changelogs/v1.x.md`

- **수정된 파일**:
  - `mr-code-review/SKILL.md`: 7가지 검증, 4-Phase 워크플로우, 중간 산출물
  - `.claude-plugin/marketplace.json`: version 3.5.3 → 3.6.0

- **중간 산출물 워크플로우**:
  ```
  Phase 1 → .mr-review/1_CONTEXT.md
  Phase 2 → .mr-review/2_CODE_ANALYSIS.md
  Phase 3 → .mr-review/3_SECURITY_ANALYSIS.md
  Phase 4 → INLINE_DISCUSSION.json + SUMMARY_COMMENT.md
  ```

---

## [3.5.3] - 2025-12-12

### Added

- **mr-code-review: 브랜치 목표 섹션 추가**: MR 리뷰 시 코드 품질뿐 아니라 "브랜치가 원래 목표를 달성했는지" 확인
  - **Phase 1 개선**: "브랜치 목표 파악 (Branch Objective)" 단계를 최우선 작업으로 추가
    - 브랜치명/MR description에서 JIRA 이슈 자동 추출
    - JIRA 이슈 상세 조회 후 AC 추출
    - Sequential Thinking으로 목표 분석
  - **리포트 템플릿 개선**: `🎯 브랜치 목표 (Branch Objective)` 섹션을 리포트 최상단에 배치
    - JIRA 이슈 정보 테이블 (이슈 키, 제목, 유형, 스프린트, Parent Epic)
    - AC 달성 현황 테이블: 각 AC별 달성 여부 + 관련 코드 위치
    - 목표 달성 요약: 달성률 퍼센트로 한눈에 파악
    - 미구현 AC 있을 시 merge 비권장 경고

### Technical Details

- **수정된 파일**:
  - `mr-code-review/SKILL.md:164-227`: Phase 1에 Branch Objective 단계 추가
  - `mr-code-review/references/review_template.md:16-59`: 브랜치 목표 섹션 추가
- **새로운 프로세스**:
  - 1-1. 브랜치명에서 JIRA 이슈 추출
  - 1-2. MR description에서 JIRA 이슈 추출
  - 1-3. JIRA 이슈 상세 조회 (Atlassian MCP)
  - 1-4. 브랜치 목표 정리 (Sequential Thinking)
  - 1-5. 브랜치 목표 요약 작성

---

## [3.5.2] - 2025-12-12

### Changed

- **atlassian MCP: Docker → uvx 전환**: 더 가볍고 빠른 실행 환경으로 변경
  - `docker run ghcr.io/sooperset/mcp-atlassian` → `uvx mcp-atlassian`
  - 환경변수 방식 유지 (`env` 블록)
  - Docker 의존성 제거로 설치/실행 간소화

### Technical Details

- **수정된 파일**:
  - `.claude-plugin/marketplace.json`: atlassian MCP 설정 변경
  - `mcp-config/references/mcp_tools.md`: 환경 변수 문서 업데이트
- **환경 변수 매핑**:
  - `ATLASSIAN_URL` → `JIRA_URL`, `CONFLUENCE_URL`
  - `ATLASSIAN_USERNAME` → `JIRA_USERNAME`, `CONFLUENCE_USERNAME`
  - `ATLASSIAN_API_TOKEN` → `JIRA_API_TOKEN`, `CONFLUENCE_API_TOKEN`

---

## [3.5.1] - 2025-12-12

### Fixed

- **mr-code-review 한국어 출력 정책 누락 수정**: 코드 리뷰 결과가 영어로 출력되던 문제 해결
  - `CRITICAL LANGUAGE POLICY` 섹션 추가 (`mr-code-review/SKILL.md:8-24`)
  - MR_CODE_REVIEW.md 리포트 한국어 작성 강제
  - 이슈 설명, 개선 제안, 분석 코멘트 한국어로 출력
  - 다른 스킬들(analyze-issue, plan-builder, execute-plan, document)과 동일한 언어 정책 적용

### Technical Details

- **수정된 파일**: `mr-code-review/SKILL.md`
- **추가된 섹션**: `## ⚠️ CRITICAL LANGUAGE POLICY` (18 lines)
- **언어 정책 항목**:
  - ✅ MR_CODE_REVIEW.md: 한국어 작성
  - ✅ Issue descriptions: 한국어 작성
  - ✅ Improvement suggestions: 한국어 작성
  - ✅ Analysis comments: 한국어 작성
  - ✅ User communication: 한국어 응답

---

## [3.5.0] - 2025-12-11

### Changed

- **Worktree 기능 제거 → 브랜치 검증으로 단순화**: 4개 스킬의 Phase 0 완전 재설계
  - **이전**: Git Worktree 자동 생성/관리/삭제
  - **이후**: 보호된 브랜치 (main/master/staging) 검증
  - 복잡한 Worktree 로직 500+ 라인 제거
  - 브랜치 보호 강화 (main/master/staging 모두 보호)

### Removed

- **analyze-issue Phase 0**: Worktree 자동 생성 로직 제거
- **document Phase 9**: Worktree 정리 (삭제/아카이브) 로직 제거
- **Git commit 강제 로직**: Phase 6 직후 자동 커밋 제거

### Added

- **브랜치 검증 강화**: main/master/staging 3개 브랜치 보호
  - **analyze-issue**: 보호된 브랜치 감지 시 새 feature 브랜치 자동 생성
  - **plan-builder**: 보호된 브랜치 경고 + 권장 워크플로우 안내
  - **execute-plan**: 보호된 브랜치 경고 (코드 수정 위험 강조)
  - **document**: 보호된 브랜치 경고 (문서 커밋 위험)

### Fixed

- **사용자 혼란 해소**: Worktree 디렉토리 구조가 낯설고 복잡하던 문제 해결
- **워크플로우 단순화**: 익숙한 브랜치 워크플로우로 회귀
- **디버깅 용이성**: Worktree 관련 복잡한 에러 제거

### Technical Details

- **브랜치 검증 로직**:
  ```bash
  # main, master, staging 브랜치인지 확인
  if [[ "$CURRENT_BRANCH" == "main" ]] || [[ "$CURRENT_BRANCH" == "master" ]] || [[ "$CURRENT_BRANCH" == "staging" ]]; then
    echo "⚠️ 경고: $CURRENT_BRANCH 브랜치에서 작업 중입니다!"
    echo "⚠️ main/master/staging 브랜치에서는 작업할 수 없습니다."
  fi
  ```

- **유지된 기능**:
  - CRITICAL 강제 블록 (Phase 0 건너뛰기 방지)
  - Git commit/push 옵션 (document Phase 9D)
  - 브랜치 자동 생성 (analyze-issue)

- **수정된 파일**:
  - `analyze-issue/SKILL.md`: Phase 0 브랜치 검증으로 변경 (80 lines)
  - `plan-builder/SKILL.md`: Phase 0 브랜치 검증으로 변경 (50 lines)
  - `execute-plan/SKILL.md`: Phase 0 브랜치 검증으로 변경 (50 lines)
  - `document/SKILL.md`: Phase 0, Phase 9 브랜치 검증으로 변경 (100+ lines)
  - `CLAUDE.md`: ADR v3.5.0 추가, v3.4.0/v3.4.1 제거
  - `.claude-plugin/marketplace.json`: version 3.4.1 → 3.5.0

- **Breaking Change**: Worktree 의존 워크플로우는 영향받음 (소수 사용자)
  - Worktree 자동 생성 기능 사용 중이던 사용자는 수동으로 브랜치 관리 필요
  - 대부분 사용자는 브랜치 워크플로우를 이미 사용 중이므로 영향 최소화

---
