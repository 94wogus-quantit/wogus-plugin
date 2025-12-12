# Personal Claude Code Plugins

Claude Code의 확장 기능(Plugins)을 모아둔 저장소입니다. Skills를 중심으로 체계적인 개발 워크플로우를 지원합니다.

## 🔌 Plugin이란?

**Plugin**은 Claude Code를 확장하는 모든 기능의 총칭입니다:

- **🤖 Skills**: AI 기반 워크플로우 오케스트레이터 (분석, 계획, 실행, 문서화 등)
- **🔧 Agents**: AC (Acceptance Criteria) 추적 자동화 (v3.0.0: requirement-validator만 유지)
- **⚙️ Custom Commands**: 워크플로우 자동화 커맨드 (별도 설치 필요)
- **🔗 MCP Servers**: 외부 도구/서비스 통합 (별도 설정 필요)

이 저장소는 **Skills + Agents (v3.6.0)**를 제공하며, Custom Commands와 MCP Servers는 별도로 설치/설정해야 합니다.

**v3.6.0 주요 기능**:
- 🎯 **mr-code-review 7가지 검증**: 비즈니스 로직 정확성 검증 추가
- 📄 **2개 파일 출력**: `INLINE_DISCUSSION.json` + `SUMMARY_COMMENT.md`
- 🔒 **범용 보안 스캔**: Trivy로 모든 언어 지원
- 💾 **Phase별 중간 산출물**: Context 손실 방지를 위한 `.mr-review/` 파일 저장
- ⚠️ **브랜치 보호**: main/master/staging 브랜치 직접 작업 방지

## 🌐 언어 정책

**모든 스킬은 기본적으로 한국어로 작동합니다.**

- ✅ 모든 문서, 리포트, 계획서는 **한국어**로 생성
- ✅ 사용자 응답과 설명은 **한국어**로 제공
- ✅ 코드 주석과 문서화는 **한국어**로 작성
- 🔄 예외: 사용자가 다른 언어로 작성하면 해당 언어로 응답

이는 모든 스킬에 강제 적용되는 **필수 정책**입니다.

## 🔒 브랜치 보호 정책 (v3.5.0+)

workflow-skills는 보호된 브랜치(main, master, staging)에서 직접 작업하는 것을 방지합니다.

### 보호 동작

| Skill | 보호된 브랜치 감지 시 |
|-------|---------------------|
| **analyze-issue** | 새 feature 브랜치 자동 생성 |
| **plan-builder** | 경고 + 권장 워크플로우 안내 |
| **execute-plan** | 경고 (코드 수정 위험 강조) |
| **document** | 경고 (문서 커밋 위험) |

### 권장 워크플로우

```bash
# 1. Feature 브랜치 생성 후 작업
git checkout -b feature/JIRA-123

# 2. 워크플로우 실행
/analyze-issue JIRA-123
/plan JIRA-123_REPORT.md
/execute-plan JIRA-123_PLAN.md
/document

# 3. MR 생성 및 리뷰
glab mr create --title "feat: JIRA-123 구현"
```

## 🚀 Getting Started

### 마켓플레이스로 설치 (권장)

1. Claude Code에서 marketplace 추가:
   ```bash
   /marketplace add git@github.com:94wogus-quantit/wogus-plugin.git
   ```

2. 원하는 스킬 설치:
   ```bash
   /plugin install analyze-issue
   /plugin install plan-builder
   /plugin install execute-plan
   /plugin install document
   /plugin install mr-code-review
   ```

3. 설치 확인:
   ```bash
   /plugin list
   ```

4. **MCP 서버 설정** (선택사항):

   일부 MCP 서버는 환경 변수 설정이 필요합니다:

   ```bash
   # ~/.zshenv 또는 ~/.bashrc에 추가

   # Context7 API 키 (라이브러리 문서 조회용)
   export CONTEXT7_API_KEY="your-api-key-here"

   # Sentry 설정 (에러 트래킹용)
   export SENTRY_ACCESS_TOKEN="your-sentry-token-here"
   export SENTRY_HOST="your-org.sentry.io"  # 예: quantit-io.sentry.io

   # OpenAI API 키 (Sentry MCP 내부 AI 분석용)
   export OPENAI_API_KEY="your-openai-api-key-here"

   # Atlassian API 토큰 (JIRA/Confluence 연동용)
   export ATLASSIAN_URL="https://your-company.atlassian.net"
   export ATLASSIAN_USERNAME="your.email@company.com"
   export ATLASSIAN_API_TOKEN="your-api-token-here"

   # Amplitude API 키 (사용자 행동 분석용) - v3.2.0 NEW
   export AMPLITUDE_API_KEY="your-amplitude-api-key-here"

   # Claude Code 재시작
   ```

   - **sequential-thinking**: 별도 설정 없이 자동 동작
   - **context7**: [Context7](https://context7.com)에서 API 키 발급 필요
   - **serena**: 코드 심볼 분석 및 검색 (별도 설정 불필요, uvx 자동 설치)
   - **sentry**: [Sentry](https://sentry.io)에서 액세스 토큰 발급 필요 (+ OpenAI API 키)
   - **atlassian**: Docker 기반 API 토큰 인증
     - [Atlassian API 토큰 생성](https://id.atlassian.com/manage-profile/security/api-tokens)에서 토큰 발급
     - Docker가 설치되어 있어야 함 (`docker --version`으로 확인)
   - **terraform** (v3.2.0 NEW): HashiCorp Terraform IaC 자동화 (별도 설정 불필요, Docker 필요)
   - **amplitude** (v3.2.0 NEW): [Amplitude](https://amplitude.com)에서 API 키 발급 필요
   - **chrome-devtools** (v3.2.0 NEW): Chrome DevTools 연동 (별도 설정 불필요)

5. **MCP 서버 비활성화** (선택사항):

   특정 MCP 서버를 사용하지 않으려면 **mcp-config skill** 또는 수동으로 `.claude/settings.local.json`을 편집합니다.

   **방법 1: mcp-config skill 사용 (권장)**
   ```bash
   # MCP 상태 확인
   "MCP 상태 보여줘"

   # 특정 MCP 비활성화
   "sentry 비활성화해줘"
   "atlassian이랑 serena 끄기"

   # 모든 MCP 활성화 (리셋)
   "모든 MCP 활성화"
   ```

   **방법 2: 수동 편집**
   `.claude/settings.local.json`에서 `deniedMcpServers`를 사용합니다.

   **주의**:
   - `serverCommand`는 전체 명령어 배열을 **정확히 일치**시켜야 합니다.
   - 환경 변수(`${CONTEXT7_API_KEY}`)는 **실제 값으로 치환**해야 합니다.
   - 현재 API 키 확인: `echo $CONTEXT7_API_KEY`

   ```json
   {
     "deniedMcpServers": [
       {
         "serverCommand": ["npx", "-y", "@modelcontextprotocol/server-sequential-thinking"]
       }
     ]
   }
   ```

   **각 MCP 서버의 정확한 serverCommand:**

   ```json
   // sequential-thinking 비활성화
   {
     "deniedMcpServers": [
       {
         "serverCommand": ["npx", "-y", "@modelcontextprotocol/server-sequential-thinking"]
       }
     ]
   }

   // context7 비활성화 (v3.0.2+)
   {
     "deniedMcpServers": [
       {
         "serverCommand": ["npx", "-y", "@upstash/context7-mcp"]
       }
     ]
   }

   // serena 비활성화
   {
     "deniedMcpServers": [
       {
         "serverCommand": ["uvx", "--from", "git+https://github.com/oraios/serena", "serena", "start-mcp-server", "--context", "ide-assistant", "--enable-web-dashboard", "false"]
       }
     ]
   }

   // sentry 비활성화 (v3.0.2+)
   {
     "deniedMcpServers": [
       {
         "serverCommand": ["npx", "-y", "@sentry/mcp-server@latest"]
       }
     ]
   }

   // atlassian 비활성화
   {
     "deniedMcpServers": [
       {
         "serverCommand": ["docker", "run", "-i", "--rm", "-e", "JIRA_URL", "-e", "JIRA_USERNAME", "-e", "JIRA_API_TOKEN", "-e", "CONFLUENCE_URL", "-e", "CONFLUENCE_USERNAME", "-e", "CONFLUENCE_API_TOKEN", "ghcr.io/sooperset/mcp-atlassian:latest"]
       }
     ]
   }

   // terraform 비활성화 (v3.2.0+)
   {
     "deniedMcpServers": [
       {
         "serverCommand": ["docker", "run", "-i", "--rm", "hashicorp/terraform-mcp-server"]
       }
     ]
   }

   // amplitude 비활성화 (v3.2.0+)
   {
     "deniedMcpServers": [
       {
         "serverCommand": ["npx", "-y", "amplitude-mcp-server"]
       }
     ]
   }

   // chrome-devtools 비활성화 (v3.2.0+)
   {
     "deniedMcpServers": [
       {
         "serverCommand": ["npx", "-y", "chrome-devtools-mcp@latest"]
       }
     ]
   }

   // 여러 개 동시 비활성화 (예: context7 + sentry + serena)
   {
     "deniedMcpServers": [
       {
         "serverCommand": ["npx", "-y", "@upstash/context7-mcp"]
       },
       {
         "serverCommand": ["npx", "-y", "@sentry/mcp-server@latest"]
       },
       {
         "serverCommand": ["uvx", "--from", "git+https://github.com/oraios/serena", "serena", "start-mcp-server", "--context", "ide-assistant", "--enable-web-dashboard", "false"]
       }
     ]
   }
   ```

   **확인 방법:**
   ```bash
   claude mcp list
   ```

### 로컬 패키징으로 설치

1. 스킬을 패키징하여 `.zip` 파일 생성:
   ```bash
   python3 ~/.claude/plugins/marketplaces/anthropic-agent-skills/skill-creator/scripts/package_skill.py analyze-issue
   ```

2. Claude Code에서 설치:
   ```bash
   /plugin install analyze-issue.zip
   ```

## 📦 Available Skills

### analyze-issue (v3.5.0 Updated)

버그와 이슈의 근본 원인을 체계적으로 분석하는 스킬입니다.

**주요 기능:**
- JIRA 이슈 및 Sentry 에러 조사
- 다각도 가설 수립 및 검증
- 코드베이스 탐색을 통한 문제 지점 파악
- 상세한 분석 리포트 자동 생성 (`*_REPORT.md`)

**v3.5.0 변경사항:**
- ⚠️ **브랜치 보호**: main/master/staging 브랜치 감지 시 새 feature 브랜치 자동 생성
- 🔧 **Worktree 제거**: 복잡한 Worktree 로직 제거, 브랜치 워크플로우로 단순화

**사용 시점:**
- JIRA 이슈나 버그 리포트 분석 시
- Sentry 에러나 프로덕션 인시던트 조사 시
- 복잡한 문제의 근본 원인 분석이 필요할 때

**설치:**
```bash
# Claude Code에 스킬 설치
/plugin install analyze-issue.zip
```

### mr-code-review (v3.6.0 Updated)

GitLab MR의 코드 변경사항을 분석하여 맥락 기반 종합 리뷰를 수행하는 스킬입니다.

**주요 기능:**
- **7가지 종합 검증**: 아키텍처, 비즈니스 로직, 컨벤션, 이슈 패턴, JIRA 요구사항, 보안, 테스트
- **2개 파일 출력**: `INLINE_DISCUSSION.json` (GitLab 자동화용) + `SUMMARY_COMMENT.md` (요약)
- **범용 보안 스캔**: Trivy로 모든 언어 지원 (JS, Python, Go, Java, Rust 등)
- **Phase별 중간 산출물**: `.mr-review/` 디렉토리에 각 Phase 결과 저장 (Context 손실 방지)
- **MCP 기반 심화 분석**: Sequential Thinking + Serena Context7 + Atlassian 적극 활용

**사용 시점:**
- GitLab MR 코드 리뷰가 필요할 때
- 맥락 기반 종합 리뷰가 필요한 중요한 MR (아키텍처 변경, 신규 기능)
- 프로젝트 문서와 JIRA 요구사항을 종합 검증해야 할 때
- 보안, 품질, 테스트 커버리지를 체계적으로 검증하고 싶을 때

**사용 방법:**
```bash
# 로컬에서 직접 실행
claude-code exec "Use mr-code-review skill to review this MR. Branch: feature/user-auth"

# 또는 대화형으로
# "mr-code-review skill로 이 MR 리뷰해줘"
```

**설치:**
```bash
# Claude Code에 스킬 설치
/plugin install mr-code-review.zip
```

### plan-builder (v3.5.0 Updated)

자동 반복 검토를 통해 고품질 구현 계획을 생성하는 스킬입니다.

⚠️ **v3.5.0 변경사항**: 브랜치 보호 (main/master/staging 경고 및 권장 워크플로우 안내)

⚠️ **v2.2.0 주요 개선**: 각 리뷰 iteration마다 **새로운 문제를 탐색**하여 계획 품질을 극대화합니다.

**주요 기능:**
- **명시적 WHILE 루프**: 계획 생성 → 검토 → 피드백 반영 무한 반복 (ZERO 이슈까지)
- **엄격한 품질 기준**: "Approve"는 ZERO 이슈일 때만 가능 (Good ≠ Strong)
- **버전 추적**: 각 반복마다 `*_PLAN_REVIEW_v[N].md` 파일 생성 및 보존
- **CARRYOVER/NEW 태깅**: 이전 이슈 추적 + 새로 발견한 이슈 구분
- **Fresh Exploration**: 매 iteration마다 전체 체크리스트를 처음부터 재적용
- **자동 Iteration**: 사용자 개입 없이 ZERO 이슈까지 자동 반복
- 모든 태스크에 테스팅 전략 필수 포함
- 태스크 독립성 검증

**v2.2.0 변경사항 (2025-12-10)**:
- ✅ **Step A (Review) 6단계 프로세스로 강화**:
  - Step 1: 이전 리뷰 읽기 (피드백 적용 확인)
  - Step 3: FULL FRESH Critical Review (MANDATORY - 전체 체크리스트 재적용)
  - Step 4: CARRYOVER/NEW 이슈 태깅 (진행 추적)
- ✅ **review_checklist.md 강화**: "MANDATORY: Apply FULL checklist EVERY TIME" 명시
- ✅ **자동 iteration 강제**: Step D에서 사용자 확인 없이 자동으로 다음 iteration 실행
- ✅ **CRITICAL INSTRUCTION 블록**: "DO NOT assume", "LOOK FOR NEW PROBLEMS" 명시적 지시
- 🎯 **결과**: 각 iteration에서 새로운 유형의 문제 발견 보장 (Testing Strategy → Task Independence → Edge Cases...)

**v1.6.0 변경사항 (2025-12-09)**:
- ✅ Phase 2를 WHILE 루프 구조로 완전 재작성
- ✅ "Approve with Changes" 제거 → Binary decision (Approve / Needs Iteration)
- ✅ 리뷰 파일 버전 추적 메커니즘 추가
- ✅ Loop 다이어그램 및 테스트 시나리오 추가
- ⚠️ 이전보다 더 많은 반복이 발생할 수 있으나, 계획 품질이 크게 향상됨

**사용 시점:**
- `*_REPORT.md`에서 구현 계획 생성 시
- 복잡한 기능이나 아키텍처 변경 계획 시
- 실행 전 고신뢰도 계획이 필요할 때
- 품질과 완성도가 속도보다 중요할 때

**설치:**
```bash
# Claude Code에 스킬 설치
/plugin install plan-builder.zip
```

### execute-plan (v3.5.0 Updated)

승인된 구현 계획을 체계적으로 실행하는 스킬입니다.

**주요 기능:**
- TodoList 자동 생성 및 진행 추적
- 8단계 체계적 실행 프로세스
- 자동 테스트 실행 및 검증
- 코드 문서화 및 Serena 메모리 저장
- **순수 구현에만 집중** (문서 정리는 document 스킬에서 처리)

**v3.5.0 변경사항:**
- ⚠️ **브랜치 보호**: main/master/staging 브랜치 경고 (코드 수정 위험 강조)
- 🔧 **Worktree 제거**: 복잡한 Worktree 로직 제거, 브랜치 워크플로우로 단순화

**사용 시점:**
- 승인된 `*_PLAN.md` 파일 실행 시
- 체계적인 진행 추적이 필요할 때
- 모든 성공 기준 검증이 필요할 때
- 코드 구현에만 집중하고 싶을 때

**Note**: 문서 업데이트와 파일 정리는 `document` 스킬에서 처리합니다.

**설치:**
```bash
# Claude Code에 스킬 설치
/plugin install execute-plan.zip
```

## 🔧 Available Agents (v3.0.0)

**v3.0.0**: Agent는 Skills에서 실제로 활용되는 것만 유지합니다. 기존 4개 Agent(code-refactorer, test-generator, code-reviewer, performance-analyzer)는 Skills의 Phase에 직접 통합되었습니다.

### requirement-validator (유일하게 유지)

JIRA Acceptance Criteria와 코드를 자동 매핑하여 요구사항 달성 여부를 검증합니다.

**주요 기능:**
- AC ↔ 코드 자동 매핑
- 4가지 실행 모드 (Reverse, Pre, Post, Final)
- 미구현 AC 자동 탐지
- 전체 워크플로우 AC traceability

**사용 시나리오:**
```bash
# 1. 자동 호출 (Skills 통합)
# - analyze-issue에서 자동 호출 (AC 역추적)
# - plan-builder에서 자동 호출 (AC coverage 체크)
# - execute-plan에서 자동 호출 (AC 달성 보고)
# - mr-code-review에서 자동 호출 (AC 최종 게이트)

# 2. 수동 호출 (Agent 직접 실행)
# Mode 1: 특정 코드가 어떤 AC와 관련되었는지 역추적
"requirement-validator agent로 UserService.ts의 login 함수가 어떤 AC와 관련있는지 찾아줘"

# Mode 2: 계획이 모든 AC를 커버하는지 사전 검증
"requirement-validator agent Mode 2로 FEATURE_PLAN.md의 AC coverage 체크해줘"

# Mode 3: 현재 구현이 AC를 얼마나 충족하는지 확인
"requirement-validator agent Mode 3로 현재 git diff 기준 AC 달성률 보고해줘"

# Mode 4: MR이 AC를 충족하는지 최종 검증
"requirement-validator agent Mode 4로 이 MR이 JIRA-123 AC를 모두 달성했는지 확인해줘"
```

**통합 Skills**: analyze-issue, plan-builder, execute-plan, mr-code-review

---

### mcp-config

workflow-skills 플러그인의 MCP 서버를 쉽게 활성화/비활성화하는 스킬입니다.

**주요 기능:**
- MCP 서버 상태 조회 (8개 MCP 한눈에 확인)
- 단일/다중/전체 MCP 비활성화
- 비활성화된 MCP 활성화 (리셋)
- `settings.local.json` 자동 관리

**사용 시점:**
- MCP 서버 상태를 확인하고 싶을 때
- 특정 MCP 서버를 임시로 비활성화하고 싶을 때
- 비활성화된 MCP를 다시 활성화하고 싶을 때

**사용 예시:**
```bash
# MCP 상태 조회
"MCP 상태 보여줘"

# 특정 MCP 비활성화
"sentry 비활성화해줘"
"atlassian이랑 serena 끄기"

# MCP 활성화
"sentry 활성화해줘"
"모든 MCP 활성화"  # 리셋
```

**설치:**
```bash
/plugin install mcp-config
```

---

### document (v3.5.0 Updated)

워크플로우 아티팩트를 수집하여 프로젝트 문서를 종합적으로 업데이트하는 스킬입니다.

**주요 기능:**
- 10단계 체계적 문서화 프로세스
- **README, CHANGELOG, CLAUDE 문서 자동 업데이트**
- **JIRA 이슈에 구현 완료 사항 정리 및 코멘트**
- Serena 메모리에 기술 인사이트 저장
- 워크플로우 아티팩트 아카이브/정리
- Keep a Changelog 형식 준수

**v3.5.0 변경사항:**
- ⚠️ **브랜치 보호**: main/master/staging 브랜치 경고 (문서 커밋 위험)
- 🔧 **Worktree 제거**: Worktree 정리 로직 제거, 브랜치 워크플로우로 단순화
- 💾 **Git 커밋/푸시**: 옵션으로 유지 (Phase 9)

**사용 시점:**
- **`execute-plan` 완료 후 반드시 실행** (README/CHANGELOG 업데이트)
- 프로젝트 문서화가 필요한 경우
- 아키텍처 결정사항 문서화 시
- 마이그레이션 가이드 생성 시
- git commit 전 최종 문서화

**설치:**
```bash
# Claude Code에 스킬 설치
/plugin install document.zip
```

**워크플로우 통합:**
```
analyze-issue
  → *_REPORT.md 생성
  → plan-builder (자동 반복 검토)
    └─> *_PLAN.md (승인된 계획)
  → execute-plan (코드 구현 및 테스트)
    └─> 구현 완료, 코드 문서화, 파일 정리
  → document (필수: 프로젝트 문서화)
    └─> README, CHANGELOG, CLAUDE 문서, Serena 메모리
```

## 📋 권장 워크플로우

### 표준 워크플로우 (v3.0.0)

```
1. analyze-issue [JIRA/버그 리포트]
   └─> *_REPORT.md 생성
   └─> [Phase 3D] 복잡도 분석 및 리팩토링 가이드 직접 제공 (조건부 필수)
   └─> [Phase 3E] requirement-validator (AC 역추적)

2. plan-builder [REPORT 참조]
   └─> 자동 반복 검토 (계획 → 검토 → 개선 → 재검토...)
   └─> [Step C-2] requirement-validator (AC coverage 검증)
   └─> *_PLAN.md (승인된 고품질 계획)

3. execute-plan [PLAN]
   └─> TodoList 생성 및 실행
   └─> [Phase 4] 코드 구현
   └─> [Phase 4C] DB Migration 검증 (마이그레이션 작업 시)
   └─> [Phase 5] 테스트 직접 생성 - AAA 패턴 (조건부 필수)
   └─> [Phase 6] requirement-validator (AC 달성 보고)
   └─> [Phase 7] 테스트 실행 및 검증

4. document (필수)
   └─> README 업데이트 (기능, API, 설정 등)
   └─> CHANGELOG 업데이트 (변경 이력)
   └─> CLAUDE 문서 업데이트 (아키텍처 결정사항)
   └─> Serena 메모리 저장 (기술 인사이트)
   └─> JIRA 이슈 업데이트
   └─> 워크플로우 아티팩트 정리 (*_PLAN.md, *_REPORT.md)
```

**v3.0.0 변경사항**:
- ✅ **Skills 자립성 강화**: Agent 의존 없이 Skills가 직접 기능 수행
- ✅ **Phase 3D 강화**: 복잡도 분석 + 리팩토링 가이드 직접 제공 (code-refactorer 통합)
- ✅ **Phase 5 강화**: AAA 패턴으로 테스트 직접 생성 (test-generator 통합)
- ✅ **Agent 축소**: 5개 → 1개 (requirement-validator만 유지)
- ✅ **Dead Code 제거**: 72% → 0%

**중요**:
- `plan-builder`는 자동으로 피드백 루프를 반복하여 고품질 계획을 보장합니다
- `execute-plan`은 7-Phase 구조로 체계적입니다
- `execute-plan`은 코드 구현에, `document`는 문서화에 집중하도록 역할이 분리되어 있습니다
- 완전한 워크플로우를 위해서는 두 단계를 모두 실행해야 합니다

### MR 리뷰 워크플로우 (v3.6.0)

```
mr-code-review [Branch/MR URL]
├─> Phase 1: 맥락 수집 → .mr-review/1_CONTEXT.md
├─> Phase 2: 코드 분석 → .mr-review/2_CODE_ANALYSIS.md
├─> Phase 3: 보안 분석 (Trivy) → .mr-review/3_SECURITY_ANALYSIS.md
└─> Phase 4: 리포트 생성
    ├─> INLINE_DISCUSSION.json (GitLab Inline Discussion용)
    └─> SUMMARY_COMMENT.md (요약 리포트)

7가지 종합 검증:
1. 아키텍처 일관성
2. 비즈니스 로직 정확성 (JIRA 목표 대비)
3. 컨벤션 준수
4. 과거 이슈 패턴 재발 방지
5. JIRA 요구사항 검증
6. 보안 검토
7. 테스트 커버리지
```


## 📦 Marketplace Distribution

이 저장소는 **Claude Code Marketplace**로 배포되어 있습니다.

### 마켓플레이스 설정

마켓플레이스 구성은 [.claude-plugin/marketplace.json](.claude-plugin/marketplace.json)에 정의되어 있습니다:

```json
{
  "name": "wogus-plugins",
  "owner": {
    "name": "94wogus",
    "email": "94wogus@quantit.io"
  },
  "metadata": {
    "description": "체계적인 개발 워크플로우를 위한 Claude Code 스킬 모음 + Agents - 이슈 분석, 계획 수립, MR 리뷰, 실행, 문서화, AC 요구사항 추적, MCP 자동 통합 (한국어 기본)",
    "version": "3.6.0",
    "repository": "https://github.com/94wogus-quantit/wogus-plugin",
    "homepage": "https://github.com/94wogus-quantit/wogus-plugin#readme",
    "license": "Private"
  },
  "plugins": [
    {
      "name": "workflow-skills",
      "description": "체계적인 개발 워크플로우를 위한 통합 솔루션 (Skills + Agents)",
      "source": "./",
      "skills": [
        "./analyze-issue",
        "./plan-builder",
        "./execute-plan",
        "./document",
        "./mr-code-review"
      ]
    }
  ]
}
```

### 마켓플레이스 사용 방법

**사용자 입장:**

1. 마켓플레이스 추가:
   ```bash
   /marketplace add git@github.com:94wogus-quantit/wogus-plugin.git
   ```

2. 사용 가능한 스킬 확인:
   ```bash
   /marketplace list
   ```

3. 원하는 스킬 설치:
   ```bash
   /plugin install workflow-skills:analyze-issue
   /plugin install workflow-skills:plan-builder
   # 또는 짧은 형식
   /plugin install analyze-issue
   ```

**배포자 입장:**

1. **GitHub Public 저장소 설정**
   - 저장소를 public으로 설정
   - `.claude-plugin/marketplace.json` 파일 작성
   - 스킬 소스 디렉토리 구조 유지

2. **버전 관리**
   - `marketplace.json`의 `metadata.version` 업데이트
   - 변경사항 커밋 및 푸시
   - 사용자는 마켓플레이스 갱신으로 최신 버전 확인 가능

3. **스킬 추가/수정**
   ```bash
   # 새 스킬 생성
   python3 ~/.claude/.../init_skill.py new-skill --path .

   # marketplace.json의 skills 배열에 추가
   # "skills": [..., "./new-skill"]

   # Git 커밋 및 푸시
   git add .
   git commit -m "feat: add new-skill"
   git push
   ```

### 마켓플레이스 vs 로컬 패키징

| 방식 | 장점 | 단점 |
|------|------|------|
| **Marketplace** | ✅ 자동 업데이트<br>✅ 중앙 관리<br>✅ 간편한 설치 | ⚠️ GitHub 의존성<br>⚠️ Public 저장소 필요 |
| **로컬 패키징** | ✅ 오프라인 가능<br>✅ 버전 고정 | ⚠️ 수동 업데이트<br>⚠️ 패키징 필요 |

**권장**: 개인/팀 사용은 Marketplace, 특정 버전 고정이 필요한 경우 로컬 패키징 사용

## 📁 Repository Structure

```
wogus-plugin/  (v3.6.0)
├── .claude-plugin/         # Marketplace 설정
│   ├── marketplace.json    # 플러그인 목록 및 메타데이터
│   └── plugin.json         # Plugin manifest (Skills + Agents)
│
├── agents/                 # Agent 정의 파일 (v3.0.0: 1개만 유지)
│   └── requirement-validator.md  # AC traceability (유일하게 유지)
│
├── analyze-issue/          # 이슈 분석 스킬
│   ├── SKILL.md           # [업데이트] Phase 3D 추가
│   └── references/
│       ├── report_template.md
│       └── common_bug_patterns.md
│
├── mr-code-review/        # MR 코드 리뷰 스킬 (v3.6.0)
│   ├── SKILL.md           # 7가지 검증, 4-Phase 워크플로우
│   └── references/
│       ├── inline_discussion_template.json  # GitLab 자동화용 JSON
│       ├── summary_discussion_template.md   # 요약 리포트 템플릿
│       └── verification_guides/
│           ├── architecture_check.md
│           ├── business_logic_check.md      # NEW: 비즈니스 로직 검증
│           ├── convention_check.md
│           ├── known_issues_check.md
│           ├── jira_validation.md
│           ├── security_review.md
│           └── test_coverage.md
│
├── plan-builder/          # 계획 생성 스킬
│   ├── SKILL.md
│   └── references/
│       ├── plan_template.md
│       ├── review_checklist.md
│       ├── testing_strategy_guide.md
│       └── task_independence_guide.md
│
├── execute-plan/          # 계획 실행 스킬
│   └── SKILL.md           # [업데이트] 6-Phase → 7-Phase
│
├── document/              # 문서화 스킬
│   └── SKILL.md
│
├── mcp-config/            # MCP 설정 스킬 (NEW v3.1.0)
│   ├── SKILL.md           # MCP 활성화/비활성화 자동화
│   └── references/
│       └── settings_template.json
│
├── .gitignore            # Git 제외 설정
├── CHANGELOG.md          # 변경 이력
├── CLAUDE.md             # Claude Code 가이드
└── README.md             # 이 파일
```

## 🛠 Development

### 새로운 스킬 만들기

```bash
# 1. skill-creator로 템플릿 생성
python3 ~/.claude/plugins/marketplaces/anthropic-agent-skills/skill-creator/scripts/init_skill.py <skill-name> --path .

# 2. 스킬 커스터마이징 (SKILL.md, references 등 편집)

# 3. 패키징 (배포용 .zip 생성)
python3 ~/.claude/plugins/marketplaces/anthropic-agent-skills/skill-creator/scripts/package_skill.py <skill-folder>
```

### 스킬 구조

```
skill-name/
├── SKILL.md (required)      # 메타데이터 + 사용 가이드
├── scripts/ (optional)      # 실행 가능한 스크립트
├── references/ (optional)   # 참조 문서
└── assets/ (optional)       # 템플릿, 에셋
```

### Git 워크플로우

**버전 관리 대상:**
- ✅ 스킬 소스 디렉토리 (`analyze-issue/`, `plan-builder/` 등)
- ✅ 문서 파일 (`CLAUDE.md`, `README.md`)
- ✅ `.gitignore`

**제외 항목** (`.gitignore`로 관리):
- ❌ `.zip` 파일 (빌드 결과물)
- ❌ `.claude/` (개인 설정)
- ❌ IDE 설정, 로그, 캐시 등

**워크플로우:**
```bash
# 소스만 커밋
git add analyze-issue/ plan-builder/
git commit -m "feat: add new skill"

# 배포는 로컬에서 패키징
python3 ~/.claude/.../package_skill.py analyze-issue
/plugin install analyze-issue.zip
```

## 📝 License

개인 사용을 위한 저장소입니다.
