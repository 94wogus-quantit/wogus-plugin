# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Personal plugin collection repository containing Claude Code Skills, Agents, and custom commands for systematic software development workflows.

**Key Artifacts (v3.4.1):**
- **Skills**: Workflow orchestrators for multi-step processes (분석, 계획, 실행, 문서화)
- **Agents**: AC (Acceptance Criteria) traceability (requirement-validator만 유지)
- **Custom Commands**: Workflow automation commands (별도 설치)
- **Reference Materials**: Templates and pattern catalogs

## Repository Structure

```
wogus-plugin/  (v3.4.1)
├── .claude-plugin/         # Plugin configuration
│   ├── marketplace.json    # Marketplace metadata
│   └── plugin.json         # Plugin manifest (Skills + Agents)
│
├── agents/                 # Agent definitions (v3.0.0: 1개만 유지)
│   └── requirement-validator.md  # AC traceability
│
├── <skill-name>/          # Each skill is a directory
│   ├── SKILL.md          # Required: Metadata + instructions
│   ├── references/       # Optional: Reference documentation
│   ├── scripts/          # Optional: Executable code
│   └── assets/           # Optional: Templates and assets
│
├── docs/architecture/decisions/  # Architecture Decision Records
├── CLAUDE.md            # This file
├── README.md            # User-facing documentation
└── CHANGELOG.md         # Version history
```

## Available Skills

### analyze-issue (v3.4.1)
Systematic root cause analysis with Git Worktree support.
- **Worktree 자동 생성** (`../worktrees/[branch-name]`)
- **Output**: `[ISSUE_ID]_REPORT.md`
- **Integration**: First step in workflow

### plan-builder (v3.4.1)
Create high-quality, thoroughly reviewed implementation plans.
- **Iterative review loop** (ZERO 이슈까지 반복)
- **Output**: `[FEATURE]_PLAN.md`
- **Integration**: Second step in workflow

### execute-plan (v3.4.1)
Execute approved implementation plans with TodoList tracking.
- **Worktree 환경 확인** (브랜치 충돌 경고)
- **Output**: Code implementation + test results
- **Integration**: Third step in workflow

### document (v3.4.1)
Consolidate workflow artifacts and update project documentation.
- **Worktree 정리** (삭제/유지/아카이브 옵션)
- **Output**: Updated README, CHANGELOG, CLAUDE docs
- **Integration**: Final step in workflow

### mr-code-review
GitLab MR의 코드 변경사항을 분석하여 맥락 기반 종합 리뷰 수행.

### mcp-config (v3.3.0)
MCP 서버 활성화/비활성화 및 도구별 권한 관리.

## Available Agents

### requirement-validator (v3.0.0)
JIRA Acceptance Criteria와 코드를 자동 매핑하여 요구사항 달성 여부 검증.

**4가지 실행 모드**:
- **Mode 1 (Reverse)**: 코드 → AC 역매핑 (analyze-issue)
- **Mode 2 (Pre)**: 계획 → AC coverage (plan-builder)
- **Mode 3 (Post)**: git diff → AC 구현 확인 (execute-plan)
- **Mode 4 (Final)**: MR → AC 최종 게이트 (mr-code-review)

**Integration**: 4개 Skills에서 자동 호출

## Skills vs Agents

| Aspect | Skills | Agents |
|--------|--------|--------|
| **Purpose** | Orchestrate multi-step workflows | AC traceability |
| **Scope** | Broad (analysis → execution) | Narrow (AC 추적 전용) |
| **File Format** | `SKILL.md` in skill directory | `.md` files in `agents/` |
| **Invocation** | User explicitly uses skill | Skills call automatically |
| **Count** | 6개 | 1개 |

## AC Traceability Example

```
JIRA-123: "사용자 이메일 로그인"
├─ AC#1: 이메일 validation
├─ AC#2: 5회 실패 시 계정 잠금
└─ AC#3: JWT 토큰 발급

1. analyze-issue → Mode 1: "이 버그는 AC#2 미충족"
2. plan-builder → Mode 2: "계획이 AC#1,2,3 모두 커버 ✅"
3. execute-plan → Mode 3: "AC#1 ✅, AC#2 ❌ 미구현"
4. mr-code-review → Mode 4: "AC#2 미구현 → MR BLOCKED"
```

## Skill Development

### Creating New Skills

1. Initialize with skill-creator template
2. Customize `SKILL.md` (metadata, workflow instructions, tool references)
3. Add resources (optional): `references/`, `scripts/`, `assets/`
4. Package for distribution

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

## Agent Development (v3.0.0)

**Current Status**: v3.0.0에서 1개 Agent만 유지 (requirement-validator)

**Creating New Agents** (if needed):
- Agent는 여러 Skills에서 공유할 때만 생성
- 단독 호출 전용이면 Skills Phase로 구현
- All content in Korean (한국어 필수)
- 4-5 phases maximum

For detailed guidelines, see historical ADRs.

## Integration with Custom Commands

**Workflow**: `/analyze-issue [JIRA] → /plan → /execute-plan → /document`

Skills work alongside custom commands in `~/.claude/commands/` for seamless workflow automation.

## Marketplace Distribution

This repository is distributed as a **Claude Code Marketplace**.

### Configuration

- **File**: `.claude-plugin/marketplace.json`
- **Version**: Semantic versioning (current: v3.4.1)
- **MCP Servers**: 8개 자동 통합 (sequential-thinking, context7, serena, sentry, atlassian, terraform, amplitude, chrome-devtools)

### Publishing Workflow

1. Develop: Create/modify skills or agents
2. Update Version: Increment `metadata.version`
3. Commit & Push: Push to GitHub
4. Users Update: `/marketplace refresh`

### User Installation

```bash
/marketplace add git@github.com:94wogus-quantit/skills.git
/plugin install workflow-skills:analyze-issue
```

## Development Best Practices

**For Skills:**
- Focus on procedural knowledge and domain expertise
- Reference bundled resources explicitly
- Test with realistic scenarios

**For Agents:**
- Single responsibility principle (여러 Skills에서 공유할 때만 생성)
- Clear trigger conditions in description
- All content in Korean (mandatory)

**For This Repository:**
- One skill per directory, one agent per `.md` file
- Document integrations with other skills/commands/agents
- Architecture decisions → `docs/architecture/decisions/`

---

## 아키텍처 결정사항 (Architecture Decisions)

### 최신 결정사항 (Latest ADRs)

이 섹션에는 최신 3개의 아키텍처 결정사항만 포함합니다.
이전 버전의 ADR은 **[docs/architecture/decisions/](docs/architecture/decisions/)** 디렉토리를 참조하세요.

---

### v3.4.1 - Phase 0 강제 실행 보장 (CRITICAL 블록 추가) (2025-12-11)

**컨텍스트**:
v3.4.0에서 4개 스킬에 Phase 0 (Worktree Validation/Setup)를 추가했지만, Claude가 Phase 0를 건너뛰고 바로 Phase 1을 실행하는 문제 발생.

**문제점**:
- **서술적 지침의 약한 강제력**: "Objective", "Steps" 같은 서술형 표현은 "권장사항"으로 해석
- **bash 스크립트 예시로 오해**: 코드 블록이 "참고용 예시"로 인식
- **Phase 전환 불명확**: "Phase 1로 진행" 표현은 Phase 0 실행 여부와 무관하게 진행 가능하다고 해석
- **일관성 부족**: plan-builder의 "⛔ MANDATORY" 스타일과 달리 Phase 0는 약한 표현 사용

**결정**: 모든 4개 스킬의 Phase 0에 CRITICAL 강제 블록 추가 (plan-builder v1.6.0 패턴 적용)

```markdown
⚠️ **CRITICAL: DO NOT SKIP PHASE 0**

> **MANDATORY REQUIREMENT**:
> - Phase 0 is the **FIRST step** of this skill
> - You **MUST** execute Phase 0 **BEFORE** proceeding to Phase 1
> - **DO NOT** assume you are in the correct environment
> - **ALWAYS** verify worktree status explicitly
> - **NEVER** start [next phase] without completing Phase 0
```

**영향**:
- Phase 0 실행 보장: 명시적 금지 표현으로 Phase 0 건너뛰기 방지
- 사용자 경험 개선: Worktree 생성/확인 누락 없음 → 브랜치 충돌 위험 감소
- 일관성 확보: plan-builder의 MANDATORY 패턴과 동일한 강제력
- Breaking Change: 없음 (기존 로직 유지, 표현 강화만)

**패턴**: 문서 강제력 확보 패턴 (재적용)
- 서술적 지침 ("Objective", "Steps") → 약한 강제력
- 명시적 구조 + 구체적 명령 ("⚠️ CRITICAL", "MUST", "DO NOT", "NEVER") → 강한 강제력
- 이 패턴은 plan-builder의 "Feedback Loop Until Perfect" (v1.6.0)에서 검증됨

**관련 파일**:
- analyze-issue/SKILL.md:45-58 - CRITICAL 블록
- plan-builder/SKILL.md:69-82 - CRITICAL 블록
- execute-plan/SKILL.md:63-76 - CRITICAL 블록
- document/SKILL.md:87-100 - CRITICAL 블록

**버전**: v3.4.0 → v3.4.1

---

### v3.4.0 - Git Worktree 워크플로우 통합 (2025-12-11)

**컨텍스트**:
workflow-skills의 4개 스킬은 단일 작업 흐름을 가정. 실무에서는 긴급 핫픽스, 병렬 작업 등으로 여러 JIRA 작업을 동시에 진행해야 하는 경우가 많음.

**문제점**:
- **작업 전환 비용**: 브랜치 전환 시 stash/unstash + 의존성 재설치 (10분 소요)
- **작업 격리 부족**: 여러 작업을 병렬로 진행할 때 브랜치 충돌 발생
- **수동 관리 부담**: 사용자가 직접 worktree 생성/전환/삭제 명령어 실행

**결정**: 모든 4개 스킬에 Phase 0 추가 (Worktree Lifecycle Management)

**Skill별 Phase 0 동작**:
- **analyze-issue**: Worktree 자동 생성 및 이동 (PROACTIVE)
- **plan-builder**: Worktree 확인 및 권장 메시지 (DEFENSIVE)
- **execute-plan**: Worktree 확인 및 브랜치 충돌 경고 (DEFENSIVE)
- **document**: Worktree 확인 (Phase 0) + 정리 옵션 (Phase 9)

**Worktree 생명 주기**:
- **생성**: analyze-issue Phase 0에서 자동 생성 (`../worktrees/[branch-name]`)
- **사용**: plan-builder, execute-plan에서 검증 및 경고
- **정리**: document Phase 9에서 Git 커밋/푸시 확인 + 삭제 옵션
- **이름 규칙**: `../worktrees/feature/JIRA-123`

**영향**:
- 시간 절약: 브랜치 전환 10분 → worktree 전환 5초 (하루 3회 전환 시 30분 절약)
- 작업 격리: 여러 JIRA 작업 동시 진행 가능 (병렬 작업 지원)
- 자동화: Worktree 생성/삭제 자동화로 수동 명령어 불필요
- Breaking Change: 없음

**버전**: v3.3.0 → v3.4.0

---

### v3.3.0 - MCP 도구 권한 관리 기능 추가 (2025-12-10)

**컨텍스트**:
v3.2.2까지 mcp-config는 MCP 서버 활성화/비활성화(`deniedMcpServers`)만 관리. 개별 MCP 도구의 권한(allow/deny/ask) 설정은 `settings.local.json` 직접 편집 필요.

**문제점**:
- **도구 목록 조회 불가**: MCP 서버별 제공 도구를 확인할 방법이 없음
- **권한 패턴 불명확**: `mcp__plugin_workflow-skills_serena__find_symbol` 같은 패턴을 사용자가 직접 작성
- **권한 관리 복잡**: allow/deny/ask 간 이동 시 이전 배열에서 수동 제거 필요

**결정**: Phase 5 추가 (MCP 도구 권한 관리)

1. **MCP 도구 참조 문서 생성** (`references/mcp_tools.md`):
   - 8개 MCP 서버의 140+ 도구 목록 문서화
   - Permission 패턴 규칙 및 예시 제공

2. **Phase 5 워크플로우**:
   - Step 1: 현재 권한 상태 파악
   - Step 2: 사용자 요청 파싱 (자연어 → permission 패턴)
   - Step 3: 권한 설정 수정 (중복 확인, 권한 이동)
   - Step 4: 결과 확인 (상태 테이블 출력)

3. **자동 권한 이동**:
   - allow → deny 이동 시 allow에서 자동 제거
   - 권한 제거 시 모든 배열에서 제거

**영향**:
- 자연어로 도구 권한 관리 가능
- 140+ 도구를 한눈에 확인
- 권한 충돌 방지
- Breaking Change: 없음

**버전**: v3.2.2 → v3.3.0

---

## 이전 버전 ADRs

v3.0.0 ~ v3.2.1, v2.0.0 ~ v2.4.0, v1.6.0 등의 아키텍처 결정사항은 다음 디렉토리에서 확인하세요:

📁 **[docs/architecture/decisions/](docs/architecture/decisions/)**
- [ADR-0001](docs/architecture/decisions/ADR-0001-v2.0.0-agents-introduction.md): v2.0.0 Agents 시스템 도입
- [ADR-0002](docs/architecture/decisions/ADR-0002-v2.4.0-mcp-server-expansion-1.md): v2.4.0 MCP 서버 확장 (Sentry + Atlassian)
- [ADR-0003](docs/architecture/decisions/ADR-0003-v3.0.0-agents-reduction.md): v3.0.0 Agents 시스템 축소 리팩토링
- [ADR-0004-0007](docs/architecture/decisions/ADR-0004-0007-v3.0-v3.2.md): v3.0.1 ~ v3.2.0 MCP 관련 개선사항
- [HISTORICAL_ADRS](docs/architecture/decisions/HISTORICAL_ADRS.md): v2.1.0 ~ v2.3.0, v1.6.0

---

## Notes

- **Current version**: v3.4.1 (Skills + Agents + Git Worktree + Phase 0 강제)
- Skills use MCP servers (serena, atlassian, sentry, context7, sequential-thinking, terraform, amplitude, chrome-devtools)
- Agents use MCP servers (serena, sequential-thinking, context7, atlassian)
- All skills and agents designed for Korean language output
- Reference files loaded on-demand to manage context efficiently
- Marketplace distribution requires GitHub public repository
- Version updates reflected when users run `/marketplace refresh`
