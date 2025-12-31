# Slack MCP 플러그인 생성 분석 보고서

## 요약

Slack MCP 플러그인을 wogus-plugins 마켓플레이스에 추가하기 위한 분석 보고서입니다.
기존 amplitude/terraform 플러그인 패턴을 따르며, 환경변수를 통해 Slack 토큰을 전달합니다.

## 컨텍스트

### 사용자 요청
- Slack MCP 플러그인 생성
- 환경변수 사용 (amplitude/terraform 패턴 참고)

### 현재 상태
- **버전**: v3.8.0
- **기존 플러그인**: workflow-bundle, terraform, amplitude (3개)
- **목표**: slack 플러그인 추가하여 4개로 확장

## 조사 과정

### 1. Slack MCP 패키지 후보 분석

| 패키지 | 상태 | 실행 방식 | 기능 수 |
|--------|------|-----------|---------|
| `@modelcontextprotocol/server-slack` | ❌ **DEPRECATED** | - | - |
| `slack-mcp` (tomgutt) | ✅ 활성 | npx | 2개 |
| `slack-mcp-server` (korotovsky) | ✅ **활성** | **npx** | **5개** |

### 2. 패키지 선정 이유

**선정: `slack-mcp-server` (korotovsky)**

- ✅ npx로 직접 실행 가능 (`npx -y slack-mcp-server@latest --transport stdio`)
- ✅ 최신 업데이트 (2025-12-09)
- ✅ 풍부한 기능 (5개 도구)
- ✅ 유연한 토큰 옵션 (xoxb, xoxp, xoxc/xoxd)
- ✅ MIT 라이선스

**제외 이유**:
- `@modelcontextprotocol/server-slack`: deprecated 상태
- `slack-mcp` (tomgutt): 기능 제한 (2개만)

### 3. 환경변수 분석

`slack-mcp-server`가 지원하는 토큰 옵션:

| 변수명 | 토큰 유형 | 설명 |
|--------|----------|------|
| `SLACK_MCP_XOXB_TOKEN` | Bot 토큰 | Slack App Bot 토큰 (권장) |
| `SLACK_MCP_XOXP_TOKEN` | User 토큰 | User OAuth 토큰 |
| `SLACK_MCP_XOXC_TOKEN` + `SLACK_MCP_XOXD_TOKEN` | Browser 토큰 | 브라우저 세션 토큰 |

### 4. 기능

`slack-mcp-server`가 제공하는 MCP 도구:

1. **`conversations_history`**: 채널/DM 메시지 히스토리 조회
2. **`conversations_replies`**: 스레드 메시지 조회
3. **`conversations_search_messages`**: 메시지 검색 (필터 지원)
4. **`channels_list`**: 채널 목록 조회
5. **`conversations_add_message`**: 메시지 전송 (선택적 활성화)

## 권장사항

### 즉시 조치

#### 1. marketplace.json 수정

**위치**: `.claude-plugin/marketplace.json:71`

```json
{
  "name": "slack",
  "description": "Slack 메시지 검색, 히스토리, 스레드 조회 MCP 서버",
  "source": "./",
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "slack-mcp-server@latest", "--transport", "stdio"],
      "env": {
        "SLACK_MCP_XOXB_TOKEN": "${SLACK_BOT_TOKEN:-}"
      }
    }
  }
}
```

#### 2. 버전 업데이트

- `metadata.version`: 3.8.0 → 3.9.0
- `metadata.description`: 플러그인 수 3개 → 4개 반영

### 문서 업데이트

#### README.md

```markdown
## 설치

/plugin install wogus-plugins:slack  # Slack MCP만
```

#### CHANGELOG.md

```markdown
## v3.9.0 - Slack MCP 플러그인 추가

### Added
- **slack 플러그인**: Slack 메시지 검색, 히스토리, 스레드 조회 MCP 서버
  - 패키지: `slack-mcp-server` (korotovsky)
  - 환경변수: `SLACK_BOT_TOKEN`
  - 기능: conversations_history, conversations_replies, conversations_search_messages, channels_list
```

### 사용자 설정

플러그인 설치 후 사용자가 설정해야 할 환경변수:

```bash
# ~/.zshrc 또는 ~/.bashrc
export SLACK_BOT_TOKEN="xoxb-your-bot-token-here"
```

## 테스트 계획

- [ ] npx -y slack-mcp-server@latest --transport stdio 실행 확인
- [ ] 환경변수 전달 확인
- [ ] conversations_history 도구 동작 확인
- [ ] conversations_search_messages 도구 동작 확인

## 관련 코드

- [marketplace.json:14-71](.claude-plugin/marketplace.json#L14-L71) - 기존 플러그인 정의
- [amplitude 플러그인](.claude-plugin/marketplace.json#L54-L70) - 환경변수 패턴 참고

## 참고 자료

- [slack-mcp-server GitHub](https://github.com/korotovsky/slack-mcp-server)
- [slack-mcp-server npm](https://www.npmjs.com/package/slack-mcp-server)
- [Slack Bot Tokens](https://api.slack.com/authentication/token-types#bot)
