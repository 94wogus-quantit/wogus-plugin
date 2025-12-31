# Slack MCP 플러그인 구현 계획

## 개요

| 항목 | 내용 |
|------|------|
| **목표** | wogus-plugins 마켓플레이스에 Slack MCP 플러그인 추가 |
| **패키지** | `slack-mcp-server` (korotovsky) |
| **버전** | v3.8.0 → v3.9.0 |
| **예상 작업량** | 4개 Task |

## 분석 보고서 참조

- **파일**: `SLACK_MCP_PLUGIN_REPORT.md`
- **선정 패키지**: `slack-mcp-server@latest`
- **실행 명령**: `npx -y slack-mcp-server@latest --transport stdio`
- **환경변수**: `SLACK_MCP_XOXB_TOKEN`

---

## Task 목록

### Task 1: marketplace.json 수정 (플러그인 추가 + 메타데이터)

**우선순위**: P0 (핵심)

**설명**: marketplace.json에 slack 플러그인을 추가하고 메타데이터를 업데이트합니다.

**작업 내용**:

1. **메타데이터 업데이트**:
   - `metadata.version`: "3.8.0" → "3.9.0"
   - `metadata.description`: 플러그인 수 3개 → 4개 반영

2. **plugins 배열에 slack 플러그인 추가**:

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

**수정 파일**:
- `.claude-plugin/marketplace.json`
  - Line 9: version
  - Line 8: description
  - Line 71+: plugins 배열 끝에 slack 추가

**의존성**: 없음

**Testing Strategy**:
- **테스트 유형**: JSON 유효성 + 구조 검증 + 통합 테스트
- **테스트 케이스**:
  1. Given: marketplace.json 파일
     When: JSON 파싱
     Then: 유효한 JSON 구조
  2. Given: marketplace.json
     When: metadata.version 조회
     Then: "3.9.0" 반환
  3. Given: plugins 배열
     When: slack 플러그인 조회
     Then: name="slack", mcpServers.slack 존재
  4. Given: slack.mcpServers.slack
     When: 구조 검증
     Then: command="npx", args 배열, env.SLACK_MCP_XOXB_TOKEN 존재
  5. Given: npx slack-mcp-server 명령
     When: 실행 (--help)
     Then: 정상 종료 (exit 0)
- **테스트 명령**:
  ```bash
  # 1. JSON 유효성 검증
  cat .claude-plugin/marketplace.json | jq . > /dev/null && echo "✅ JSON valid"

  # 2. 버전 확인
  cat .claude-plugin/marketplace.json | jq -r '.metadata.version' | grep -q "3.9.0" && echo "✅ Version 3.9.0"

  # 3. slack 플러그인 존재 확인
  cat .claude-plugin/marketplace.json | jq '.plugins[] | select(.name=="slack")' | grep -q "slack" && echo "✅ Slack plugin exists"

  # 4. MCP 서버 구조 확인
  cat .claude-plugin/marketplace.json | jq '.plugins[] | select(.name=="slack") | .mcpServers.slack.command' | grep -q "npx" && echo "✅ npx command set"

  # 5. npx 패키지 실행 테스트 (패키지 존재 확인)
  npm view slack-mcp-server version && echo "✅ Package exists on npm"
  ```
- **성공 기준**: 모든 테스트 명령 통과

---

### Task 2: README.md 업데이트

**우선순위**: P1 (문서화)

**설명**: README에 slack 플러그인 설치 방법 및 환경변수 문서화

**작업 내용**:
1. 플러그인 목록에 slack 추가
2. 설치 명령어 추가: `/plugin install wogus-plugins:slack`
3. 환경변수 설정 방법 추가: `SLACK_BOT_TOKEN`

**수정 파일**:
- `README.md`

**의존성**: Task 1 완료 후

**Testing Strategy**:
- **테스트 유형**: 내용 검증
- **테스트 케이스**:
  1. Given: README.md
     When: "slack" 검색 (대소문자 무시)
     Then: 플러그인 설명 섹션 존재
  2. Given: README.md
     When: 설치 명령 검색
     Then: `wogus-plugins:slack` 포함
  3. Given: README.md
     When: 환경변수 검색
     Then: `SLACK_BOT_TOKEN` 포함
- **테스트 명령**:
  ```bash
  # 1. slack 언급 확인
  grep -i "slack" README.md | head -3 && echo "✅ Slack mentioned"

  # 2. 설치 명령 확인
  grep "wogus-plugins:slack" README.md && echo "✅ Install command exists"

  # 3. 환경변수 확인
  grep "SLACK_BOT_TOKEN" README.md && echo "✅ Env var documented"
  ```
- **성공 기준**: 모든 grep 명령이 결과 반환

---

### Task 3: CHANGELOG.md 업데이트

**우선순위**: P1 (문서화)

**설명**: v3.9.0 변경사항을 CHANGELOG에 기록

**작업 내용**:
1. 파일 상단에 v3.9.0 섹션 추가
2. Added 항목에 slack 플러그인 추가 기록

```markdown
## v3.9.0 - Slack MCP 플러그인 추가 (2025-12-31)

### Added
- **slack 플러그인**: Slack 메시지 검색, 히스토리, 스레드 조회 MCP 서버
  - 패키지: `slack-mcp-server` (korotovsky)
  - 환경변수: `SLACK_BOT_TOKEN`
  - 기능: conversations_history, conversations_replies, conversations_search_messages, channels_list
```

**수정 파일**:
- `CHANGELOG.md`

**의존성**: Task 1 완료 후

**Testing Strategy**:
- **테스트 유형**: 내용 검증
- **테스트 케이스**:
  1. Given: CHANGELOG.md
     When: "v3.9.0" 검색
     Then: 섹션 헤더 존재
  2. Given: CHANGELOG.md v3.9.0 섹션
     When: 내용 확인
     Then: "slack", "Added" 포함
  3. Given: CHANGELOG.md
     When: 날짜 형식 확인
     Then: YYYY-MM-DD 형식
- **테스트 명령**:
  ```bash
  # 1. v3.9.0 섹션 확인
  grep "## v3.9.0" CHANGELOG.md && echo "✅ v3.9.0 section exists"

  # 2. slack 내용 확인
  grep -A 10 "v3.9.0" CHANGELOG.md | grep -i "slack" && echo "✅ Slack in changelog"

  # 3. 날짜 형식 확인
  grep "v3.9.0" CHANGELOG.md | grep -E "[0-9]{4}-[0-9]{2}-[0-9]{2}" && echo "✅ Date format OK"
  ```
- **성공 기준**: v3.9.0 섹션에 slack 플러그인 설명, 올바른 날짜 형식

---

### Task 4: CLAUDE.md 업데이트

**우선순위**: P1 (문서화)

**설명**: 개발자 문서에 플러그인 수 및 구조 업데이트

**작업 내용**:
1. 플러그인 수 "3개" → "4개" 수정
2. 플러그인 목록에 slack 추가
3. 설치 명령 예시 업데이트

**수정 파일**:
- `CLAUDE.md`

**의존성**: Task 1 완료 후

**Testing Strategy**:
- **테스트 유형**: 내용 검증
- **테스트 케이스**:
  1. Given: CLAUDE.md
     When: 플러그인 수 확인
     Then: "4개" 포함 (또는 "3개" 없음)
  2. Given: CLAUDE.md
     When: "slack" 검색
     Then: 플러그인 설명 존재
  3. Given: CLAUDE.md
     When: 설치 명령 확인
     Then: `wogus-plugins:slack` 포함
- **테스트 명령**:
  ```bash
  # 1. 플러그인 수 확인 (4개 존재 확인)
  grep "4개" CLAUDE.md && echo "✅ 4 plugins mentioned"

  # 2. slack 언급 확인
  grep -i "slack" CLAUDE.md | head -3 && echo "✅ Slack in CLAUDE.md"

  # 3. 설치 명령 확인
  grep "wogus-plugins:slack" CLAUDE.md && echo "✅ Install command in CLAUDE.md"
  ```
- **성공 기준**: 4개 플러그인 반영, slack 설명 포함

---

## 실행 순서

```
Task 1 (marketplace.json - 플러그인 + 메타데이터)
    ↓
Task 2, 3, 4 (문서 업데이트 - 병렬 가능)
```

## 성공 기준 (전체)

### 검증 체크리스트

- [ ] marketplace.json이 유효한 JSON
- [ ] 버전이 3.9.0으로 업데이트됨
- [ ] slack 플러그인이 plugins 배열에 존재
- [ ] 모든 문서가 v3.9.0, 4개 플러그인 반영

### 통합 검증 명령

```bash
#!/bin/bash
echo "=== Slack MCP 플러그인 통합 검증 ==="

# 1. JSON 유효성
echo -n "1. JSON 유효성: "
cat .claude-plugin/marketplace.json | jq . > /dev/null 2>&1 && echo "✅ PASS" || echo "❌ FAIL"

# 2. 버전 확인
echo -n "2. 버전 3.9.0: "
cat .claude-plugin/marketplace.json | jq -r '.metadata.version' | grep -q "3.9.0" && echo "✅ PASS" || echo "❌ FAIL"

# 3. slack 플러그인 존재
echo -n "3. slack 플러그인: "
cat .claude-plugin/marketplace.json | jq '.plugins[] | select(.name=="slack")' | grep -q "slack" && echo "✅ PASS" || echo "❌ FAIL"

# 4. README 업데이트
echo -n "4. README slack 포함: "
grep -qi "slack" README.md && echo "✅ PASS" || echo "❌ FAIL"

# 5. CHANGELOG 업데이트
echo -n "5. CHANGELOG v3.9.0: "
grep -q "v3.9.0" CHANGELOG.md && echo "✅ PASS" || echo "❌ FAIL"

# 6. CLAUDE.md 업데이트
echo -n "6. CLAUDE.md slack 포함: "
grep -qi "slack" CLAUDE.md && echo "✅ PASS" || echo "❌ FAIL"

# 7. npm 패키지 존재
echo -n "7. npm 패키지 존재: "
npm view slack-mcp-server version > /dev/null 2>&1 && echo "✅ PASS" || echo "❌ FAIL"

echo "=== 검증 완료 ==="
```

## 롤백 계획

문제 발생 시:
```bash
git checkout main -- .claude-plugin/marketplace.json
git checkout main -- README.md CHANGELOG.md CLAUDE.md
```

## 참고 자료

- [slack-mcp-server GitHub](https://github.com/korotovsky/slack-mcp-server)
- [분석 보고서](SLACK_MCP_PLUGIN_REPORT.md)
