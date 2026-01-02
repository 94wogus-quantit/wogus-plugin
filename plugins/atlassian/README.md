# Atlassian MCP Plugin

Jira 이슈 관리 및 Confluence 문서 연동을 위한 MCP 서버 플러그인 (Self-hosted 지원).

## 설치

```bash
/plugin install wogus-plugins:atlassian
```

## 환경변수

```bash
export ATLASSIAN_URL="https://your-domain.atlassian.net"
export ATLASSIAN_USERNAME="your-email@example.com"
export ATLASSIAN_API_TOKEN="your-api-token"
```

[Atlassian API Token 발급 방법](https://support.atlassian.com/atlassian-account/docs/manage-api-tokens-for-your-atlassian-account/)

## MCP 서버

- **atlassian**: Atlassian MCP Server (mcp-atlassian)
  - 패키지: `mcp-atlassian` (uvx)
  - 기능: Jira 이슈 CRUD, Confluence 페이지 관리, 검색
