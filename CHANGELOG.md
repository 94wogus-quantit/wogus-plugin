# Changelog

이 프로젝트의 모든 주요 변경사항은 이 파일에 문서화됩니다.

이 형식은 [Keep a Changelog](https://keepachangelog.com/ko/1.0.0/)를 기반으로 하며,
이 프로젝트는 [Semantic Versioning](https://semver.org/lang/ko/)을 따릅니다.

---

## [3.4.1] - 2025-12-11

### Changed

- **Phase 0 강제 실행 보장**: 4개 스킬의 Phase 0에 CRITICAL 강제 블록 추가
  - **analyze-issue**: Phase 0 시작 부분에 "⚠️ CRITICAL: DO NOT SKIP PHASE 0" 블록 추가
  - **plan-builder**: Phase 0 시작 부분에 CRITICAL 블록 추가
  - **execute-plan**: Phase 0 시작 부분에 CRITICAL 블록 추가
  - **document**: Phase 0 시작 부분에 CRITICAL 블록 추가
- **문서 강제력 확보 패턴 재적용**: plan-builder v1.6.0의 MANDATORY 패턴을 Phase 0에 적용
  - 서술적 지침 ("Objective", "Steps") → 명시적 명령 ("MUST", "DO NOT", "NEVER")
  - bash 코드 예시로 오해하던 문제 해결
  - Phase 전환 명확화: "Phase 0를 완료하지 않고는 Phase 1 시작 불가"

### Fixed

- **Phase 0 건너뛰기 문제 해결**: Claude가 Phase 0를 건너뛰고 바로 Phase 1을 실행하던 문제 수정
  - v3.4.0에서 Phase 0를 추가했지만 강제력이 약해 실제로 실행되지 않던 문제
  - 명시적 금지 표현으로 Phase 0 실행 보장

### Technical Details

- **CRITICAL 블록 구조**:
  ```markdown
  ⚠️ **CRITICAL: DO NOT SKIP PHASE 0**

  > **MANDATORY REQUIREMENT**:
  > - Phase 0 is the **FIRST step** of this skill
  > - You **MUST** execute Phase 0 **BEFORE** proceeding to Phase 1
  > - **DO NOT** assume you are in the correct environment
  > - **ALWAYS** verify worktree status explicitly
  > - **NEVER** start [next phase] without completing Phase 0
  ```

- **Skill별 강조 메시지**:
  - analyze-issue: "NEVER start analysis (Phase 1) without completing Phase 0"
  - plan-builder: "NEVER start plan creation (Phase 1) without completing Phase 0"
  - execute-plan: "NEVER start code modification (Phase 1) without completing Phase 0"
  - document: "NEVER start documentation (Phase 1) without completing Phase 0"

- **수정된 파일**:
  - `analyze-issue/SKILL.md`: Phase 0에 CRITICAL 블록 추가 (14 lines)
  - `plan-builder/SKILL.md`: Phase 0에 CRITICAL 블록 추가 (14 lines)
  - `execute-plan/SKILL.md`: Phase 0에 CRITICAL 블록 추가 (14 lines)
  - `document/SKILL.md`: Phase 0에 CRITICAL 블록 추가 (14 lines)
  - `CLAUDE.md`: ADR v3.4.1 추가 (상세한 문제점, 결정, 영향 문서화)
  - `.claude-plugin/marketplace.json`: version 3.4.0 → 3.4.1

- **패턴**: 문서 강제력 확보 패턴 (재적용)
  - 이 패턴은 plan-builder의 "Feedback Loop Until Perfect" (v1.6.0)에서 검증됨
  - 서술적 지침은 Claude에게 "권장사항"으로 해석되어 강제력 부족
  - 명시적 구조 + 구체적 명령으로 강제력 확보

- **Breaking Change**: 없음 (기존 로직 유지, 표현 강화만)

---

## [3.4.0] - 2025-12-11

### Added

- **Git Worktree 지원**: 4개 Skills에 Phase 0 추가
  - **analyze-issue**: Worktree 자동 생성 및 이동 (Phase 0)
  - **plan-builder**: Worktree 확인 및 권장 메시지 (Phase 0)
  - **execute-plan**: Worktree 확인 및 브랜치 충돌 경고 (Phase 0)
  - **document**: Worktree 확인 (Phase 0) + 정리 옵션 제공 (Phase 9)
- **병렬 작업 지원**: 여러 JIRA 작업 동시 진행 가능
- **자동화**: Worktree 생성/삭제 자동화로 수동 명령어 불필요
- **Testing Guide**: WORKTREE_INTEGRATION_TESTING_GUIDE.md 추가 (수동 테스트 가이드)

### Changed

- **analyze-issue**: Phase 6 끝에 Git 커밋 로직 추가 (REPORT 파일 자동 커밋)
- **plan-builder**: Phase 3 끝에 Git 커밋 로직 추가 (PLAN 파일 자동 커밋)
- **document**: Phase 9에 Git 커밋/푸시 확인 및 Worktree 정리 로직 추가 (9D, 9E)
- **execute-plan**: Phase count 7-phase → 8-phase (Phase 0 추가)
- **document**: Phase count 9-Phase → 10-Phase (Phase 0 추가)

### Technical Details

- **Worktree 생명 주기**:
  - 생성: analyze-issue Phase 0에서 자동 생성 (`../worktrees/[branch-name]`)
  - 사용: plan-builder, execute-plan에서 검증 및 경고
  - 정리: document Phase 9D/9E에서 Git 커밋/푸시 + 삭제 옵션
  - 이름 규칙: `../worktrees/feature/JIRA-123`

- **감지 로직**: `.git`이 파일이면 worktree, 디렉터리면 main repo
- **성능 개선**: 브랜치 전환 10분 → worktree 전환 5초
- **Breaking Change**: 없음 (Phase 0 추가는 기존 로직에 영향 없음)

- **수정된 파일**:
  - `analyze-issue/SKILL.md`: Phase 0 추가 (93 lines), Phase 6 Git 커밋 (13 lines)
  - `plan-builder/SKILL.md`: Phase 0 추가 (59 lines), Phase 3 Git 커밋 (9 lines)
  - `execute-plan/SKILL.md`: Phase 0 추가 (35 lines), overview 업데이트
  - `document/SKILL.md`: Phase 0 추가 (28 lines), Phase 9D/9E 추가 (100 lines)
  - `CLAUDE.md`: ADR v3.4.0 추가
  - `README.md`: "🔧 Git Worktree 지원" 섹션 추가
  - `WORKTREE_INTEGRATION_TESTING_GUIDE.md`: 수동 테스트 가이드 생성

---

## [3.3.2] - 2025-12-11

### Changed

- **mcp-config skill의 전역 설정 파일 수정 금지 경고 대폭 강화**
  - 최상단에 "CRITICAL WARNING" 섹션 추가 (영어 + 한국어)
  - 전역 설정 파일 절대 금지 경로 명시 (`~/.claude/*`, `$HOME/.claude/*`)
  - 프로젝트별 설정만 허용 (`./.claude/settings.local.json`)
  - Phase 1, 3, 5에 경로 검증 경고 추가 (총 10곳)
  - 모든 Read/Write/Edit 작업 전 경로 검증 체크리스트 추가
  - 실수 시 즉시 중단 프로토콜 명시

### Technical Details

- **수정된 파일**:
  - `mcp-config/SKILL.md`: CRITICAL WARNING 섹션 추가 (~30 lines), Phase별 경고 추가 (~40 lines)

- **경고 추가 위치**:
  - 최상단: CRITICAL WARNING 섹션 (전역 설정 절대 금지)
  - Phase 1: 현재 상태 파악 전 경로 검증 (2곳)
  - Phase 3: 설정 파일 수정 전 경로 검증 (5곳)
  - Phase 5: 도구 권한 관리 전 경로 검증 (3곳)

- **보안 강화**:
  - 전역 설정 수정 방지로 다른 프로젝트 영향 차단
  - 명시적 경로 검증으로 사용자 실수 방지
  - 다국어 경고(영어/한국어)로 명확성 향상

---

## [3.3.1] - 2025-12-11

### Fixed

- **mcp-config skill의 deniedMcpServers 형식 오류 수정**
  - Phase 3에서 배열만 추가하던 것을 `{ "serverCommand": [...] }` 객체 형식으로 수정
  - 올바른 형식 예시 코드 추가로 명확성 향상
  - Claude Code의 settings.local.json 스펙에 맞게 수정

### Technical Details

- **수정된 파일**:
  - `mcp-config/SKILL.md`: Phase 3 "비활성화 요청"/"활성화 요청" 섹션 업데이트, 예시 추가 (~20 lines)
  - `.claude-plugin/marketplace.json`: version 3.3.0 → 3.3.1

---

## [3.3.0] - 2025-12-10

### Added

- **mcp-config skill에 MCP 도구 권한 관리 기능 추가 (Phase 5)**
  - MCP 도구 목록 조회 ("MCP 도구 목록 보여줘")
  - MCP 도구 권한 상태 조회 ("MCP 권한 상태")
  - 전체 MCP 서버 allow/deny/ask 설정 ("serena allow해줘")
  - 개별 MCP 도구 allow/deny/ask 설정 ("serena의 write_memory deny해줘")
  - 권한 제거 기능 ("serena 제거해줘")
  - 권한 이동 자동 처리 (allow → deny 이동 시 allow에서 자동 제거)

- **MCP 도구 참조 문서 추가**: `mcp-config/references/mcp_tools.md`
  - 8개 MCP 서버의 140+ 도구 목록
  - Permission 패턴 규칙 및 예시
  - 전체 서버 vs 개별 도구 패턴 설명

### Technical Details

- **새로운 파일**:
  - `mcp-config/references/mcp_tools.md`: MCP 도구 참조 문서 (~500 lines)

- **수정된 파일**:
  - `mcp-config/SKILL.md`: Phase 5 추가, description/When to Use 업데이트 (~220 lines 추가)
  - `.claude-plugin/marketplace.json`: version 3.2.2 → 3.3.0

### Migration Guide

**기존 사용자 (v3.2.2 → v3.3.0)**:

1. **마켓플레이스 갱신**:
   ```bash
   /marketplace refresh
   ```

2. **호환성**:
   - ✅ 완전 하위 호환 (Breaking Change 없음)
   - ✅ 기존 Phase 1-4 기능 그대로 유지
   - ✅ Phase 5는 새로운 트리거 키워드로만 활성화

3. **새 기능 사용**:
   - "MCP 도구 목록" - 8개 MCP 서버의 모든 도구 확인
   - "serena allow해줘" - 전체 서버 권한 설정
   - "serena의 write_memory deny해줘" - 개별 도구 권한 설정

---

## [3.2.2] - 2025-12-10

### Fixed

- **mcp-config skill 전역 설정 수정 방지**: 프로젝트 로컬 설정만 수정하도록 명확화
  - **문제**: mcp-config skill이 전역 설정(`~/.claude/settings.local.json`)을 수정할 위험
  - **해결**: CRITICAL 경고 박스 추가로 프로젝트 내부 설정만 수정하도록 강제
    - ✅ 올바른 경로: `{PROJECT_ROOT}/.claude/settings.local.json`
    - ❌ 잘못된 경로: `~/.claude/settings.local.json` (전역 설정)
  - 위치: `mcp-config/SKILL.md:118-126, 203-205, 228`

### Technical Details

- **수정된 파일**:
  - `mcp-config/SKILL.md`: 전역 설정 방지 경고 추가 (~15 lines)

### Migration Guide

**기존 사용자 (v3.2.1 → v3.2.2)**:

1. **마켓플레이스 갱신**:
   ```bash
   /marketplace refresh
   ```

2. **호환성**:
   - ✅ 완전 하위 호환 (Breaking Change 없음)
   - ✅ 문서 개선만 포함

---

## [3.2.1] - 2025-12-10

### Fixed

- **amplitude MCP 서버 연결 실패 수정**: 환경 변수 전달 방식 변경
  - **문제**: amplitude-mcp-server가 `env` 블록의 환경 변수를 인식하지 못함
  - **원인**: amplitude-mcp-server는 환경 변수가 아닌 `--api-key` CLI 플래그만 지원
  - **해결**: `env.AMPLITUDE_API_KEY` → `args["--api-key", "${AMPLITUDE_API_KEY:-}"]`
  - 위치: `.claude-plugin/marketplace.json:103-112`

- **mcp-config skill 동적 serverCommand 생성**: 환경 변수 포함 MCP 지원
  - **문제**: 하드코딩된 serverCommand 테이블로는 `${VAR}` 패턴 처리 불가
  - **해결**: marketplace.json에서 동적으로 serverCommand 생성
    - `${VAR}` 패턴을 실제 환경 변수 값으로 치환
    - 환경 변수 없으면 빈 문자열 `""`로 대체
  - 위치: `mcp-config/SKILL.md:25-60, 212-258`

### Changed

- **marketplace.json**: amplitude MCP 설정 변경
  - **이전**: `env: { "AMPLITUDE_API_KEY": "${AMPLITUDE_API_KEY}" }`
  - **현재**: `args: ["-y", "amplitude-mcp-server", "--api-key", "${AMPLITUDE_API_KEY:-}"]`
  - `${VAR:-}` 문법으로 환경 변수 없을 때 빈 값 처리

- **mcp-config skill**: 참조 테이블 구조 변경
  - **이전**: 하드코딩된 `serverCommand` 배열
  - **현재**: `command` + `동적 args 포함` 여부 표시
  - Phase 3에 "Step 1: serverCommand 동적 생성 (MANDATORY)" 추가

### Technical Details

- **amplitude-mcp-server 한계**: 환경 변수 미지원 (CLI 플래그만 지원)
  - 조사한 패키지: `amplitude-mcp-server`, `amplitude-mcp`, Amplitude 공식 Remote MCP
  - 모두 환경 변수 미지원 → `--api-key` 플래그 사용이 유일한 방법

- **v3.0.2 보안 원칙 예외**:
  - 원칙: "인증 정보는 args 대신 env 사용"
  - 예외: amplitude-mcp-server가 env 미지원으로 args 사용 불가피
  - `${VAR:-}` 문법으로 프로세스 목록 노출 최소화

- **수정된 파일**:
  - `.claude-plugin/marketplace.json`: amplitude 설정 변경 (~8 lines)
  - `mcp-config/SKILL.md`: 동적 serverCommand 로직 추가 (~70 lines)

### Migration Guide

**기존 사용자 (v3.2.0 → v3.2.1)**:

1. **마켓플레이스 갱신**:
   ```bash
   /marketplace refresh
   ```

2. **Claude Code 재시작**: amplitude MCP 연결 정상화

3. **확인**:
   ```bash
   claude mcp list
   # amplitude: ✓ Connected 확인
   ```

4. **호환성**:
   - ✅ 완전 하위 호환 (Breaking Change 없음)
   - ✅ 기존 환경 변수 설정 그대로 사용
   - ✅ 다른 MCP 서버 영향 없음

---

## [3.2.0] - 2025-12-10

### Added

- **MCP 서버 3개 추가**: Plugin에 3개 MCP 서버 자동 통합
  - **terraform**: HashiCorp Terraform IaC 자동화
    - Docker 이미지: `hashicorp/terraform-mcp-server` (버전 미지정 → latest)
    - Docker 설치 필요
    - 별도 환경 변수 불필요
    - 위치: `.claude-plugin/marketplace.json:93-101`
  - **amplitude**: Amplitude 사용자 행동 분석
    - `AMPLITUDE_API_KEY` 환경 변수 필요
    - 위치: `.claude-plugin/marketplace.json:102-112`
  - **chrome-devtools**: Chrome DevTools 연동
    - 별도 설정 불필요
    - 위치: `.claude-plugin/marketplace.json:113-118`

- **mcp-config skill 기능 강화**:
  - **환경 변수 설정 가이드**: MCP별 필요 환경 변수 및 발급처 안내
  - **실제 MCP 상태 확인**: `claude mcp list` 명령어로 실제 연결 상태 확인
  - **에러 진단 가이드**: 환경 변수 누락, Docker 미실행, 의존성 문제 해결 방법
  - **상태 테이블 개선**: 설정 상태 + 실제 상태 분리 표시
  - 트리거 키워드 확장: "환경변수", "API 키", "설정" 추가
  - 위치: `mcp-config/SKILL.md`

### Changed

- **marketplace.json**: v3.1.0 → v3.2.0
  - `metadata.version` 업데이트
  - `mcpServers`에 terraform, amplitude, chrome-devtools 추가
  - 위치: `.claude-plugin/marketplace.json`

- **mcp-config skill**: 5개 MCP → 8개 MCP 관리 지원
  - MCP 서버 참조 테이블 업데이트 (8개)
  - 유효한 MCP ID 목록 업데이트
  - 상태 조회 출력 형식 개선 (설정/실제 상태 분리)
  - tools에 Bash 추가 (`claude mcp list` 실행용)
  - 위치: `mcp-config/SKILL.md`

- **README.md**: v3.2.0 반영
  - 환경 변수 섹션에 `AMPLITUDE_API_KEY` 추가
  - MCP 서버 목록에 terraform, amplitude, chrome-devtools 추가
  - 비활성화 예시에 새 MCP serverCommand 추가
  - 버전 정보 업데이트
  - 위치: `README.md`

- **CLAUDE.md**: v3.2.0 아키텍처 결정사항 추가
  - "MCP 서버 확장 (Terraform, Amplitude, Chrome DevTools)" 섹션 추가
  - 버전 정보 업데이트
  - 위치: `CLAUDE.md`

### Technical Details

- **환경 변수 추가**:
  - `AMPLITUDE_API_KEY`: Amplitude MCP 서버 인증용
  - 위치: `~/.zshenv`

- **MCP 서버 총 8개**:
  1. sequential-thinking (필수 - 체계적 사고)
  2. context7 (선택 - 라이브러리 문서)
  3. serena (필수 - 코드 심볼 분석)
  4. sentry (선택 - 에러 트래킹)
  5. atlassian (선택 - JIRA 연동)
  6. **terraform** (선택 - IaC 자동화) ← NEW
  7. **amplitude** (선택 - 사용자 분석) ← NEW
  8. **chrome-devtools** (선택 - DevTools) ← NEW

- **mcp-config 기능 확장**:
  - 환경 변수 설정 가이드 (API 키 발급처 포함)
  - 실제 MCP 상태 확인 (`claude mcp list`)
  - 에러 진단 가이드 (4가지 시나리오)
  - 상태 아이콘: 🟢 connected, 🔴 error, 🟡 disconnected, ⚫ 비활성화

- **수정된 파일**:
  - `.claude-plugin/marketplace.json`: version 3.1.0 → 3.2.0, MCP 3개 추가
  - `mcp-config/SKILL.md`: 5개→8개 MCP, 환경 변수 가이드, 에러 진단 (~150 lines 추가)
  - `README.md`: 환경 변수, MCP 목록, 비활성화 예시 업데이트 (~40 lines)
  - `CLAUDE.md`: v3.2.0 아키텍처 결정사항 (~35 lines)
  - `~/.zshenv`: AMPLITUDE_API_KEY 추가

### Migration Guide

**기존 사용자 (v3.1.0 → v3.2.0)**:

1. **마켓플레이스 갱신**:
   ```bash
   /marketplace refresh
   ```

2. **환경 변수 설정** (선택사항):
   ```bash
   # ~/.zshenv에 추가
   export AMPLITUDE_API_KEY="your-amplitude-api-key"

   # 적용
   source ~/.zshenv
   ```

3. **Docker 확인** (terraform, atlassian 사용 시):
   ```bash
   docker --version
   docker ps  # Docker 실행 확인
   ```

4. **새 기능 활용**:
   ```bash
   # MCP 상태 + 실제 연결 상태 확인
   "MCP 상태 보여줘"

   # 환경 변수 설정 안내
   "amplitude 환경변수 설정 방법"

   # 새 MCP 비활성화
   "terraform 비활성화해줘"
   ```

5. **호환성**:
   - ✅ 완전 하위 호환 (Breaking Change 없음)
   - ✅ 기존 MCP 서버 영향 없음
   - ✅ 새 MCP 서버는 선택적 (환경 변수 없으면 자동 비활성화)

---

## [3.1.0] - 2025-12-10

### Added

- **mcp-config skill (NEW)**: MCP 서버 활성화/비활성화 자동화 스킬
  - **상태 조회**: 5개 MCP 서버 상태 한눈에 확인
  - **비활성화/활성화**: MCP 이름만으로 설정 변경 (serverCommand 자동 처리)
  - **전체 리셋**: 모든 MCP 활성화 (deniedMcpServers 초기화)
  - **자동 파일 생성**: settings.local.json 없을 때 기본 템플릿 생성
  - 위치: `mcp-config/SKILL.md`

- **mcp-config references**: 설정 파일 템플릿
  - `mcp-config/references/settings_template.json`: settings.local.json 기본 구조
  - 위치: `mcp-config/references/`

### Changed

- **marketplace.json**: v3.0.3 → v3.1.0
  - `metadata.version` 업데이트
  - `plugins[0].skills` 배열에 `"./mcp-config"` 추가
  - 위치: `.claude-plugin/marketplace.json`

- **README.md**: mcp-config skill 문서화
  - Available Skills 섹션에 mcp-config 추가
  - MCP 비활성화 가이드에 "mcp-config skill 사용 (권장)" 방법 추가
  - Repository Structure에 mcp-config 디렉토리 추가
  - 위치: `README.md`

### Technical Details

- **새 파일 (3개)**:
  - `mcp-config/SKILL.md` (~6KB, 230 lines)
  - `mcp-config/references/settings_template.json` (120 bytes)
  - `CHANGELOG.md` 업데이트

- **수정된 파일 (2개)**:
  - `.claude-plugin/marketplace.json`: version 3.0.3 → 3.1.0, skill 등록
  - `README.md`: mcp-config 섹션 추가 (~50 lines)

- **Skills 총 6개**:
  1. analyze-issue (이슈 분석)
  2. plan-builder (계획 수립)
  3. execute-plan (계획 실행)
  4. document (문서화)
  5. mr-code-review (MR 코드 리뷰)
  6. **mcp-config** (MCP 설정) ← NEW

### Migration Guide

**기존 사용자 (v3.0.3 → v3.1.0)**:

1. **마켓플레이스 갱신**:
   ```bash
   /marketplace refresh
   ```

2. **새 기능 사용**:
   ```bash
   # MCP 상태 확인
   "MCP 상태 보여줘"

   # 특정 MCP 비활성화
   "sentry 비활성화해줘"

   # 모든 MCP 활성화 (리셋)
   "모든 MCP 활성화"
   ```

3. **호환성**:
   - ✅ 완전 하위 호환 (Breaking Change 없음)
   - ✅ 기존 Skills 영향 없음
   - ✅ 기존 워크플로우 정상 작동

### Development Process

이 기능은 다음 워크플로우로 개발되었습니다:

1. **analyze-issue**: MCP 설정 자동화 필요성 분석 (`MCP_CONFIG_SKILL_REPORT.md`)
2. **plan-builder**: 구현 계획 수립 (`MCP_CONFIG_PLAN.md`, 3차 검토 완료)
3. **execute-plan**: 9개 태스크 완료 (현재 단계)

---

## [3.0.3] - 2025-12-10

### Changed

- **MCP 예시 전체 최신화**: Plugin MCP 네이밍 컨벤션으로 통일
  - **변환 패턴**:
    - `mcp__sequential-thinking__` → `mcp__plugin_workflow-skills_sequential-thinking__`
    - `mcp__serena__` → `mcp__plugin_workflow-skills_serena__`
    - `mcp__atlassian__` → `mcp__plugin_workflow-skills_atlassian__`
  - **함수명 업데이트**: 신규 MCP API 포맷 적용
    - `getJiraIssue` → `jira_get_issue`
    - `searchJiraIssues` → `jira_search`
    - `addCommentToJiraIssue` → `jira_add_comment`
    - `searchConfluence` → `confluence_search`
    - `get_project_context` → `read_memory`

### Updated Files

- **Skills (5개)**:
  - `analyze-issue/SKILL.md`
  - `plan-builder/SKILL.md`
  - `execute-plan/SKILL.md`
  - `document/SKILL.md`
  - `mr-code-review/SKILL.md`

- **Agents (1개)**:
  - `agents/requirement-validator.md`

- **References (7개)**:
  - `mr-code-review/references/verification_guides/architecture_check.md`
  - `mr-code-review/references/verification_guides/convention_check.md`
  - `mr-code-review/references/verification_guides/jira_validation.md`
  - `mr-code-review/references/verification_guides/known_issues_check.md`
  - `mr-code-review/references/verification_guides/security_review.md`
  - `mr-code-review/references/verification_guides/test_coverage.md`
  - `plan-builder/references/plan_template.md`

### Technical Details

- **총 변경 파일**: 13개
- **MCP 참조 업데이트**: 100+ 건
- **호환성**: 완전 하위 호환 (코드 예시만 변경, 기능 변경 없음)

### Migration Guide

**기존 사용자 (v3.0.2 → v3.0.3)**:

1. **자동 업그레이드**:
   ```bash
   /marketplace refresh
   ```

2. **영향 없음**:
   - ✅ 기존 워크플로우 정상 작동
   - ✅ 기능 변경 없음 (문서 업데이트만)
   - ✅ MCP 호출은 Claude Code가 자동 처리

---

## [3.0.2] - 2025-12-10

### Changed

- **MCP 서버 환경 변수 통합**: args에서 민감 정보 제거 → env로 이동
  - **context7**: `--api-key ${CONTEXT7_API_KEY}` → `env.CONTEXT7_API_KEY`
  - **sentry**: `--access-token`, `--host` → `env.SENTRY_ACCESS_TOKEN`, `env.SENTRY_HOST`
  - 위치: `.claude-plugin/marketplace.json:35-66`

### Added

- **환경 변수 1개 추가**:
  - `SENTRY_HOST`: Sentry 인스턴스 호스트 (예: `quantit-io.sentry.io`)

### Technical Details

- **보안 강화**: 프로세스 args에 토큰 노출 방지 (`ps aux`로 볼 수 없음)
- **일관성 향상**: 모든 MCP 서버가 `env` 블록으로 인증 정보 전달
- **args 간소화**: 패키지 이름과 필수 플래그만 유지

### Migration Guide

**기존 사용자 (v3.0.1 → v3.0.2)**:

1. **환경 변수 추가**:
   ```bash
   # ~/.zshenv에 추가
   export SENTRY_HOST="quantit-io.sentry.io"  # 또는 your-org.sentry.io
   ```

2. **자동 업그레이드**: 마켓플레이스 갱신 시 자동 적용
   ```bash
   /marketplace refresh
   ```

3. **호환성**:
   - ✅ 완전 하위 호환 (Breaking Change 없음)
   - ✅ 기존 환경 변수 그대로 사용
   - ✅ 기능 변경 없음 (보안 개선만)

---

## [3.0.1] - 2025-12-10

### Changed

- **Atlassian MCP 서버**: OAuth 방식 → Docker 기반 API 토큰 방식으로 변경
  - **이전**: `mcp-remote` + OAuth 인증 (브라우저 인증 필요, 반복적 재인증)
  - **현재**: Docker 컨테이너 + API 토큰 인증 (환경 변수 설정 후 자동 인증)
  - 위치: `.claude-plugin/marketplace.json:74-97`

### Added

- **환경 변수 3개 추가**:
  - `ATLASSIAN_URL`: Atlassian 인스턴스 URL (예: `https://company.atlassian.net`)
  - `ATLASSIAN_USERNAME`: 사용자 이메일
  - `ATLASSIAN_API_TOKEN`: API 토큰 ([생성 링크](https://id.atlassian.com/manage-profile/security/api-tokens))

### Technical Details

- **Docker 이미지**: `ghcr.io/sooperset/mcp-atlassian:latest`
- **환경 변수 전달**: Docker `-e` 플래그로 JIRA/Confluence 인증 정보 주입
- **보안**: API 토큰은 환경 변수로 처리, marketplace.json에 하드코딩 없음

### Migration Guide

**기존 사용자 (v3.0.0 → v3.0.1)**:

1. **환경 변수 설정**:
   ```bash
   # ~/.zshenv 또는 ~/.bashrc에 추가
   export ATLASSIAN_URL="https://your-company.atlassian.net"
   export ATLASSIAN_USERNAME="your.email@company.com"
   export ATLASSIAN_API_TOKEN="your-api-token-here"
   ```

2. **API 토큰 생성**:
   - https://id.atlassian.com/manage-profile/security/api-tokens 접속
   - "Create API token" 클릭
   - 토큰 이름 입력 (예: "Claude Code MCP")
   - 생성된 토큰을 `ATLASSIAN_API_TOKEN`에 설정

3. **Docker 필요**: `docker --version`으로 설치 확인

4. **장점**:
   - ✅ OAuth 반복 인증 불필요
   - ✅ API 토큰은 수동 revoke 전까지 유효
   - ✅ Docker 컨테이너로 격리된 환경

---

## [3.0.0] - 2025-12-10

### ⚠️ Breaking Changes

- **Agents 시스템 축소**: 5개 → 1개 (requirement-validator만 유지)
  - 삭제된 Agent: code-refactorer, test-generator, code-reviewer, performance-analyzer
  - 영향: 기존에 Agent를 직접 호출하던 코드/명령은 더 이상 작동하지 않음
  - 대안: Skills(analyze-issue, execute-plan)의 Phase에서 동일 기능 직접 제공

### Removed

- **agents/code-refactorer.md**: analyze-issue Phase 3D에 통합
- **agents/test-generator.md**: execute-plan Phase 5에 통합
- **agents/code-reviewer.md**: 미사용으로 삭제 (Skills에서 호출 없음)
- **agents/performance-analyzer.md**: 미사용으로 삭제 (Skills에서 호출 없음)

### Enhanced

- **analyze-issue skill**: Phase 3D "선택적" → "조건부 필수"로 강화
  - code-refactorer 핵심 로직 직접 통합
  - Sequential Thinking 패턴으로 복잡도 분석
  - Cyclomatic complexity > 10, 함수 길이 > 50줄 기준 명시
  - Extract Method/Extract Class 리팩토링 가이드 제공
  - 위치: `analyze-issue/SKILL.md:211-310`

- **execute-plan skill**: Phase 5 "선택적" → "조건부 필수"로 강화
  - test-generator 핵심 로직 직접 통합
  - AAA 패턴 (Arrange-Act-Assert) 코드 예시 추가
  - Given/When/Then BDD 스타일 예시 추가
  - Happy path/Edge cases/Error handling 테스트 케이스 분류표 추가
  - "test-generator agent" 참조 완전 제거
  - 위치: `execute-plan/SKILL.md:440-614`

### Changed

- **marketplace.json**: v2.4.0 → v3.0.0
  - `metadata.version` 업데이트
  - `agents` 배열: 5개 → 1개 (requirement-validator.md만 유지)
  - 위치: `.claude-plugin/marketplace.json`

- **CLAUDE.md**: v3.0.0 아키텍처 결정사항 추가
  - "Agents 시스템 축소 리팩토링" 섹션 추가
  - Repository Structure 업데이트 (agents/ 1개만 표시)
  - Available Agents 섹션 전면 개편
  - Skills vs Agents 비교표 업데이트
  - 위치: `CLAUDE.md`

- **README.md**: v3.0.0 반영
  - Plugin 정의 업데이트 (Agents 역할 변경)
  - Available Agents 섹션 전면 개편
  - 표준 워크플로우 다이어그램 업데이트
  - Repository Structure 업데이트
  - 위치: `README.md`

### Technical Details

- **Dead Code 제거**: 72% → 0%
  - 5개 Agent 중 Skills에서 미사용되는 4개 삭제
  - 핵심 로직은 Skills의 Phase에 직접 통합

- **삭제된 파일 (4개)**:
  - `agents/code-refactorer.md` (~8KB)
  - `agents/test-generator.md` (~6KB)
  - `agents/code-reviewer.md` (~6KB)
  - `agents/performance-analyzer.md` (~7KB)

- **유지된 파일 (1개)**:
  - `agents/requirement-validator.md` (4개 Skills에서 활발히 사용)

- **검증 지표**:
  - marketplace.json JSON 파싱 ✅
  - agents/ 디렉토리 1개 파일 ✅
  - code-refactorer 참조 제거 (grep 0건) ✅
  - test-generator 참조 제거 (grep 0건) ✅

### Development Process

이 기능은 다음 워크플로우로 개발되었습니다:

1. **분석**: Skills에서 Agents 활용도 분석 → 5개 중 1개만 실사용 발견
2. **계획**: Option A 선택 (requirement-validator만 유지, 나머지 Skills에 통합)
3. **plan-builder**: `AGENTS_REFACTOR_PLAN.md` (2차 검토 완료, 7개 이슈 해결)
4. **execute-plan**: 8개 태스크 완료 (현재 단계)

### Migration Guide

**기존 사용자 (v2.4.0 → v3.0.0)**:

1. **마켓플레이스 갱신**:
   ```bash
   /marketplace refresh
   ```

2. **Breaking Change 확인**:
   - ❌ `code-refactorer agent로 ...` → 더 이상 작동 안 함
   - ❌ `test-generator agent로 ...` → 더 이상 작동 안 함
   - ❌ `code-reviewer agent로 ...` → 더 이상 작동 안 함
   - ❌ `performance-analyzer agent로 ...` → 더 이상 작동 안 함
   - ✅ `requirement-validator agent로 ...` → 정상 작동

3. **대안**:
   - **리팩토링 필요 시**: `analyze-issue` skill 실행 → Phase 3D에서 자동 제공
   - **테스트 생성 필요 시**: `execute-plan` skill 실행 → Phase 5에서 자동 생성
   - **코드 리뷰 필요 시**: `mr-code-review` skill 사용
   - **성능 분석 필요 시**: 수동으로 분석 또는 별도 도구 사용

4. **호환성**:
   - ⚠️ **Breaking Change**: Agent 직접 호출 불가
   - ✅ **Skills 워크플로우**: 정상 작동 (오히려 강화됨)
   - ✅ **requirement-validator**: 정상 작동

### Related Files

- 구현 계획: `AGENTS_REFACTOR_PLAN.md`
- 계획 리뷰: `AGENTS_REFACTOR_PLAN_REVIEW_v2.md`

---

## [2.4.0] - 2025-12-10

### Added

- **MCP 서버 추가**: Plugin에 2개 MCP 서버 자동 통합
  - **sentry**: Sentry 에러 트래킹 서버
    - Sentry 이슈 조회 및 분석 지원
    - `SENTRY_ACCESS_TOKEN` 환경 변수 필요
    - `OPENAI_API_KEY` 환경 변수 필요 (Sentry MCP 내부 사용)
    - 위치: `.claude-plugin/marketplace.json:65-77`
  - **atlassian**: Atlassian (JIRA/Confluence) 연동 서버
    - JIRA 이슈, 코멘트, 상태 전환 등 자동화
    - 별도 설정 불필요 (OAuth 자동 처리)
    - 위치: `.claude-plugin/marketplace.json:78-87`

- **MCP 서버 비활성화 가이드**: README에 상세한 비활성화 방법 추가
  - `deniedMcpServers` 사용법 문서화
  - 각 MCP 서버별 정확한 `serverCommand` 예시 제공
  - 5개 MCP 서버 (sequential-thinking, context7, serena, sentry, atlassian) 비활성화 방법
  - 환경 변수 치환 주의사항 명시
  - 위치: `README.md:65-150`

### Changed

- **marketplace.json**: v2.3.0 → v2.4.0
  - `metadata.version` 업데이트
  - 위치: `.claude-plugin/marketplace.json`

- **README.md**: 환경 변수 설정 가이드 확장
  - `SENTRY_ACCESS_TOKEN` 추가
  - `OPENAI_API_KEY` 추가 (Sentry MCP용)
  - 위치: `README.md:50-63`

### Technical Details

- **환경 변수 추가**:
  - `SENTRY_ACCESS_TOKEN`: Sentry MCP 서버 인증용
  - `OPENAI_API_KEY`: Sentry MCP 내부에서 AI 기반 에러 분석 시 사용
  - `.zshenv`에 환경 변수 설정 권장

- **MCP 서버 총 5개**:
  1. sequential-thinking (필수 - 체계적 사고)
  2. context7 (선택 - 라이브러리 문서)
  3. serena (필수 - 코드 심볼 분석)
  4. **sentry** (선택 - 에러 트래킹) ← NEW
  5. **atlassian** (선택 - JIRA 연동) ← NEW

- **보안 강화**:
  - 모든 민감 정보는 환경 변수로 처리
  - marketplace.json에 하드코딩된 키 없음
  - Public repo 노출 방지

### Migration Guide

**기존 사용자 (v2.3.0 → v2.4.0)**:

1. **마켓플레이스 갱신**:
   ```bash
   /marketplace refresh
   ```

2. **환경 변수 설정** (선택사항):
   ```bash
   # ~/.zshrc 또는 ~/.zshenv에 추가
   export SENTRY_ACCESS_TOKEN="sntryu_your-token-here"
   export OPENAI_API_KEY="sk-proj-your-key-here"

   # 적용
   source ~/.zshenv
   ```

3. **자동 업그레이드**:
   - sentry, atlassian MCP 서버 자동 포함
   - 환경 변수 설정하지 않으면 해당 MCP 서버만 비활성화됨 (다른 서버는 정상 작동)

4. **MCP 서버 비활성화** (선택사항):
   - README.md의 "MCP 서버 비활성화" 섹션 참조
   - `.claude/settings.local.json`에서 `deniedMcpServers` 사용

5. **호환성**:
   - ✅ 완전 하위 호환 (Breaking Change 없음)
   - ✅ 기존 MCP 서버 (sequential-thinking, context7, serena) 영향 없음
   - ✅ 기존 워크플로우 정상 작동

**새 MCP 서버 활용**:
```bash
# analyze-issue에서 Sentry 에러 자동 조회
"analyze-issue skill로 Sentry 이슈 ISSUE-123 분석해줘"

# JIRA 이슈 자동 업데이트
"execute-plan 완료 후 JIRA에 구현 완료 코멘트 남겨줘"
```

### Related Files

- `.claude-plugin/marketplace.json`: MCP 서버 정의 추가
- `README.md`: MCP 서버 비활성화 가이드 추가
- `.zshenv`: 환경 변수 설정

---

## [2.3.0] - 2025-12-10

### Added

- **MCP 서버 자동 통합**: Plugin 설치 시 3개 MCP 서버 자동 활성화
  - **sequential-thinking**: 체계적 사고 프로세스 지원 (별도 설정 불필요)
  - **context7**: 최신 라이브러리 문서 조회 (`CONTEXT7_API_KEY` 환경 변수 필요)
  - **serena**: 코드 심볼 분석 및 검색 (별도 설정 불필요, uvx 자동 설치)
  - 위치: `.claude-plugin/marketplace.json:34-64`

### Changed

- **README.md**: MCP 서버 설정 안내 업데이트
  - 환경 변수 설정 방법 문서화 (CONTEXT7_API_KEY)
  - 각 MCP 서버별 요구사항 설명
  - 위치: `README.md:50-63`

### Technical Details

- **보안 강화**: API 키를 환경 변수로 처리하여 public repo 노출 방지
- **코드 분석 강화**: Serena MCP로 심볼 기반 코드 검색 및 편집 지원
- **의존성**: `@modelcontextprotocol/server-sequential-thinking`, `@upstash/context7-mcp`, Serena (GitHub)

---

## [2.2.0] - 2025-12-10

### Enhanced

- **plan-builder skill**: 리뷰 iteration에서 "새로운 문제 탐색" 강제 기능 추가
  - **Step A (Review) 6단계 프로세스로 강화**:
    - **Step 1**: 이전 리뷰 읽기 (피드백 적용 확인)
    - **Step 2**: 현재 계획 읽기
    - **Step 3**: FULL FRESH Critical Review (MANDATORY - 전체 체크리스트 재적용)
    - **Step 4**: CARRYOVER/NEW 이슈 태깅 (진행 추적)
    - **Step 5**: 버전별 리뷰 파일 저장
    - **Step 6**: Iteration Status 출력
  - **CRITICAL INSTRUCTION 블록 추가**:
    - "DO NOT assume sections are OK just because previous review didn't flag them"
    - "APPLY FULL CHECKLIST FROM SCRATCH every time"
    - "LOOK FOR NEW PROBLEMS - each iteration should discover different types of issues"
  - **CARRYOVER/NEW 태깅 시스템**:
    - [CARRYOVER]: 이전 리뷰에서도 발견된 이슈 (미수정 또는 불충분한 수정)
    - [NEW]: 이번 iteration에서 처음 발견한 이슈
    - 진행 상황 가시화 (CARRYOVER 감소 추세 = 피드백 적용 성공)
  - **자동 iteration 강제**:
    - Step D Step 8에 "Do NOT ask user for approval" 명시
    - 사용자 개입 없이 ZERO 이슈까지 자동 반복
  - 위치: `plan-builder/SKILL.md:131-427`

- **review_checklist.md**: Step 2 (Systematic Evaluation) 강화
  - "⚠️ MANDATORY: Apply the FULL checklist EVERY TIME" 지시사항 추가
  - **Critical Requirements**:
    - "Work through **ALL sections** of this checklist (1-10)"
    - "Do not skip sections even if previous review was clean"
    - "Look for NEW problems, not just CARRYOVER issues from previous reviews"
    - "Each iteration should catch different types of issues"
  - **Why this matters** 섹션 추가:
    - "Early iterations catch obvious problems (missing sections, unclear criteria)"
    - "Later iterations catch subtle problems (coupling, edge cases, performance)"
    - "Skipping sections means missing potential issues"
  - 위치: `plan-builder/references/review_checklist.md:8-30`

### Added

- **plan-builder 테스트 문서**: Fresh Exploration 검증 시나리오
  - `plan-builder/tests/review_iteration_fresh_exploration.md` 생성
  - **Test Case 1**: 새로운 문제 탐색 확인
    - Iteration 2에서 [NEW] 태그 1개 이상 존재 검증
    - 검증 명령: `grep -c "\[NEW\]" *_PLAN_REVIEW_v2.md` → 결과 1 이상
  - **Test Case 2**: 전체 체크리스트 재적용 확인
    - 이전 iteration에서 확인 안 한 섹션도 재검토 증거
  - **실패 시나리오**: Scenario A (새 탐색 실패), Scenario B (CARRYOVER 태깅 오류)

### Changed

- **marketplace.json**: v2.1.0 → v2.2.0
  - `metadata.version` 업데이트
  - 위치: `.claude-plugin/marketplace.json`

- **CLAUDE.md**: 아키텍처 결정사항 추가
  - "2025-12-10 - v2.2.0 plan-builder 리뷰 iteration의 새로운 탐색 강제" 섹션
  - **컨텍스트**: 이전 리뷰 포인트만 재확인하고 새로운 문제를 놓칠 위험 발견
  - **근본 원인**: 이전 리뷰 맥락 부재, "새로운 탐색" 명령 부재, 진행 추적 메커니즘 부재
  - **결정**: Incremental Review with Strong Mandates + CARRYOVER/NEW Tracking
  - **재발 방지**: 암묵적 기대를 명시적 지시로 변환
  - 위치: `CLAUDE.md:457-550`

### Technical Details

- **아키텍처 확장**: 문서 강제력 확보 패턴 확장
  - v1.6.0: WHILE 루프 + Binary Decision (피드백 루프 강제)
  - v2.2.0: CRITICAL INSTRUCTION + CARRYOVER/NEW (새 탐색 강제)
  - 패턴: 서술적 지침 ("you should") → 명시적 구조 ("MANDATORY", "DO NOT")

- **새 파일**:
  - `plan-builder/tests/review_iteration_fresh_exploration.md` (6.5KB, 196 lines)

- **수정된 파일**:
  - `.claude-plugin/marketplace.json`: version 2.1.0 → 2.2.0
  - `plan-builder/SKILL.md`: Step A 6단계로 확장, Step D Step 8 강화 (~100 lines 추가)
  - `plan-builder/references/review_checklist.md`: Step 2 MANDATORY 지시사항 추가 (~20 lines 추가)
  - `CLAUDE.md`: v2.2.0 아키텍처 결정 추가 (~100 lines)
  - `README.md`: plan-builder v2.2.0 변경사항 반영 (~20 lines)

- **검증 지표**:
  - SKILL.md Markdown syntax 검증 통과 (코드 블록 26개, 짝수) ✅
  - CRITICAL INSTRUCTION 블록 추가 완료 ✅
  - CARRYOVER/NEW 태깅 예제 포함 ✅
  - 테스트 케이스 문서화 완료 ✅
  - Graceful degradation: 이전 리뷰 파일 없어도 동작 ✅

### Development Process

이 기능은 다음 워크플로우로 개발되었습니다:

1. **analyze-issue**: 리뷰 iteration 문제 분석 (`PLAN_BUILDER_REVIEW_ITERATION_ISSUE_REPORT.md`)
2. **plan-builder**: Fresh Exploration 강제 계획 수립 (`PLAN_BUILDER_REVIEW_ITERATION_FIX_PLAN.md`, 3차 검토 완료)
3. **execute-plan**: 9개 태스크 완료 (Phase 1-3)
4. **document**: 문서화 (현재 단계)

### Migration Guide

**기존 사용자 (v2.1.0 → v2.2.0)**:

1. **마켓플레이스 갱신**:
   ```bash
   /marketplace refresh
   ```

2. **자동 업그레이드**:
   - plan-builder는 확장된 리뷰 프로세스로 자동 업데이트
   - 다른 Skills는 영향 없음

3. **새 기능 활용**:
   - **plan-builder 실행 시**:
     - Iteration 2+에서 [NEW] 이슈 자동 발견
     - CARRYOVER 이슈 추적으로 진행 상황 가시화
     - 사용자 개입 없이 자동 iteration (ZERO 이슈까지)
   - **리뷰 파일 형식**:
     - `*_PLAN_REVIEW_v[N].md` 형식으로 버전 추적
     - 각 이슈에 [CARRYOVER] 또는 [NEW] 태그 포함

4. **호환성**:
   - ✅ 완전 하위 호환 (Breaking Change 없음)
   - ✅ 기존 계획 파일 형식 동일
   - ✅ 기존 워크플로우 영향 없음 (단, iteration 횟수는 증가 가능)

**예상 변화**:
- **이전 (v2.1.0)**: 2-3회 iteration 후 Approve
- **현재 (v2.2.0)**: 각 iteration마다 새로운 유형의 문제 발견 → 더 철저한 리뷰
- **결과**: 계획 생성 시간 약간 증가, 품질 크게 향상

### Related Files

- 분석 리포트: `PLAN_BUILDER_REVIEW_ITERATION_ISSUE_REPORT.md`
- 구현 계획: `PLAN_BUILDER_REVIEW_ITERATION_FIX_PLAN.md`
- 계획 리뷰: `PLAN_BUILDER_REVIEW_ITERATION_FIX_PLAN_REVIEW_FINAL.md`
- 테스트 시나리오: `plan-builder/tests/review_iteration_fresh_exploration.md`

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
