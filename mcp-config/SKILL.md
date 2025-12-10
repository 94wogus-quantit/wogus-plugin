---
name: mcp-config
description: workflow-skills 플러그인의 MCP 서버를 활성화/비활성화하고, MCP 도구 권한(allow/deny/ask)을 관리하며, 환경 변수 설정을 안내합니다. "MCP 상태 보여줘", "sentry 비활성화해줘", "MCP 도구 목록", "serena allow해줘", "serena의 write_memory deny해줘", "MCP 권한 상태" 등의 요청에 사용합니다.
tools: Read, Edit, Write, Bash
---

# MCP Config

workflow-skills 플러그인의 8개 MCP 서버를 관리하고 활성화/비활성화하며, MCP 도구별 권한을 설정하는 Skill입니다.

## When to Use

이 skill을 사용하는 상황:

**MCP 서버 관리 (Phase 1-4)**:
- MCP 서버 상태를 확인하고 싶을 때 ("MCP 상태 보여줘")
- 특정 MCP 서버를 비활성화하고 싶을 때 ("sentry 비활성화해줘")
- 비활성화된 MCP 서버를 다시 활성화하고 싶을 때 ("atlassian 활성화해줘")
- 모든 MCP 서버를 활성화/비활성화하고 싶을 때 ("모든 MCP 활성화")
- MCP 환경 변수 설정 방법을 알고 싶을 때 ("MCP 환경변수 설정 방법")
- 특정 MCP의 API 키 발급처를 알고 싶을 때 ("amplitude API 키 어디서 발급해?")

**MCP 도구 권한 관리 (Phase 5)**:
- MCP 도구 목록을 확인하고 싶을 때 ("MCP 도구 목록 보여줘")
- 특정 MCP 서버 전체를 allow/deny/ask 하고 싶을 때 ("serena allow해줘")
- 특정 MCP 도구만 allow/deny/ask 하고 싶을 때 ("serena의 write_memory deny해줘")
- MCP 도구 권한 상태를 확인하고 싶을 때 ("MCP 권한 상태")
- MCP 도구 권한을 제거하고 싶을 때 ("serena 제거해줘")

**트리거 키워드**: MCP, 상태, 비활성화, 활성화, 끄기, disable, enable, 환경변수, API 키, 설정, 도구, tool, 권한, permission, allow, deny, ask, 제거

---

## MCP 서버 참조 테이블

8개 MCP 서버의 ID입니다. `serverCommand` 값은 **marketplace.json에서 동적으로 읽어야** 합니다.

> **중요**: args에 `${ENV_VAR}` 패턴이 있으면 실제 환경 변수 값으로 확장됩니다. `deniedMcpServers`에 추가할 때는 **확장된 값**을 사용해야 합니다.

| ID | command | 동적 args 포함 |
|----|---------|---------------|
| `sequential-thinking` | `npx` | ❌ |
| `context7` | `npx` | ❌ |
| `serena` | `uvx` | ❌ |
| `sentry` | `npx` | ❌ |
| `atlassian` | `docker` | ❌ |
| `terraform` | `docker` | ❌ |
| `amplitude` | `npx` | ✅ `${AMPLITUDE_API_KEY}` |
| `chrome-devtools` | `npx` | ❌ |

### serverCommand 생성 방법

**Phase 3에서 marketplace.json을 읽고 serverCommand를 동적으로 생성합니다:**

1. marketplace.json에서 해당 MCP의 `command`와 `args` 읽기
2. `args`에서 `${VAR}` 패턴을 환경 변수 값으로 치환
3. `[command, ...args]` 형태로 serverCommand 배열 생성

**예시 (amplitude)**:
```
marketplace.json:
  "args": ["-y", "amplitude-mcp-server", "--api-key", "${AMPLITUDE_API_KEY}"]

환경 변수:
  AMPLITUDE_API_KEY=abc123

생성된 serverCommand:
  ["npx", "-y", "amplitude-mcp-server", "--api-key", "abc123"]
```

### MCP 서버 설명

| ID | 설명 | 환경 변수 |
|----|------|----------|
| `sequential-thinking` | 체계적 사고 (Sequential Thinking MCP) | 없음 |
| `context7` | 라이브러리 문서 조회 (Context7 API) | `CONTEXT7_API_KEY` |
| `serena` | 코드 심볼 분석 및 검색 | 없음 |
| `sentry` | 에러 트래킹 (Sentry) | `SENTRY_ACCESS_TOKEN`, `SENTRY_HOST`, `OPENAI_API_KEY` |
| `atlassian` | JIRA/Confluence 연동 | `ATLASSIAN_URL`, `ATLASSIAN_USERNAME`, `ATLASSIAN_API_TOKEN` |
| `terraform` | Terraform IaC 자동화 (HashiCorp) | 없음 (Docker 필요) |
| `amplitude` | 사용자 행동 분석 (Amplitude Analytics) | `AMPLITUDE_API_KEY` |
| `chrome-devtools` | Chrome DevTools 연동 | 없음 |

### 환경 변수 설정 가이드

MCP 서버가 작동하지 않으면 환경 변수 설정을 확인하세요.

**~/.zshenv (또는 ~/.bashrc)에 추가:**

```bash
# Context7 - 라이브러리 문서 조회
export CONTEXT7_API_KEY="your-api-key"
# 발급: https://context7.com

# Sentry - 에러 트래킹
export SENTRY_ACCESS_TOKEN="your-token"
export SENTRY_HOST="your-org.sentry.io"
export OPENAI_API_KEY="your-openai-key"  # Sentry AI 분석용
# 발급: https://sentry.io → Settings → API Keys

# Atlassian - JIRA/Confluence
export ATLASSIAN_URL="https://your-company.atlassian.net"
export ATLASSIAN_USERNAME="your.email@company.com"
export ATLASSIAN_API_TOKEN="your-api-token"
# 발급: https://id.atlassian.com/manage-profile/security/api-tokens

# Amplitude - 사용자 행동 분석
export AMPLITUDE_API_KEY="your-api-key"
# 발급: https://amplitude.com → Settings → Projects → API Key
```

**적용:**
```bash
source ~/.zshenv
```

**확인:**
```bash
echo $CONTEXT7_API_KEY
echo $SENTRY_ACCESS_TOKEN
```

---

## 워크플로우

> ⛔ **CRITICAL: 절대 전역 설정(`~/.claude/settings.local.json`)을 수정하지 마세요!**
>
> 반드시 **현재 프로젝트 내부**의 `.claude/settings.local.json`만 수정해야 합니다.
> - ✅ 올바른 경로: `{PROJECT_ROOT}/.claude/settings.local.json`
> - ❌ 잘못된 경로: `~/.claude/settings.local.json` (전역 설정)
>
> 이 규칙은 **무조건** 준수해야 합니다. 예외 없음.

### Phase 1: 현재 상태 파악

**Step 1: 설정 파일 확인**

1. Read 도구로 **현재 프로젝트의** `.claude/settings.local.json` 파일을 읽습니다.
   - ⚠️ **절대 `~/.claude/settings.local.json` (전역 설정)이 아닙니다!**
2. 파일이 존재하지 않으면 `deniedMcpServers = []`로 간주합니다 (모두 활성화).
3. `deniedMcpServers` 값을 확인합니다.
4. 참조 테이블을 참고하여 각 MCP의 설정 상태를 파악합니다:
   - `deniedMcpServers`에 해당 `serverCommand`가 있으면 → 비활성화 (설정)
   - 없으면 → 활성화 (설정)

**Step 2: 실제 MCP 상태 확인 (MANDATORY)**

Bash 도구로 실제 MCP 서버 상태를 확인합니다:

```bash
claude mcp list
```

이 명령어는 다음 정보를 제공합니다:
- 실제로 실행 중인 MCP 서버 목록
- 각 MCP의 연결 상태 (connected, error, disconnected)
- 에러가 있는 경우 에러 메시지

**Step 3: 설정 vs 실제 상태 비교**

| 설정 상태 | 실제 상태 | 해석 |
|----------|----------|------|
| 활성화 | connected | ✅ 정상 작동 |
| 활성화 | error | ⚠️ 환경 변수 누락 또는 의존성 문제 |
| 활성화 | 목록에 없음 | ⚠️ MCP 서버 시작 실패 |
| 비활성화 | 목록에 없음 | ✅ 의도대로 비활성화됨 |

**JSON 값 비교 방법**:
```
// serverCommand 값을 JSON.stringify로 비교해야 합니다
// 예: ["npx", "-y", "@sentry/mcp-server@latest"]
// deniedMcpServers의 각 문자열과 정확히 일치해야 함
```

---

### Phase 2: 사용자 요청 파싱

사용자 요청에서 키워드를 파싱합니다:

**1. 작업 유형**

| 키워드 | 작업 |
|--------|------|
| "상태", "보여줘", "확인" | 조회 전용 |
| "비활성화", "끄기", "disable" | 비활성화 요청 |
| "활성화", "켜기", "enable" | 활성화 요청 |
| "모든", "전체" + 비활성화 | 전체 비활성화 |
| "모든", "전체" + 활성화 | 전체 활성화 (리셋) |

**2. 대상 MCP 파싱**

| 개수 | 예시 |
|------|------|
| 단일 | "sentry 비활성화" |
| 복수 | "sentry랑 atlassian 비활성화" |
| 전체 | "모든 MCP 비활성화" |

**3. 유효성 검사**

유효한 MCP ID:
- `sequential-thinking`
- `context7`
- `serena`
- `sentry`
- `atlassian`
- `terraform`
- `amplitude`
- `chrome-devtools`

존재하지 않는 MCP 이름이면 다음 메시지를 출력합니다:
```
'{name}'은(는) 유효한 MCP 이름이 아닙니다.
유효한 MCP: sequential-thinking, context7, serena, sentry, atlassian, terraform, amplitude, chrome-devtools
```

---

### Phase 3: 설정 파일 수정

> ⛔ **다시 한번 강조: 반드시 현재 프로젝트의 `.claude/settings.local.json`만 수정!**
> - 전역 설정(`~/.claude/...`)은 절대 건드리지 않습니다.

**조회 전용**:
- 수정 없이 Phase 4로 이동

**파일 생성 필요**:
1. Write 도구로 **현재 프로젝트에** 기본 템플릿을 생성합니다
2. `references/settings_template.json`의 내용을 사용합니다
3. 생성 후 비활성화/활성화 작업을 수행합니다

**Step 1: serverCommand 동적 생성 (MANDATORY)**

모든 비활성화/활성화 작업 전에 serverCommand를 동적으로 생성해야 합니다:

1. marketplace.json에서 대상 MCP의 `command`와 `args` 읽기
2. `args` 배열의 각 요소에서 `${VAR}` 패턴 처리:
   - Bash로 환경 변수 값 조회: `echo "${VAR_NAME}"`
   - 환경 변수가 설정되어 있으면: 실제 값으로 치환
   - 환경 변수가 없으면: 빈 문자열 `""`로 치환
3. `[command, ...expanded_args]` 형태로 serverCommand 배열 생성

**예시**:
```bash
# 환경 변수 확인
echo "${AMPLITUDE_API_KEY:-}"  # 없으면 빈 문자열
```

```
환경 변수 있음:
  ["npx", "-y", "amplitude-mcp-server", "--api-key", "abc123"]

환경 변수 없음:
  ["npx", "-y", "amplitude-mcp-server", "--api-key", ""]
```

**비활성화 요청**:
1. Step 1에서 생성한 serverCommand 사용
2. 이미 `deniedMcpServers`에 존재하는지 확인합니다 (중복 방지)
   - 이미 존재하면: "이미 비활성화되어 있습니다" 메시지
   - 존재하지 않으면: `deniedMcpServers` 배열에 추가
3. Edit 도구로 JSON을 업데이트합니다

**활성화 요청**:
1. Step 1에서 생성한 serverCommand 사용
2. `deniedMcpServers`에 존재하는지 확인합니다
   - 존재하지 않으면: "이미 활성화되어 있습니다" 메시지
   - 존재하면: 해당 항목을 `deniedMcpServers` 배열에서 제거
3. Edit 도구로 JSON을 업데이트합니다

**전체 활성화 (리셋)**:
1. `deniedMcpServers`를 빈 배열 `[]`로 설정합니다
2. Edit 도구로 JSON을 업데이트합니다

**전체 비활성화**:
1. 8개 MCP 모두에 대해 Step 1 수행하여 serverCommand 생성
2. 모든 serverCommand를 `deniedMcpServers`에 추가합니다
3. Edit 도구로 JSON을 업데이트합니다

---

### Phase 4: 결과 확인

**상태 조회 출력 형식**:

```
## workflow-skills MCP 서버 상태

| MCP | 설정 | 실제 상태 | 설명 |
|-----|------|----------|------|
| sequential-thinking | ✅ 활성화 | 🟢 connected | 체계적 사고 |
| context7 | ✅ 활성화 | 🟢 connected | 라이브러리 문서 |
| serena | ❌ 비활성화 | ⚫ - | 코드 심볼 분석 |
| sentry | ✅ 활성화 | 🔴 error | 에러 트래킹 |
| atlassian | ❌ 비활성화 | ⚫ - | JIRA 연동 |
| terraform | ✅ 활성화 | 🟢 connected | Terraform IaC |
| amplitude | ✅ 활성화 | 🔴 error | 사용자 분석 |
| chrome-devtools | ✅ 활성화 | 🟢 connected | Chrome DevTools |

### 요약
- 설정 비활성화: 2개 (serena, atlassian)
- 실제 에러: 2개 (sentry, amplitude)

### ⚠️ 에러 상세
**sentry**: 환경 변수 누락
- 필요: `SENTRY_ACCESS_TOKEN`, `SENTRY_HOST`, `OPENAI_API_KEY`
- 해결: `~/.zshenv`에 환경 변수 추가 후 `source ~/.zshenv`

**amplitude**: 환경 변수 누락
- 필요: `AMPLITUDE_API_KEY`
- 해결: `~/.zshenv`에 환경 변수 추가 후 `source ~/.zshenv`

설정 파일: .claude/settings.local.json
```

**실제 상태 아이콘**:
- 🟢 connected: 정상 작동
- 🔴 error: 에러 발생 (환경 변수, 의존성 등)
- 🟡 disconnected: 연결 끊김
- ⚫ -: 비활성화됨 (의도적)

**변경 후 출력 형식**:

```
## MCP 설정 변경

변경 사항:
- sentry: ✅ 활성화 → ❌ 비활성화

`.claude/settings.local.json` 업데이트 완료.
새 Claude Code를 시작하면 적용됩니다.
```

**복수 변경 후 출력 형식**:

```
## MCP 설정 변경

변경 사항:
- sentry: ✅ 활성화 → ❌ 비활성화
- atlassian: ✅ 활성화 → ❌ 비활성화

`.claude/settings.local.json` 업데이트 완료.
새 Claude Code를 시작하면 적용됩니다.
```

**이미 해당 상태일 때**:

```
ℹ️ sentry는 이미 비활성화되어 있습니다.
```

---

## 예외 처리 시나리오

### 1. settings.local.json 파일 없음

```
ℹ️ `.claude/settings.local.json` 파일이 없습니다.
기본 설정으로 파일을 생성합니다...
✅ 파일 생성 완료
```

### 2. 존재하지 않는 MCP 이름

```
'{invalid-mcp}'은(는) 유효한 MCP 이름이 아닙니다.
유효한 MCP: sequential-thinking, context7, serena, sentry, atlassian
```

### 3. deniedMcpServers 배열 없음

기존 settings.local.json에 `deniedMcpServers` 배열이 없으면:
1. 빈 배열 `[]`로 추가합니다
2. 이후 비활성화/활성화 작업을 수행합니다

---

## 참고 파일

- `references/settings_template.json`: settings.local.json 기본 템플릿
- `../.claude-plugin/marketplace.json`: MCP 서버 정의 (최신 serverCommand)

---

## 에러 진단 가이드

MCP 서버가 `error` 상태일 때 일반적인 원인과 해결 방법:

### 환경 변수 누락

**증상**: `error` 상태, "환경 변수를 찾을 수 없음" 메시지

**해결**:
1. 필요한 환경 변수 확인 (MCP 서버 설명 테이블 참조)
2. `~/.zshenv`에 환경 변수 추가
3. `source ~/.zshenv` 실행
4. Claude Code 재시작

### Docker 미설치/미실행 (terraform, atlassian)

**증상**: `error` 상태, "docker: command not found" 또는 연결 실패

**해결**:
```bash
# Docker 설치 확인
docker --version

# Docker 실행 확인
docker ps

# Docker Desktop 실행 (macOS)
open -a Docker
```

### npx/uvx 의존성 문제

**증상**: `error` 상태, 패키지 설치 실패

**해결**:
```bash
# npm 캐시 정리
npm cache clean --force

# 수동 설치 테스트
npx -y @modelcontextprotocol/server-sequential-thinking --version
```

### 네트워크 문제

**증상**: `disconnected` 상태, 간헐적 연결 끊김

**해결**:
- 네트워크 연결 확인
- VPN 사용 시 일시 해제 후 테스트
- Claude Code 재시작

---

### Phase 5: MCP 도구 권한 관리

MCP 도구별 권한(allow/deny/ask)을 설정합니다.

**트리거 키워드**: 도구, tool, 권한, permission, allow, deny, ask

#### Step 1: 현재 권한 상태 파악

1. Read 도구로 **현재 프로젝트의** `.claude/settings.local.json` 읽기
2. `permissions.allow`, `permissions.deny`, `permissions.ask` 배열에서 MCP 관련 항목 필터링
3. `mcp__plugin_workflow-skills_*` 패턴 추출

**필터링 예시**:
```json
{
  "permissions": {
    "allow": [
      "mcp__plugin_workflow-skills_sequential-thinking",  // ← MCP 항목
      "Bash(mkdir:*)"  // ← MCP 아님
    ]
  }
}
```

#### Step 2: 사용자 요청 파싱

**1. 요청 유형**

| 키워드 | 작업 |
|--------|------|
| "도구 목록", "tool list", "MCP 도구" | 도구 목록 조회 |
| "권한 상태", "permission status" | 권한 상태 조회 |
| "allow", "허용" | allow 배열에 추가 |
| "deny", "거부" | deny 배열에 추가 |
| "ask", "확인" | ask 배열에 추가 |
| "제거", "삭제", "remove" | 모든 배열에서 제거 |

**2. 대상 파싱**

| 패턴 | 의미 | 생성되는 Permission 패턴 |
|------|------|-------------------------|
| "serena" | 전체 서버 | `mcp__plugin_workflow-skills_serena` |
| "serena의 write_memory" | 개별 도구 | `mcp__plugin_workflow-skills_serena__write_memory` |
| "atlassian의 jira_get_issue" | 개별 도구 | `mcp__plugin_workflow-skills_atlassian__jira_get_issue` |

**3. MCP ID 유효성 검사**

유효한 MCP ID: `sequential-thinking`, `context7`, `serena`, `sentry`, `atlassian`, `terraform`, `amplitude`, `chrome-devtools`

도구 이름은 `references/mcp_tools.md`를 참조하여 검증합니다.

#### Step 3: 권한 설정 수정

**도구 목록 조회**:
- `references/mcp_tools.md` 파일의 내용을 출력합니다
- 각 MCP 서버별 도구 목록과 Permission 패턴을 보여줍니다

**권한 상태 조회**:
- 수정 없이 Step 4로 이동

**allow/deny/ask 설정**:

1. **중복 확인**: 대상이 이미 같은 배열에 있는지 확인
   - 이미 존재하면: "이미 {배열}에 있습니다" 메시지 출력, 종료

2. **다른 배열에서 제거**: 대상이 다른 배열에 있으면 제거 (권한 이동)
   - allow에서 deny로 이동 시: allow에서 제거 후 deny에 추가
   - deny에서 ask로 이동 시: deny에서 제거 후 ask에 추가

3. **새 배열에 추가**: 대상을 요청된 배열에 추가

4. Edit 도구로 JSON 업데이트

**권한 제거**:

1. **존재 확인**: 대상이 allow/deny/ask 중 어디에 있는지 확인
   - 어디에도 없으면: "권한 설정에 없습니다" 메시지 출력, 종료

2. **모든 배열에서 제거**: 해당 항목을 모든 배열에서 제거
   - 다른 배열에 추가하지 않음

3. Edit 도구로 JSON 업데이트

**전체 서버 vs 개별 도구**:
- 전체 서버: `mcp__plugin_workflow-skills_{mcp_id}`
- 개별 도구: `mcp__plugin_workflow-skills_{mcp_id}__{tool_name}`

#### Step 4: 결과 확인

**도구 목록 출력 형식**:

```
## MCP 도구 목록

상세 목록: `mcp-config/references/mcp_tools.md`

### 요약
| MCP 서버 | 도구 수 | 전체 서버 패턴 |
|----------|--------|----------------|
| sequential-thinking | 1 | `mcp__plugin_workflow-skills_sequential-thinking` |
| context7 | 2 | `mcp__plugin_workflow-skills_context7` |
| serena | 24 | `mcp__plugin_workflow-skills_serena` |
| sentry | 6+ | `mcp__plugin_workflow-skills_sentry` |
| atlassian | 41 | `mcp__plugin_workflow-skills_atlassian` |
| terraform | 33 | `mcp__plugin_workflow-skills_terraform` |
| amplitude | 7+ | `mcp__plugin_workflow-skills_amplitude` |
| chrome-devtools | 26 | `mcp__plugin_workflow-skills_chrome-devtools` |

**총 도구 수**: 140+ 개

### 사용 방법
- 전체 서버 allow: "serena allow해줘"
- 개별 도구 deny: "serena의 write_memory deny해줘"
- 권한 상태 확인: "MCP 권한 상태"
```

**권한 상태 출력 형식**:

```
## MCP 도구 권한 상태

### Allow (자동 허용)
| 패턴 | 대상 |
|------|------|
| `mcp__plugin_workflow-skills_sequential-thinking` | sequential-thinking 전체 |
| `mcp__plugin_workflow-skills_serena__find_symbol` | serena의 find_symbol |

### Deny (거부)
| 패턴 | 대상 |
|------|------|
| (없음) | |

### Ask (매번 확인)
| 패턴 | 대상 |
|------|------|
| (없음) | |

### 변경 방법
- 전체 allow: "serena 전체 allow해줘"
- 개별 deny: "serena의 write_memory deny해줘"
- 권한 제거: "serena 제거해줘"

설정 파일: .claude/settings.local.json
```

**권한 변경 출력 형식**:

```
## MCP 도구 권한 변경

변경 사항:
- `mcp__plugin_workflow-skills_serena`: (없음) → ✅ allow

`.claude/settings.local.json` 업데이트 완료.
```

**권한 이동 출력 형식**:

```
## MCP 도구 권한 변경

변경 사항:
- `mcp__plugin_workflow-skills_serena`: ✅ allow → ❌ deny

`.claude/settings.local.json` 업데이트 완료.
```

**권한 제거 출력 형식**:

```
## MCP 도구 권한 변경

변경 사항:
- `mcp__plugin_workflow-skills_serena`: ✅ allow → (제거됨)

`.claude/settings.local.json` 업데이트 완료.
```

**이미 해당 상태일 때**:

```
ℹ️ `mcp__plugin_workflow-skills_serena`는 이미 allow에 있습니다.
```

**존재하지 않는 권한 제거 시**:

```
ℹ️ `mcp__plugin_workflow-skills_serena`는 권한 설정에 없습니다.
```

---

## Phase 5 예외 처리 시나리오

### 1. 존재하지 않는 MCP ID

```
'{invalid-id}'은(는) 유효한 MCP ID가 아닙니다.
유효한 MCP: sequential-thinking, context7, serena, sentry, atlassian, terraform, amplitude, chrome-devtools
```

### 2. 존재하지 않는 도구 이름

```
'{tool-name}'은(는) '{mcp-id}'의 유효한 도구가 아닙니다.
도구 목록: `mcp-config/references/mcp_tools.md` 참조
```

### 3. permissions 객체 없음

기존 settings.local.json에 `permissions` 객체가 없으면:
1. 기본 구조 추가: `{ "allow": [], "deny": [], "ask": [] }`
2. 이후 권한 설정 작업 수행

---

## Tips

- **재시작 필요**: MCP 설정 변경 후 Claude Code를 재시작해야 적용됩니다
- **serverCommand 정확성**: 값이 정확히 일치해야 비활성화가 됩니다
- **marketplace.json 참고**: MCP 서버 args 변경 시 이 skill의 참조 테이블도 업데이트해야 합니다
- **실제 상태 확인**: `claude mcp list` 명령어로 실제 MCP 상태 확인 가능
- **도구 권한 우선순위**: Deny > Ask > Allow (Claude Code 기본 동작)
