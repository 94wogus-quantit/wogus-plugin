# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Personal plugin collection repository containing Claude Code Skills, Agents, and custom commands for systematic software development workflows.

**Key Artifacts (v3.2.1):**
- **Skills**: Workflow orchestrators for multi-step processes (분석, 계획, 실행, 문서화)
- **Agents**: AC (Acceptance Criteria) traceability (requirement-validator만 유지)
- **Custom Commands**: Workflow automation commands (별도 설치)
- **Reference Materials**: Templates and pattern catalogs

## Repository Structure

```
wogus-plugin/  (v3.2.1)
├── .claude-plugin/         # Plugin configuration
│   ├── marketplace.json    # Marketplace metadata
│   └── plugin.json         # Plugin manifest (Skills + Agents)
│
├── agents/                 # Agent definitions (v3.0.0: 1개만 유지)
│   └── requirement-validator.md  # AC traceability (유일하게 유지)
│
├── <skill-name>/          # Each skill is a directory
│   ├── SKILL.md          # Required: Metadata + instructions
│   ├── references/       # Optional: Reference documentation
│   ├── scripts/          # Optional: Executable code
│   └── assets/           # Optional: Templates and assets
│
├── CLAUDE.md            # This file
├── README.md            # User-facing documentation
└── CHANGELOG.md         # Version history
```

## Available Skills

### analyze-issue

Systematic root cause analysis for bugs and issues.

**When to use**: Analyzing JIRA issues, Sentry errors, or investigating bug reports
**Output**: `[ISSUE_ID]_REPORT.md` with root cause, affected code, and recommendations
**Integration**: First step in `/analyze-issue → /plan → /execute-plan → /document` workflow

### mcp-config (v3.1.0 NEW)

workflow-skills 플러그인의 MCP 서버를 활성화/비활성화합니다.

**When to use**: MCP 서버 상태 확인, 특정 MCP 비활성화/활성화
**Trigger phrases**: "MCP 상태 보여줘", "sentry 비활성화해줘", "atlassian 끄기", "모든 MCP 활성화"
**Configuration file**: `.claude/settings.local.json`의 `deniedMcpServers` 배열 관리

## Available Agents (v3.0.0)

**What are Agents?**

Agents are specialized automation tools that focus on specific technical tasks. v3.0.0에서는 Skills에서 실제로 활용되는 Agent만 유지하고, 나머지는 Skills의 Phase에 직접 통합했습니다.

### requirement-validator (유일하게 유지)

JIRA Acceptance Criteria와 코드를 자동 매핑하여 요구사항 달성 여부를 검증합니다.

**4가지 실행 모드**:
- **Mode 1 (Reverse)**: 코드 → AC 역매핑 (analyze-issue)
- **Mode 2 (Pre)**: 계획 → AC coverage (plan-builder)
- **Mode 3 (Post)**: git diff → AC 구현 확인 (execute-plan)
- **Mode 4 (Final)**: MR → AC 최종 게이트 (mr-code-review)

**Integration**: 4개 Skills에서 자동 호출 (analyze-issue, plan-builder, execute-plan, mr-code-review)

### v3.0.0에서 삭제된 Agents (Skills에 통합)

| Agent | 통합 위치 | 비고 |
|-------|----------|------|
| code-refactorer | analyze-issue Phase 3D | 복잡도 분석 + 리팩토링 가이드 직접 제공 |
| test-generator | execute-plan Phase 5 | AAA 패턴, 테스트 케이스 직접 생성 |
| code-reviewer | 삭제 | Skills에서 미사용, 독립 호출 ROI 낮음 |
| performance-analyzer | 삭제 | Skills에서 미사용, 독립 호출 ROI 낮음 |

## Skills vs Agents (v3.0.0)

| Aspect | Skills | Agents |
|--------|--------|--------|
| **Purpose** | Orchestrate multi-step workflows | AC (Acceptance Criteria) traceability |
| **Scope** | Broad (analysis → plan → execution) | Narrow (AC 추적 전용) |
| **File Format** | `SKILL.md` in skill directory | `.md` files in `agents/` |
| **Invocation** | User explicitly uses skill | Skills call Agents automatically |
| **Examples** | analyze-issue, plan-builder | requirement-validator |
| **Count** | 6개 (v3.1.0) | 1개 |

## AC Traceability (요구사항 추적)

requirement-validator agent를 통해 전체 워크플로우에서 JIRA Acceptance Criteria를 추적할 수 있습니다.

### 워크플로우별 AC 활용

```
JIRA-123: "사용자 이메일 로그인"
├─ AC#1: 이메일 validation
├─ AC#2: 5회 실패 시 계정 잠금
└─ AC#3: JWT 토큰 발급

1. analyze-issue (버그 분석)
   → requirement-validator Mode 1 (Reverse Tracing)
   → "이 버그는 AC#2 미충족"

2. plan-builder (계획 수립)
   → requirement-validator Mode 2 (Pre-validation)
   → "계획이 AC#1,2,3 모두 커버 ✅"

3. execute-plan (구현)
   → requirement-validator Mode 3 (Post-validation)
   → "AC#1 ✅, AC#2 ❌ 미구현"

4. mr-code-review (MR 리뷰)
   → requirement-validator Mode 4 (Final Gate)
   → "AC#2 미구현 → MR BLOCKED"
```

### requirement-validator Agent

**4가지 실행 모드**:
- **Mode 1 (Reverse)**: 코드 → AC 역매핑
- **Mode 2 (Pre)**: 계획 → AC coverage
- **Mode 3 (Post)**: git diff → AC 구현 확인
- **Mode 4 (Final)**: MR → AC 최종 게이트

**도구**:
- mcp__atlassian (JIRA AC 조회)
- mcp__sequential-thinking (체계적 분석)
- Read, Write, Grep, Glob, Bash (코드 매핑)

## Skill Development Workflow

### Creating New Skills

1. **Initialize**: Use skill-creator to generate template
   ```bash
   python3 ~/.claude/plugins/.../init_skill.py <skill-name> --path .
   ```

2. **Customize**: Edit `SKILL.md` with:
   - Accurate metadata (name, description)
   - Clear "When to Use" section
   - Step-by-step workflow instructions
   - Tool and MCP references
   - Best practices

3. **Add Resources** (optional):
   - `references/` - Documentation to load into context
   - `scripts/` - Executable code for deterministic tasks
   - `assets/` - Templates or files used in output

4. **Package**: Validate and create distribution package
   ```bash
   python3 ~/.claude/plugins/.../package_skill.py <skill-folder>
   ```

### Skill Writing Guidelines

**Metadata Quality:**
- `description` determines when Claude uses the skill
- Be specific about trigger scenarios
- Use third-person (not "you should")

**Instruction Style:**
- Use imperative/infinitive form (verb-first)
- "To accomplish X, do Y" not "You should do X"
- Objective, instructional language

**Progressive Disclosure:**
- Keep SKILL.md lean (<5k words)
- Move detailed info to `references/`
- Include grep patterns for large reference files

## Integration with Custom Commands

Skills work alongside custom commands in `~/.claude/commands/`:

**Workflow Commands:**
1. `/analyze-issue` - Root cause analysis → `*_REPORT.md`
2. `/plan` - Create execution plan → `*_PLAN.md`
3. `/plan-review` - Review plan quality → `*_REVIEW.md`
4. `/apply-review` - Apply feedback to plan
5. `/execute-plan` - Execute with TodoList tracking
6. `/document` - Final documentation and cleanup
7. `/security` - Security assessment

**Typical Workflow:**
```
/analyze-issue [JIRA] → /plan → /plan-review
  → /apply-review → /execute-plan → /document
```

## Agent Development Workflow (v2.0.0 NEW)

### Creating New Agents

1. **Create Agent Definition File**:
   ```bash
   # Create .md file in agents/ directory
   touch agents/new-agent.md
   ```

2. **Write YAML Frontmatter**:
   ```yaml
   ---
   name: new-agent
   description: 한국어로 Agent의 목적과 사용 시점을 명확히 설명 (description은 트리거 조건 결정)
   tools: Read, Write, Bash, Grep, Glob, mcp__serena, mcp__sequential-thinking
   model: sonnet
   ---
   ```

3. **Define Phase Structure**:
   - Phase-based workflow (4-5 phases recommended)
   - Each phase has clear inputs, process, outputs
   - Use MCP tools (serena, sequential-thinking, context7)
   - All content in Korean (한국어 필수)

4. **Update plugin.json** (automatic - no manual action needed):
   - Agent files are automatically discovered from `agents/` directory
   - plugin.json's `"agents": "./agents"` handles discovery

### Agent Definition Structure

```markdown
---
name: agent-name
description: 명확한 한국어 설명 (트리거 조건 포함)
tools: Tool1, Tool2, mcp__server
model: sonnet
---

# Agent Name

## Phase 1: 분석
[Step-by-step instructions]

## Phase 2: 실행
[Detailed implementation steps]

## Phase 3: 검증
[Validation and testing]

## Phase 4: 보고
[Result reporting]
```

### Agent Writing Best Practices

**YAML Frontmatter Requirements**:
- `name`: Lowercase, hyphen-separated (e.g., `code-refactorer`)
- `description`: Korean, specific trigger conditions (e.g., "복잡도 > 10일 때 사용")
- `tools`: List all required tools (Read, Write, Bash, MCP servers)
- `model`: Usually `sonnet` for balanced performance

**Content Guidelines**:
- **All content in Korean** (한국어 필수 - descriptions, instructions, examples)
- Keep focused on single responsibility
- 4-5 phases maximum (avoid complexity)
- Include concrete code examples
- Reference specific thresholds (e.g., complexity > 10)

**Integration Points**:
- Document which Skills call this Agent
- Specify trigger conditions clearly
- Provide usage examples

### Agent Testing

Before committing new Agents:

1. **Validate YAML Frontmatter**:
   ```bash
   # Check frontmatter syntax
   head -10 agents/new-agent.md | grep -E "^name:|^description:|^tools:|^model:"
   ```

2. **Manual Testing**:
   - Test Agent invocation from Skills
   - Verify output format
   - Check Korean language compliance

3. **Integration Testing**:
   - Test Skills → Agent workflow
   - Verify TodoList tracking
   - Ensure proper error handling

## Development Best Practices

**For Skills:**
- Focus on procedural knowledge and domain expertise
- Reference bundled resources explicitly in SKILL.md
- Test with realistic scenarios before packaging
- Iterate based on actual usage

**For Agents (NEW):**
- Single responsibility principle (one task per Agent)
- Clear trigger conditions in description
- All content in Korean (mandatory)
- 4-5 phases maximum
- Concrete examples and thresholds

**For This Repository:**
- One skill per directory
- One agent per `.md` file in `agents/`
- Always package skills before distribution (Agents are included automatically)
- Document integrations with other skills/commands/agents
- Keep examples and templates up to date

## Marketplace Distribution

This repository is distributed as a **Claude Code Marketplace**.

### Configuration

Marketplace configuration is in `.claude-plugin/marketplace.json`:
- `name`: Marketplace identifier
- `owner`: Owner information
- `metadata.version`: Current version (semantic versioning)
- `plugins[].skills`: Array of skill directories to include

### Publishing Workflow (v2.0.0)

1. **Develop**: Create/modify skills or agents
   - Skills: Edit `<skill-name>/SKILL.md`
   - Agents: Edit `agents/<agent-name>.md`
2. **Update Version**: Increment `metadata.version` in `marketplace.json`
   - Also update `plugin.json` version if needed
3. **Commit & Push**: Push to GitHub public repository
4. **Users Update**: Users run `/marketplace refresh` to get latest version

### Version Management

```bash
# Update version in marketplace.json and plugin.json
# v1.6.0 → v2.0.0 (breaking change - added Agents)
# v2.0.0 → v2.1.0 (new feature)
# v2.0.0 → v2.0.1 (bug fix)

# Example: v1.6.0 → v2.0.0 (current version)
git add .claude-plugin/marketplace.json .claude-plugin/plugin.json
git commit -m "chore: bump version to v2.0.0"
git push
```

### Adding New Skills to Marketplace

```bash
# 1. Create new skill
python3 ~/.claude/.../init_skill.py new-skill --path .

# 2. Edit marketplace.json
# Add "./new-skill" to plugins[0].skills array

# 3. Commit and push
git add .claude-plugin/ new-skill/
git commit -m "feat: add new-skill to marketplace"
git push
```

### User Installation

Users install from marketplace:
```bash
# Add marketplace
/marketplace add git@github.com:94wogus-quantit/skills.git

# Install skills
/plugin install workflow-skills:analyze-issue
/plugin install plan-builder  # short form
```

## Common Tasks

**Package a skill (for local distribution):**
```bash
cd /path/to/skills
python3 ~/.claude/plugins/marketplaces/anthropic-agent-skills/skill-creator/scripts/package_skill.py ./analyze-issue
```

**Install a skill:**
```bash
# From marketplace (recommended)
/plugin install analyze-issue

# From local zip
/plugin install analyze-issue.zip
```

**Update marketplace version:**
```bash
# Edit .claude-plugin/marketplace.json
# Increment metadata.version
git commit -m "chore: bump version"
git push
```

**Validate skill structure:**
The packaging script automatically validates before creating the zip file.

## 아키텍처 결정사항

### 2025-12-10 - v3.2.1 amplitude MCP 환경 변수 전달 방식 수정

**컨텍스트**:
v3.2.0에서 amplitude MCP 서버를 추가할 때, v3.0.2의 보안 패턴("인증 정보는 args 대신 env 사용")을 따라 `env` 블록으로 API 키를 전달했습니다. 그러나 amplitude-mcp-server가 환경 변수를 인식하지 못해 연결 실패가 발생했습니다.

**문제점**:
- **amplitude MCP 연결 실패**: `claude mcp list`에서 `✗ Failed to connect`
- **원인 조사**: amplitude-mcp-server 및 대안 패키지들 모두 환경 변수 미지원
- **mcp-config 한계**: 하드코딩된 serverCommand 테이블로는 `${VAR}` 패턴 처리 불가

**결정**: args 방식 + 동적 serverCommand 생성

1. **amplitude MCP 설정 변경**:
   - **이전**: `env: { "AMPLITUDE_API_KEY": "${AMPLITUDE_API_KEY}" }`
   - **현재**: `args: ["--api-key", "${AMPLITUDE_API_KEY:-}"]`
   - `${VAR:-}` 문법으로 환경 변수 없을 때 빈 값 처리

2. **mcp-config skill 개선**:
   - 하드코딩된 serverCommand 테이블 → 동적 생성 방식
   - Phase 3에 "Step 1: serverCommand 동적 생성 (MANDATORY)" 추가
   - `${VAR}` 패턴을 실제 환경 변수 값으로 치환
   - 환경 변수 없으면 빈 문자열 `""`로 대체

**영향**:
- **amplitude MCP 정상 연결**: Claude Code 재시작 후 `✓ Connected`
- **mcp-config 유연성**: 동적 args 포함 MCP도 비활성화/활성화 가능
- **v3.0.2 보안 원칙 예외**: amplitude-mcp-server 한계로 args 사용 불가피
- **Breaking Change**: 없음

**대안**:
1. ~~다른 amplitude MCP 패키지 사용~~ → 모두 환경 변수 미지원
2. ~~amplitude MCP 삭제~~ → 기능 손실
3. ✅ **args 방식 + ${VAR:-} 문법** → 채택 (기능 유지 + 빈 값 처리)

**조사한 패키지**:
- `amplitude-mcp-server` (현재 사용): `--api-key` 플래그만 지원
- `amplitude-mcp`: `--amplitude-api-key` 플래그만 지원
- Amplitude 공식 Remote MCP: OAuth 2.0 (복잡한 인증)

**관련 파일**:
- [.claude-plugin/marketplace.json](.claude-plugin/marketplace.json) - amplitude 설정 변경
- [mcp-config/SKILL.md](mcp-config/SKILL.md) - 동적 serverCommand 로직

**버전**: v3.2.0 → v3.2.1

---

### 2025-12-10 - v3.2.0 MCP 서버 확장 (Terraform, Amplitude, Chrome DevTools)

**컨텍스트**:
v3.1.0까지 5개 MCP 서버(sequential-thinking, context7, serena, sentry, atlassian)만 포함되어 있었습니다. IaC 자동화(Terraform), 사용자 행동 분석(Amplitude), 브라우저 디버깅(Chrome DevTools) 통합이 필요했습니다.

**결정**: 3개 MCP 서버 추가

1. **terraform**:
   - HashiCorp 공식 Docker 이미지 사용
   - `hashicorp/terraform-mcp-server` (버전 미지정 → latest 자동 사용)
   - Terraform IaC 자동화 지원

2. **amplitude**:
   - 사용자 행동 분석 MCP
   - API 키를 `env` 블록으로 처리 (v3.0.2 보안 패턴 준수)
   - `AMPLITUDE_API_KEY` 환경 변수 필요

3. **chrome-devtools**:
   - Chrome DevTools 연동
   - 별도 인증 불필요

**영향**:
- **MCP 서버 총 8개**: 기존 5개 + terraform, amplitude, chrome-devtools
- **mcp-config 스킬 업데이트**: 8개 MCP 관리 지원
- **환경 변수 1개 추가**: `AMPLITUDE_API_KEY`
- **Breaking Change**: 없음 (새 기능 추가)

**관련 파일**:
- [.claude-plugin/marketplace.json](.claude-plugin/marketplace.json) - mcpServers에 3개 추가
- [mcp-config/SKILL.md](mcp-config/SKILL.md) - 8개 MCP 참조 테이블
- `~/.zshenv` - AMPLITUDE_API_KEY 환경 변수

**버전**: v3.1.0 → v3.2.0

---

### 2025-12-10 - v3.1.0 mcp-config Skill 추가

**컨텍스트**:
v3.0.3까지 MCP 서버 비활성화는 사용자가 직접 `.claude/settings.local.json` 파일을 편집하고 `deniedMcpServers` 배열에 정확한 `serverCommand`를 추가해야 했습니다. 이 과정이 복잡하고 오류가 발생하기 쉬웠습니다.

**문제점**:
- **복잡한 설정**: `serverCommand` 배열을 JSON.stringify하여 정확히 매칭해야 함
- **문서화 부족**: 각 MCP 서버의 정확한 `serverCommand` 값을 찾기 어려움
- **오류 발생**: 오타나 형식 오류로 비활성화 실패
- **상태 파악 어려움**: 어떤 MCP가 활성화/비활성화 상태인지 한눈에 확인 불가

**결정**: mcp-config Skill 추가

1. **4 Phase 워크플로우**:
   - Phase 1: 현재 상태 파악 (settings.local.json 읽기)
   - Phase 2: 사용자 요청 파싱 (자연어 → MCP ID)
   - Phase 3: 설정 파일 수정 (deniedMcpServers 업데이트)
   - Phase 4: 결과 확인 (상태 테이블 출력)

2. **MCP 매핑 테이블**:
   - 5개 MCP 서버의 정확한 `serverCommand` 값 문서화
   - ID로 간편하게 참조 가능 (예: "sentry 비활성화해줘")

3. **자동 파일 생성**:
   - `settings.local.json` 없을 때 기본 템플릿 생성
   - `references/settings_template.json` 참조

**영향**:
- **사용자 경험 개선**: 자연어로 MCP 관리 가능
- **오류 감소**: 정확한 `serverCommand` 자동 처리
- **상태 가시성**: 모든 MCP 상태를 테이블로 확인
- **Breaking Change**: 없음 (새 기능 추가)

**대안**:
1. ~~README에 수동 설정 방법만 문서화~~ → 복잡성 그대로, 오류 발생 가능
2. ~~marketplace.json에 비활성화 옵션 추가~~ → plugin 구조 변경 필요, 유연성 부족
3. ✅ **mcp-config Skill** → 채택 (자동화 + 사용자 친화적)

**관련 파일**:
- [mcp-config/SKILL.md](mcp-config/SKILL.md) - Skill 정의
- [mcp-config/references/settings_template.json](mcp-config/references/settings_template.json) - 설정 템플릿
- [.claude-plugin/marketplace.json](.claude-plugin/marketplace.json) - v3.1.0, skills 배열에 추가

**패턴**: 자연어 인터페이스 Skill
- 복잡한 설정 작업을 자연어로 처리
- 내부적으로 정확한 JSON 조작 수행
- 결과를 사용자 친화적인 형식으로 출력

**버전**: v3.0.3 → v3.1.0

---

### 2025-12-10 - v3.0.2 MCP 서버 환경 변수 통합

**컨텍스트**:
v3.0.1에서 MCP 서버들이 `args`에 직접 API 키와 토큰을 포함하고 있어 프로세스 목록(`ps aux`)에서 민감 정보가 노출될 수 있었습니다.

**문제점**:
- **보안 리스크**: `--api-key`, `--access-token` 등이 프로세스 args에 노출
- **일관성 부족**: 일부 MCP는 args, 일부는 env 사용
- **args 복잡성**: 인증 정보로 인해 args 배열이 비대해짐

**결정**: 모든 인증 정보를 `env` 블록으로 이동

1. **context7**:
   - **이전**: `args: ["-y", "@upstash/context7-mcp", "--api-key", "${CONTEXT7_API_KEY}"]`
   - **현재**: `args: ["-y", "@upstash/context7-mcp"]`, `env: { "CONTEXT7_API_KEY": "..." }`

2. **sentry**:
   - **이전**: `args: [..., "--access-token=${SENTRY_ACCESS_TOKEN}", "--host=quantit-io.sentry.io"]`
   - **현재**: `args: ["-y", "@sentry/mcp-server@latest"]`, `env: { "SENTRY_ACCESS_TOKEN", "SENTRY_HOST", "OPENAI_API_KEY" }`

3. **환경 변수 1개 추가**:
   - `SENTRY_HOST`: Sentry 인스턴스 호스트 (예: `quantit-io.sentry.io`)

**영향**:
- **보안 강화**: 프로세스 args에 토큰 노출 방지
- **일관성 향상**: 모든 MCP 서버가 `env` 블록 사용
- **args 간소화**: 패키지 이름과 필수 플래그만 유지
- **Breaking Change**: 없음 (환경 변수 추가만)

**대안**:
1. ~~args에 토큰 유지~~ → 보안 리스크 지속
2. ~~환경 변수 없이 직접 값 사용~~ → 하드코딩, public repo 노출
3. ✅ **env 블록으로 통합** → 채택 (보안 + 일관성)

**관련 파일**:
- [.claude-plugin/marketplace.json:35-66](.claude-plugin/marketplace.json#L35-L66) - MCP 서버 설정
- `~/.zshenv` - SENTRY_HOST 환경 변수 추가

**재발 방지**:
- MCP 서버 추가 시 인증 정보는 항상 `env` 블록 사용
- `args`에는 패키지 이름과 필수 플래그만 포함
- 민감 정보는 절대 args에 직접 넣지 않기

**버전**: v3.0.1 → v3.0.2

---

### 2025-12-10 - v3.0.1 Atlassian MCP Docker 기반 API 토큰 인증으로 변경

**컨텍스트**:
v2.4.0에서 추가된 Atlassian MCP 서버가 `mcp-remote` + OAuth 방식을 사용하고 있어, 브라우저 인증이 반복적으로 필요했습니다. OAuth 토큰이 만료될 때마다 재인증이 필요해 사용자 경험이 불편했습니다.

**문제점**:
- **반복적 OAuth 인증**: 토큰 만료 시마다 브라우저에서 재인증 필요
- **사용자 경험 저하**: Claude Code 세션 중 인증 팝업으로 작업 흐름 중단
- **자동화 제한**: 비대화형 환경에서 OAuth 인증 불가능

**결정**: Docker 기반 API 토큰 인증 방식으로 변경

1. **인증 방식 변경**:
   - **이전**: `npx mcp-remote https://mcp.atlassian.com/v1/sse` (OAuth)
   - **현재**: `docker run ghcr.io/sooperset/mcp-atlassian:latest` (API 토큰)

2. **환경 변수 3개 추가**:
   - `ATLASSIAN_URL`: Atlassian 인스턴스 URL
   - `ATLASSIAN_USERNAME`: 사용자 이메일
   - `ATLASSIAN_API_TOKEN`: API 토큰

3. **Docker 환경 변수 전달**:
   - `-e JIRA_URL`, `-e JIRA_USERNAME`, `-e JIRA_API_TOKEN`
   - `-e CONFLUENCE_URL`, `-e CONFLUENCE_USERNAME`, `-e CONFLUENCE_API_TOKEN`

**영향**:
- **사용자 경험 개선**: OAuth 반복 인증 불필요
- **자동화 가능**: 비대화형 환경에서도 작동
- **보안 유지**: API 토큰은 환경 변수로 처리, 하드코딩 없음
- **의존성 추가**: Docker 설치 필요

**대안**:
1. ~~OAuth 방식 유지~~ → 반복 인증으로 사용자 경험 저하
2. ~~Personal Access Token (PAT) 직접 사용~~ → mcp-remote가 PAT 미지원
3. ✅ **Docker 기반 mcp-atlassian** → 채택 (API 토큰 + 환경 변수)

**관련 파일**:
- [.claude-plugin/marketplace.json:74-97](.claude-plugin/marketplace.json#L74-L97) - atlassian MCP 설정
- [README.md:66-80](README.md#L66-L80) - 환경 변수 설정 가이드
- `~/.zshenv` - 환경 변수 저장

**재발 방지**:
- MCP 서버 추가 시 인증 방식의 사용자 경험 고려
- OAuth보다 API 토큰 방식 선호 (자동화 친화적)
- 환경 변수로 민감 정보 처리

**버전**: v3.0.0 → v3.0.1

---

### 2025-12-10 - v3.0.0 Agents 시스템 축소 리팩토링

**컨텍스트**:
v2.4.0에서 5개의 Agents가 존재했지만, Skills에서 실제로 활용되는 Agent는 1개(requirement-validator)뿐이었습니다. 나머지 4개(code-refactorer, test-generator, code-reviewer, performance-analyzer)는 외부에서 단독 호출용으로만 존재하고, Skills 내부에서 호출되지 않아 72%의 dead code로 판단되었습니다.

**문제점**:
- **Dead Code**: 5개 Agent 중 4개가 Skills에서 미사용 (72%)
- **과잉 엔지니어링**: 단독 호출용 Agent들이 복잡한 Phase 구조로 작성됨
- **일관성 부족**: Skills와 Agents 간 통합 패턴 불명확
- **유지보수 부담**: 사용하지 않는 Agent 파일들 관리 필요

**결정**: Agents 시스템 축소 (5개 → 1개) + Skills에 핵심 로직 통합

1. **삭제 대상 (4개)**:
   - `code-reviewer.md` (미사용 - Skills에서 호출 없음)
   - `performance-analyzer.md` (미사용 - Skills에서 호출 없음)
   - `code-refactorer.md` (analyze-issue Phase 3D에 통합)
   - `test-generator.md` (execute-plan Phase 5에 통합)

2. **유지 대상 (1개)**:
   - `requirement-validator.md` (4개 Skills에서 활발히 사용)

3. **Skills 강화**:
   - **analyze-issue Phase 3D**: "선택적" → "조건부 필수", code-refactorer 핵심 로직 직접 통합
     - Sequential Thinking 패턴으로 복잡도 분석
     - Cyclomatic complexity > 10, 함수 길이 > 50줄 기준
     - Extract Method/Extract Class 리팩토링 가이드
   - **execute-plan Phase 5**: "선택적" → "조건부 필수", test-generator 핵심 로직 직접 통합
     - AAA 패턴 (Arrange-Act-Assert) 예시
     - Given/When/Then BDD 스타일 예시
     - Happy path/Edge cases/Error handling 분류

**영향**:
- **Breaking Change**: v2.4.0 → v3.0.0 (Agent 참조 변경)
- **Dead Code 제거**: 72% → 0% (4개 Agent 삭제)
- **Skills 자립성**: Agent 의존 없이 직접 기능 수행
- **유지보수 간소화**: 관리 대상 Agent 5개 → 1개

**대안**:
1. ~~전체 Agents 유지~~ → Dead code 72% 지속, 유지보수 부담
2. ~~전체 Agents 삭제~~ → requirement-validator의 AC 추적 기능 손실
3. ✅ **선택적 축소 + 핵심 통합** → 채택 (효율성 + 기능 보존)

**패턴**: Agent → Skill 인라인 통합
- 외부 단독 호출용 Agent는 제거 (ROI 낮음)
- 핵심 로직은 Skills의 Phase에 직접 통합
- AC 추적처럼 여러 Skills에서 공유하는 기능만 Agent로 유지

**관련 파일**:
- [.claude-plugin/marketplace.json](.claude-plugin/marketplace.json) - agents 배열 1개로 축소
- [analyze-issue/SKILL.md](analyze-issue/SKILL.md) - Phase 3D 강화
- [execute-plan/SKILL.md](execute-plan/SKILL.md) - Phase 5 강화

**재발 방지**:
- 새 Agent 추가 전 "Skills에서 실제로 호출되는가?" 검토 필수
- 단독 호출 전용이면 Agent 대신 Skills Phase로 구현
- Agent는 여러 Skills에서 공유할 때만 생성

**버전**: v2.4.0 → v3.0.0

---

### 2025-12-10 - v2.4.0 MCP 서버 확장 (Sentry + Atlassian)

**컨텍스트**:
v2.3.0에서 MCP 서버 자동 통합 기능을 도입했지만, 3개 MCP 서버(sequential-thinking, context7, serena)만 포함되어 있었습니다. 에러 트래킹(Sentry)과 프로젝트 관리(JIRA) 통합이 수동으로 이루어져야 했습니다.

**문제점**:
- **Sentry 수동 조회**: analyze-issue에서 Sentry 에러를 수동으로 복사-붙여넣기 필요
- **JIRA 수동 업데이트**: execute-plan 완료 후 JIRA 이슈 상태를 수동으로 업데이트
- **MCP 서버 비활성화 어려움**: deniedMcpServers 사용법이 문서화되지 않음
- **환경 변수 분산**: API 키, 토큰 관리가 일관되지 않음

**결정**: Sentry + Atlassian MCP 서버 추가 및 비활성화 가이드 문서화

1. **2개 MCP 서버 추가**:
   - **sentry**: Sentry 에러 트래킹 자동화
     - `SENTRY_ACCESS_TOKEN` 환경 변수 필요
     - `OPENAI_API_KEY` 환경 변수 필요 (Sentry MCP 내부 AI 분석용)
     - analyze-issue에서 Sentry 이슈 자동 조회 가능
   - **atlassian**: JIRA/Confluence 자동화
     - OAuth 기반 인증 (별도 토큰 불필요)
     - execute-plan, document에서 JIRA 이슈 자동 업데이트 가능
     - requirement-validator에서 AC 자동 조회 가능

2. **MCP 서버 비활성화 가이드**:
   - `deniedMcpServers` 사용법 상세 문서화 (README.md)
   - 5개 MCP 서버별 정확한 `serverCommand` 예시 제공
   - 환경 변수 치환 주의사항 명시 (`${VAR}` → 실제 값)
   - 확인 방법 (`claude mcp list`) 추가

3. **환경 변수 통합 관리**:
   - 모든 MCP 서버 인증 정보를 `.zshenv`에 통합
   - `CONTEXT7_API_KEY`, `SENTRY_ACCESS_TOKEN`, `OPENAI_API_KEY` 일괄 관리
   - marketplace.json에서 환경 변수로 참조하여 보안 강화

**영향**:
- **MCP 서버 총 5개**: sequential-thinking, context7, serena, sentry, atlassian
- **자동화 향상**: Sentry/JIRA 수동 작업 자동화
- **보안 강화**: 모든 민감 정보는 환경 변수로 처리
- **사용자 경험 개선**: MCP 서버 비활성화 방법 명확화
- **Breaking Change**: 없음 (완전 하위 호환, 선택적 실행)

**대안**:
1. ~~Sentry/JIRA MCP 서버 미포함~~ → 수동 작업 지속, 자동화 부족
2. ~~하드코딩된 토큰 사용~~ → 보안 리스크, public repo 노출
3. ✅ **환경 변수 + MCP 자동 통합** → 채택 (보안 + 편의성)

**패턴**: MCP 서버 확장 가능성
- 필요한 외부 서비스가 있을 때 MCP 서버로 통합 (API 키는 환경 변수)
- marketplace.json에 정의만 하면 자동 활성화
- 사용자가 원하면 `deniedMcpServers`로 비활성화 가능

**관련 파일**:
- [.claude-plugin/marketplace.json:65-87](.claude-plugin/marketplace.json#L65-L87) - Sentry/Atlassian MCP 정의
- [README.md:65-150](README.md#L65-L150) - MCP 서버 비활성화 가이드
- [.zshenv](.zshenv) - 환경 변수 설정

**재발 방지**:
- 새 MCP 서버 추가 시 README에 비활성화 가이드도 함께 업데이트
- 환경 변수 사용 시 실제 값 예시 제공 (치환 방법 명시)
- 보안 관련 정보는 절대 하드코딩하지 않기

**버전**: v2.3.0 → v2.4.0

---

### 2025-12-09 - v2.0.0 Agents 시스템 도입

**컨텍스트**:
v1.6.0까지 workflow-skills는 Skills만으로 구성되어 있었습니다. Skills는 다단계 워크플로우를 오케스트레이션하는 데 탁월하지만, 특정 기술 작업(리팩토링, 테스트 생성)을 자동화하기에는 너무 무거웠습니다. 각 Skill이 직접 모든 작업을 수행하면서 중복 로직이 발생하고 재사용성이 떨어졌습니다.

**문제점**:
- **중복 로직**: analyze-issue, execute-plan 모두 복잡도 분석 로직 중복
- **재사용성 부족**: 리팩토링, 테스트 생성 로직을 다른 Skills에서 활용 불가
- **Skills 비대화**: 단일 Skill이 너무 많은 책임을 담당 (SRP 위반)
- **유지보수 어려움**: 로직 수정 시 여러 Skills 동시 수정 필요

**결정**: Skills + Agents 아키텍처 도입

1. **명확한 역할 분리**:
   - **Skills**: 워크플로우 오케스트레이션 (6-9 phases, 복잡도 높음)
   - **Agents**: 특정 기술 작업 자동화 (4-5 phases, 단일 책임)

2. **4개 핵심 Agents 추가**:
   - `code-refactorer` (P0): Cyclomatic complexity > 10, 함수 길이 > 50 → Extract Method/Class
   - `test-generator` (P0): 테스트 누락 파일 자동 생성 (Happy/Edge/Error cases)
   - `performance-analyzer` (P2): N+1 query, 번들 크기, 느린 쿼리 탐지
   - `code-reviewer` (P3): SOLID 원칙, Code Smell, 네이밍 규칙 검증

3. **통합 지점**:
   - `analyze-issue` → Phase 3D (code-refactorer 권장)
   - `execute-plan` → Phase 4C (DB Migration 검증), Phase 5 (test-generator 자동 호출)
   - `mr-code-review` → Phase 4 (의존성 보안 체크 추가)

4. **plugin.json 도입**:
   ```json
   {
     "name": "workflow-skills",
     "version": "2.0.0",
     "skills": ["./analyze-issue", ...],
     "agents": "./agents"
   }
   ```

**영향**:
- **Breaking Change**: v1.6.0 → v2.0.0
- **재사용성 향상**: Agents를 여러 Skills에서 호출 가능
- **유지보수성 개선**: 로직 변경 시 Agent 파일만 수정
- **확장성**: 새 Agent 추가 시 기존 Skills 수정 불필요
- **Skills 단순화**: analyze-issue Phase 3D, execute-plan Phase 4C/5 추가하여 Agent 호출

**대안**:
1. ~~Skills만 유지 + 로직 중복 허용~~ → 장기적 유지보수 부담
2. ~~외부 라이브러리/도구 사용~~ → MCP 통합 필요, 일관성 부족
3. ✅ **Skills + Agents 아키텍처** → 채택 (명확한 책임 분리, 높은 재사용성)

**패턴**: Composition over Inheritance (in workflow design)
- Skill이 모든 기능을 직접 구현 (Inheritance 스타일) → Agent를 조합하여 복잡한 워크플로우 구성 (Composition 스타일)
- 이 패턴은 향후 새로운 Skill 개발 시에도 적용 가능

**관련 파일**:
- [.claude-plugin/plugin.json](.claude-plugin/plugin.json) - Plugin manifest (NEW)
- [agents/code-refactorer.md](agents/code-refactorer.md) - Refactoring Agent (NEW)
- [agents/test-generator.md](agents/test-generator.md) - Test generation Agent (NEW)
- [analyze-issue/SKILL.md:211-278](analyze-issue/SKILL.md#L211-L278) - Phase 3D 추가
- [execute-plan/SKILL.md:283-522](execute-plan/SKILL.md#L283-L522) - Phase 4C, Phase 5 추가

**재발 방지**:
- 새로운 기술 작업 추가 시 Skill이 아닌 Agent로 구현
- Skills는 워크플로우 조율에만 집중
- Agent는 단일 책임 원칙(SRP) 엄격히 준수

---

### 2025-12-09 - v2.1.0 requirement-validator Agent 및 AC Traceability

**컨텍스트**:
v2.0.0에서 Agents 시스템을 도입했지만, JIRA Acceptance Criteria (AC)와 코드 간 자동 매핑 및 추적 기능이 부족했습니다. AC 구현 여부를 수동으로 확인해야 했고, 미구현 AC가 merge 전 감지되지 않는 문제가 있었습니다.

**문제점**:
- **AC 추적 부재**: "AC#1이 어디에 구현되었는가?" 수동 탐색 필요
- **품질 게이트 부재**: 미구현 AC 자동 탐지 불가 → 불완전한 기능 merge 위험
- **Skill 간 AC 정보 전달 부족**: analyze-issue, plan-builder, execute-plan 간 AC 연계 미흡

**결정**: requirement-validator Agent 추가 + 4개 Skill 통합

1. **requirement-validator Agent (5번째 Agent)**:
   - **Mode 1 (Reverse)**: 코드에서 AC 역추적 (analyze-issue Phase 3E)
   - **Mode 2 (Pre-validation)**: 계획서 AC 완전성 검증 (plan-builder Phase 2 Step C-2)
   - **Mode 3 (Post-validation)**: 구현 후 AC 달성 검증 (execute-plan Phase 6)
   - **Mode 4 (Final)**: MR 자동 AC 매핑 (mr-code-review Phase 2-4)

2. **plan-builder에 Step C-2 추가** ⚠️ **CRITICAL**:
   ```markdown
   Step C-2: AC Coverage Check (NEW - JIRA 이슈 있을 때만)

   IF (JIRA 이슈 연결됨):
       1. requirement-validator agent 호출 (Mode 2: Pre-validation)
       2. AC Completeness 확인:
          IF (AC Completeness < 100%):
              → 리뷰 파일에 Required Change 추가
              → Recommendation: "Needs Iteration"
          ELSE:
              → AC Completeness: 100% ✅
              → EXIT LOOP → Go to Phase 3
   ```

3. **4개 Skill 통합**:
   - `analyze-issue`: Phase 3E 추가 (AC 역추적)
   - `plan-builder`: Phase 2 Step C-2 강화 (AC completeness 검증)
   - `execute-plan`: Phase 6 추가 (AC 달성 보고)
   - `mr-code-review`: Phase 2-4 개선 (자동 매핑 강화)

**영향**:
- **Breaking Change**: 없음 (완전 하위 호환, 선택적 실행)
- **품질 향상**: AC 누락 자동 탐지 → merge 전 차단
- **추적성**: 전체 워크플로우에서 AC 중심 축 확립
- **자동화**: 수동 AC 확인 → 자동 매핑/검증

**대안**:
1. ~~Manual AC Tracking (수동 체크리스트)~~ → 확장성 부족, 누락 위험
2. ~~External Tool Integration (JIRA plugin)~~ → MCP 통합 필요, 일관성 부족
3. ✅ **requirement-validator Agent + Skill 통합** → 채택 (자동화 + 워크플로우 일관성)

**관련 파일**:
- [agents/requirement-validator.md](agents/requirement-validator.md) - Agent 정의
- [plan-builder/SKILL.md:244-264](plan-builder/SKILL.md#L244-L264) - Step C-2 구현
- [REQUIREMENT_VALIDATOR_AGENT_PLAN.md](REQUIREMENT_VALIDATOR_AGENT_PLAN.md) - 구현 계획

**재발 방지**:
- 새로운 품질 게이트 추가 시 Agent로 분리 (Skill 비대화 방지)
- AC 같은 핵심 개념은 모든 Skill에서 일관되게 처리
- 선택적 실행 설계 (JIRA 없어도 동작 보장)

**버전**: v2.0.0 → v2.1.0

---

### 2025-12-10 - v2.2.0 plan-builder 리뷰 iteration의 새로운 탐색 강제

**컨텍스트**:
plan-builder 스킬의 "iterative review loop"에서 각 iteration이 이전 리뷰 포인트만 재확인하고 새로운 문제를 놓칠 위험이 발견되었습니다.

**근본 원인**:
- **이전 리뷰 맥락 부재**: Step A가 이전 리뷰 파일을 읽지 않아 피드백 적용 확인 불가능
- **"새로운 탐색" 명령 부재**: "FULL checklist 재적용" 지시가 없어 이전에 확인 안 한 카테고리를 skip 가능
- **진행 추적 메커니즘 부재**: CARRYOVER vs NEW 구분이 없어 개선 여부 불명확

**결정**: Incremental Review with Strong Mandates + CARRYOVER/NEW Tracking

1. **Step A에 5단계 프로세스 도입**:
   - Step 1: Read Previous Review (if N > 1) - 피드백 적용 확인
   - Step 2: Read Current Plan - 기존 유지
   - Step 3: Perform FULL FRESH Critical Review (MANDATORY) - 전체 재적용 강제
   - Step 4: Categorize Findings (CARRYOVER/NEW) - 진행 추적
   - Step 5: Save Review with Tags - 태그 포함 저장

2. **CRITICAL INSTRUCTION 블록**:
   - "DO NOT assume sections are OK" - 명시적 금지
   - "LOOK FOR NEW PROBLEMS" - 명시적 지시
   - "APPLY FULL CHECKLIST FROM SCRATCH" - 강제 명령
   - "Each iteration should discover different types" - 기대 행동 명시

3. **CARRYOVER/NEW 태깅 시스템**:
   - 각 이슈에 [CARRYOVER] 또는 [NEW] 태그 추가
   - 진행 추적: CARRYOVER ↓ (수정 효과), NEW > 0 (새 탐색 증거)
   - Audit trail: 각 iteration의 기여 가시화

4. **자동 Iteration 강제** (Step D Step 8):
   - "Do NOT ask user for approval" 명시
   - 자동 loop back → ZERO 이슈까지 반복
   - 사용자 개입 불필요

**영향**:
- **plan-builder 스킬**: 모든 리뷰 iteration에서 새로운 문제 탐색 보장
- **계획 품질**: 더 많은 문제를 사전 발견하여 최종 계획 품질 향상
- **사용자 경험**: 리뷰 시간 약간 증가하지만 실행 단계에서 재작업 감소
- **Breaking Change**: 없음 (기존 로직 확장, 호환성 유지)

**대안**:
1. ~~Two-Stage Review (A1: 피드백 확인 + A2: 새 탐색)~~ → 너무 복잡, 리뷰 시간 과다 증가
2. ~~Fresh Review Only (이전 리뷰 무시)~~ → 피드백 적용 확인 불가, 비효율
3. ✅ **Incremental Review with Strong Mandates** → 채택 (단순하면서 효과적)

**패턴**: 문서 강제력 확보 패턴 (확장)
- 기존: 서술적 지침 → 명시적 구조 (WHILE, IF/ELSE)
- 추가: **암묵적 기대 → 구체적 명령** (MANDATORY, DO NOT, LOOK FOR)
- 추가: **단일 목적 → 명시적 다중 목적** (확인 + 탐색)

**관련 파일**:
- [plan-builder/SKILL.md:131-270](plan-builder/SKILL.md#L131-L270) - Step A 전면 개선
- [plan-builder/references/review_checklist.md:14-29](plan-builder/references/review_checklist.md#L14-L29) - Step 2 강화
- [plan-builder/tests/review_iteration_fresh_exploration.md](plan-builder/tests/review_iteration_fresh_exploration.md) - 테스트 시나리오

**재발 방지**:
- 중요한 반복 로직은 명시적 구조 + 구체적 명령으로 표현
- "should", "recommended" 같은 약한 표현 지양
- 암묵적 기대를 명시적 지시로 변환 (예: "review comprehensively" → "APPLY FULL CHECKLIST FROM SCRATCH")

**버전**: v2.1.0 → v2.2.0

---

### 2025-12-10 - Plugin에 MCP 서버 자동 통합

**컨텍스트**:
v2.2.0까지 workflow-skills plugin을 설치한 사용자는 MCP 서버(sequential-thinking, context7 등)를 별도로 `.mcp.json`에 설정해야 했습니다. 이로 인해 설치 과정이 복잡했고, MCP 서버 없이 Skills를 실행하면 오류가 발생했습니다.

**문제점**:
- **복잡한 설치**: Plugin 설치 후 `.mcp.json` 수동 설정 필요
- **에러 발생**: MCP 서버 미설정 시 Skills 실행 실패
- **일관성 부족**: 각 사용자마다 다른 MCP 설정
- **보안 리스크**: API 키를 `.mcp.json`에 하드코딩 (Git에 노출 가능)

**결정**: marketplace.json에 mcpServers 필드 추가

1. **3개 MCP 서버 자동 활성화**:
   - **sequential-thinking**: 모든 Skills에서 17회 사용 (필수)
   - **context7**: 최신 라이브러리 문서 조회 (선택)
   - **serena**: 코드 심볼 분석 및 검색 (필수)

2. **환경 변수 사용**:
   ```json
   "mcpServers": {
     "context7": {
       "args": ["--api-key", "${CONTEXT7_API_KEY}"]
     },
     "serena": {
       "command": "uvx",
       "args": ["--from", "git+https://github.com/oraios/serena", "serena", "start-mcp-server"]
     }
   }
   ```
   - `${VAR}`: 환경 변수 확장
   - uvx로 Serena 자동 설치 및 실행

3. **보안 강화**:
   - API 키를 환경 변수로 처리 (public repo 노출 방지)
   - README.md에 환경 변수 설정 안내 추가
   - `.zshenv` 등에 키 저장 권장

**영향**:
- **사용자 경험 개선**: Plugin 설치만으로 MCP 서버 자동 활성화
- **보안 향상**: API 키가 Git 히스토리에 노출되지 않음
- **코드 분석 강화**: Serena MCP로 심볼 기반 검색 및 편집 지원
- **Breaking Change**: 없음 (기존 `.mcp.json` 설정과 병존 가능)

**대안**:
1. ~~API 키 하드코딩~~ → 보안 리스크, public repo에 노출
2. ~~MCP 서버 미포함~~ → 사용자가 수동 설정 필요, 복잡도 증가
3. ✅ **환경 변수 + uvx 자동 설치** → 채택 (보안 + 편의성)

**패턴**: Plugin 의존성 자동 관리
- 사용자가 별도로 설정하지 않아도 필수 의존성(MCP 서버) 자동 활성화
- 환경 변수 + uvx 자동 설치로 보안과 편의성 동시 확보
- 이 패턴은 다른 Plugin 개발 시에도 적용 가능

**관련 파일**:
- [.claude-plugin/marketplace.json:34-64](.claude-plugin/marketplace.json#L34-L64) - mcpServers 설정
- [README.md:50-63](README.md#L50-L63) - 환경 변수 설정 안내

**재발 방지**:
- 새 MCP 서버 추가 시 marketplace.json에 함께 정의
- API 키 등 민감 정보는 항상 환경 변수 사용
- README에 환경 변수 설정 방법 문서화

**버전**: v2.2.0 → v2.3.0

---

### 2025-12-09 - plan-builder 피드백 루프 강제력 확보

**컨텍스트**:
plan-builder 스킬의 핵심 원칙인 "계획서 생성 → 리뷰 → 수정 → 리뷰 → ... → ZERO 이슈까지 반복"이 제대로 작동하지 않는 문제가 발견되었습니다. Claude가 1-2회 반복 후 "충분하다"고 판단하여 조기 종료하는 현상이 발생했습니다.

**근본 원인**:
- **절차적 지침의 강제력 부족**: "Loop back to Phase 2A"와 같은 서술적 지침은 Claude에게 권장사항으로 해석되어 실제 강제되지 않음
- **승인 기준 불일치**: SKILL.md는 "Approve only"를 요구하지만, review_checklist.md는 "Approve with Changes" 옵션을 제공하여 모호성 발생
- **선형 구조로 오해**: Phase 1 → 2 → 3 구조가 "한 번씩만 실행"으로 해석 가능

**결정**: WHILE 루프 구조 + Binary Decision 채택

1. **명시적 WHILE 루프 구조**:
   ```markdown
   WHILE (total_issues > 0 OR Overall Assessment != "Strong") DO:
     Step A: Review
     Step B: Count Issues
     Step C: Decision Gate
     Step D: Apply Feedback → LOOP BACK TO STEP A
   END WHILE
   ```
   - 서술적 지침 → 명시적 제어 흐름
   - Phase 개념 → Step 개념 (순환 구조 명확화)
   - "Loop back" 권장 → "MANDATORY: Loop back" 강제

2. **Binary Decision Model**:
   - 이전: Approve / **Approve with Changes** / Major Revision
   - 현재: ✅ **Approve** / 🔄 **Needs Iteration**
   - "Approve with Changes"의 모호함 제거
   - ZERO 이슈일 때만 Approve 가능

3. **버전 추적 메커니즘**:
   - 리뷰 파일을 `*_PLAN_REVIEW_v[N].md` 형식으로 저장
   - 각 iteration 히스토리 보존 (audit trail)
   - Phase 3에서 아카이브/삭제 옵션 제공

**영향**:
- **plan-builder 스킬**: 모든 실행에서 최소 2-3회 이상 반복 보장
- **사용자 경험**: 계획 생성 시간 증가 (품질 향상의 trade-off)
- **Breaking Change**: v1.5.1 → v1.6.0 (Major version bump)

**대안**:
1. ~~"Approve with Changes" 유지 + 더 명확한 정의~~ → 근본적 해결 불가, 모호성 여전히 존재
2. ~~Iteration 최소 횟수 하드코딩 (예: 3회 강제)~~ → 유연성 부족, 품질 기준과 무관
3. ✅ **WHILE 루프 + Binary Decision** → 채택 (명확하고 강제력 있음)

**패턴**: 문서 강제력 확보 패턴
- 서술적 지침 ("you should", "it's recommended") → 약한 강제력
- 명시적 구조 ("WHILE", "IF/ELSE", "MANDATORY") → 강한 강제력
- 이 패턴은 다른 스킬에서도 적용 가능 (예: execute-plan의 TodoList 강제 사용)

**관련 파일**:
- [plan-builder/SKILL.md:119-427](plan-builder/SKILL.md#L119-L427) - Phase 2 LOOP 구조
- [plan-builder/references/review_checklist.md:479-548](plan-builder/references/review_checklist.md#L479-L548) - Binary 승인 기준
- [plan-builder/tests/loop_verification.md](plan-builder/tests/loop_verification.md) - 테스트 시나리오

**재발 방지**:
- 새 스킬 작성 시 서술적 지침 대신 명시적 구조 사용
- 중요한 반복 로직은 WHILE/FOR 스타일로 표현
- 승인/결정 로직은 Binary decision으로 설계 (3단계 이상 지양)

---

## Notes

- **v2.0.0**: Skills + Agents 아키텍처 도입 (Breaking Change)
- Skills use MCP servers (serena, atlassian, sentry, github, context7, sequential-thinking)
- Agents also use MCP servers (serena, sequential-thinking, context7)
- All skills and agents designed for Korean language output and documentation
- Skills integrate with existing custom command workflow
- Agents are called by Skills automatically (Skills → Agent composition)
- Reference files loaded on-demand to manage context efficiently
- Marketplace distribution requires GitHub public repository
- Version updates are reflected when users refresh marketplace
- Current version: **v2.0.0** (Skills + Agents)
