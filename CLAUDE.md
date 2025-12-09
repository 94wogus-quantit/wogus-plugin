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

### frontend-designer

Proactively create distinctive, production-grade frontend interfaces.

**When to use**: UI component/page implementation, design improvements, React/Vue/Next.js components
**Technology**: Tailwind CSS v4+ (CSS-first configuration), bold aesthetics, avoiding generic AI designs
**Integration**: Used AFTER plan-builder or DURING execute-plan for frontend tasks

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

## Notes

- Skills use MCP servers (serena, atlassian, sentry, github, context7, sequential-thinking)
- All skills designed for Korean language output and documentation
- Skills integrate with existing custom command workflow
- Reference files loaded on-demand to manage context efficiently
- Marketplace distribution requires GitHub public repository
- Version updates are reflected when users refresh marketplace
