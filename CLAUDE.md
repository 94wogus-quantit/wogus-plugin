# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Personal skills collection repository containing Claude Code skills and custom commands for systematic software development workflows.

**Key Artifacts:**
- Skills: Reusable capabilities packaged as `.zip` files
- Custom Commands: Workflow automation commands
- Reference Materials: Templates and pattern catalogs

## Repository Structure

```
skills/
├── <skill-name>/          # Each skill is a directory
│   ├── SKILL.md          # Required: Metadata + instructions
│   ├── references/       # Optional: Reference documentation
│   ├── scripts/          # Optional: Executable code
│   └── assets/           # Optional: Templates and assets
├── <skill-name>.zip      # Packaged skill for distribution
├── CLAUDE.md            # This file
└── README.md            # User-facing documentation
```

## Available Skills

### analyze-issue

Systematic root cause analysis for bugs and issues.

**When to use**: Analyzing JIRA issues, Sentry errors, or investigating bug reports
**Output**: `[ISSUE_ID]_REPORT.md` with root cause, affected code, and recommendations
**Integration**: First step in `/analyze-issue → /plan → /execute-plan → /document` workflow

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

## Development Best Practices

**For Skills:**
- Focus on procedural knowledge and domain expertise
- Reference bundled resources explicitly in SKILL.md
- Test with realistic scenarios before packaging
- Iterate based on actual usage

**For This Repository:**
- One skill per directory
- Always package skills before distribution
- Document integrations with other skills/commands
- Keep examples and templates up to date

## Marketplace Distribution

This repository is distributed as a **Claude Code Marketplace**.

### Configuration

Marketplace configuration is in `.claude-plugin/marketplace.json`:
- `name`: Marketplace identifier
- `owner`: Owner information
- `metadata.version`: Current version (semantic versioning)
- `plugins[].skills`: Array of skill directories to include

### Publishing Workflow

1. **Develop**: Create/modify skills in source directories
2. **Update Version**: Increment `metadata.version` in `marketplace.json`
3. **Commit & Push**: Push to GitHub public repository
4. **Users Update**: Users run `/marketplace refresh` to get latest version

### Version Management

```bash
# Update version in marketplace.json
# v1.5.1 → v1.6.0 (new feature)
# v1.5.1 → v1.5.2 (bug fix)

git add .claude-plugin/marketplace.json
git commit -m "chore: bump version to v1.6.0"
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

- Skills use MCP servers (serena, atlassian, sentry, github, context7, sequential-thinking)
- All skills designed for Korean language output and documentation
- Skills integrate with existing custom command workflow
- Reference files loaded on-demand to manage context efficiently
- Marketplace distribution requires GitHub public repository
- Version updates are reflected when users refresh marketplace
