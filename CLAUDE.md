# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Personal plugin collection repository containing Claude Code Skills, Agents, and custom commands for systematic software development workflows.

**Key Artifacts (v2.0.0):**
- **Skills**: Workflow orchestrators for multi-step processes (분석, 계획, 실행, 문서화)
- **Agents**: Specialized automation for technical tasks (리팩토링, 테스트 생성, 성능 분석)
- **Custom Commands**: Workflow automation commands (별도 설치)
- **Reference Materials**: Templates and pattern catalogs

## Repository Structure

```
wogus-plugin/  (v2.0.0)
├── .claude-plugin/         # Plugin configuration
│   ├── marketplace.json    # Marketplace metadata
│   └── plugin.json         # ✨ NEW: Plugin manifest (Skills + Agents)
│
├── agents/                 # ✨ NEW: Agent definitions
│   ├── code-refactorer.md       # Automatic refactoring
│   ├── test-generator.md        # Automatic test generation
│   ├── performance-analyzer.md  # Performance bottleneck analysis
│   └── code-reviewer.md         # Code quality review
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

## Available Agents (v2.0.0 NEW)

**What are Agents?**

Agents are specialized automation tools that focus on specific technical tasks. Unlike Skills (which orchestrate multi-step workflows), Agents perform focused, deterministic operations like refactoring code, generating tests, or analyzing performance.

### code-refactorer

Automatically refactors complex legacy code using Extract Method, Extract Class patterns.

**Triggers**: Cyclomatic complexity > 10, function length > 50 lines
**Integration**: Called by `analyze-issue` (Phase 3D) and `execute-plan` (Phase 4)

### test-generator

Generates comprehensive unit tests (Jest, pytest) with Happy path, Edge cases, Error handling.

**Triggers**: Missing test files, low coverage
**Integration**: Called by `execute-plan` (Phase 5)

### performance-analyzer

Analyzes performance bottlenecks (N+1 queries, slow queries, bundle size).

**Triggers**: Manual invocation or performance issues detected
**Integration**: Independent or called by custom commands

### code-reviewer

Automated code review for SOLID principles, Code Smells, naming conventions.

**Triggers**: Manual invocation before PRs
**Integration**: Independent or called by `mr-code-review`

## Skills vs Agents

| Aspect | Skills | Agents |
|--------|--------|--------|
| **Purpose** | Orchestrate multi-step workflows | Automate specific technical tasks |
| **Scope** | Broad (analysis → plan → execution) | Narrow (refactoring, testing, etc.) |
| **File Format** | `SKILL.md` in skill directory | `.md` files in `agents/` |
| **Invocation** | User explicitly uses skill | Skills call Agents automatically |
| **Examples** | analyze-issue, plan-builder | code-refactorer, test-generator |
| **Complexity** | High (6-9 phases) | Low (4-5 phases) |

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
