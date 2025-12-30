---
name: record
description: Consolidate workflow artifacts (analysis reports, plans, implementation results) into comprehensive project documentation. Updates README, CHANGELOG, CLAUDE docs and stores technical insights in Serena memory. Use after completing implementation to finalize and document completed work with optional git commit/push. Korean triggers: 문서화, 문서 작성, 문서 업데이트, README 작성, CHANGELOG 작성, 변경사항 기록, 릴리즈 노트, 정리해줘, 문서 정리, 커밋해줘, 푸시해줘, 마무리해줘, 완료 처리.
---

# Record

## ⚠️ CRITICAL LANGUAGE POLICY

**DEFAULT LANGUAGE: KOREAN (한국어)**

ALL outputs, documentation, CHANGELOG entries, and communications MUST be in **KOREAN** unless explicitly requested otherwise by the user.

- ✅ **README updates**: Write in Korean
- ✅ **CHANGELOG entries**: Write in Korean
- ✅ **CLAUDE documentation**: Write in Korean
- ✅ **Serena memories**: Write in Korean
- ✅ **JIRA comments**: Write in Korean
- ✅ **User communication**: Respond in Korean

**Exception**: If the user writes in another language, match that language for responses.

**This is a MANDATORY requirement. Do NOT default to English.**

---

## When to Use This Skill

Use this skill when:
- Implementation work is complete and needs documentation
- User requests "문서화해줘", "document this", "update documentation"
- **After `execute` completes** (mandatory for README/CHANGELOG updates)
- Need to update project README with new features
- Need to add CHANGELOG entries
- Multiple workflow artifacts need consolidation
- Before committing final changes to git
- As part of release preparation

**Typical Workflow Position**:
```
analyze → plan → execute → **record**
```

**⚠️ Important Note**:
The `execute` skill only handles code implementation and testing. This skill is **responsible for project documentation (README, CHANGELOG, etc.)**. Run this skill after `execute` completion to update all documentation.

---

## Overview

This skill provides a 9-phase process to collect all artifacts generated from the workflow and systematically update project documentation:

1. **Discovery & Collection**: Find and collect workflow artifacts
2. **README Update**: Update project README with features, API, settings, etc.
3. **CHANGELOG Update**: Add change history in Keep a Changelog format
4. **CLAUDE Documentation**: Update architecture decisions and troubleshooting guides
5. **Serena Memory**: Save technical insights to memory
6. **JIRA Issue Update**: Summarize implementation completion and add comments to JIRA issue
7. **Additional Docs**: Create migration guides, API docs, etc. as needed
8. **Verification**: Verify documentation quality and completeness
9. **Cleanup**: Clean up workflow artifacts (archive or delete)

---

## Documentation Strategy

### Documentation Purpose and Audience

| Document | Purpose | Target Audience | Update Timing |
|------|------|-----------|---------------|
| **README.md** | Project overview and onboarding | New developers | On major architecture changes |
| **CLAUDE.md** | AI work guidelines | Claude Code | On workflow/convention changes |
| **CHANGELOG.md** | Detailed change history | All developers, PM | After all feature implementation/bug fixes |
| **Serena Memory** | Complex technical patterns | Claude Code | On 50+ line code changes |

**Key Principles**:
- Each document has a clear purpose and target audience
- Avoid duplication and place information in appropriate documents
- Maintain a maintainable document structure

---

## Workflow: 10-Phase Documentation Process

### Phase 0: Branch Validation

⚠️ **CRITICAL: DO NOT SKIP PHASE 0**

> **MANDATORY REQUIREMENT**:
>
> - Phase 0 is the **FIRST step** of this skill
> - You **MUST** execute Phase 0 **BEFORE** proceeding to Phase 1
> - **DO NOT** assume you are on the correct branch
> - **ALWAYS** verify branch status explicitly by running the bash commands below
> - **NEVER** start documentation (Phase 1) without completing Phase 0
>
> **Why this matters**:
> - Verifies git branch before committing documentation
> - Ensures documentation updates are in feature branches
> - Prevents accidental commits to protected branches

**Objective**: Verify that you are working on a feature branch.

**Steps**:

**1. Check Current Branch**

```bash
CURRENT_BRANCH=$(git branch --show-current)
echo "📍 Current branch: $CURRENT_BRANCH"

# Check if on main, master, or staging branch
if [[ "$CURRENT_BRANCH" == "main" ]] || [[ "$CURRENT_BRANCH" == "master" ]] || [[ "$CURRENT_BRANCH" == "staging" ]]; then
  echo "⚠️ Warning: You are working on $CURRENT_BRANCH branch!"
  echo "⚠️ Cannot work on main/master/staging branches."
  echo ""
  read -p "Do you want to continue? [y/N] " -n 1 -r
  echo
  if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    echo "❌ Aborted"
    exit 1
  fi
else
  echo "✅ Working on a feature branch"
fi
```

**2. Proceed to Phase 1**

- Execute existing Phase 1-8
- Documentation updates (README.md, CHANGELOG.md, etc.) happen in the current feature branch

---

### Phase 1: Discovery and Collection

**Objective**: Find, read, and understand all workflow artifacts.

#### 1A. Find Workflow Artifacts

```typescript
// Search for all relevant files
Glob({pattern: "*_REPORT.md"})  // Analysis reports
Glob({pattern: "*_PLAN.md"})    // Task plans
Glob({pattern: "*_REVIEW.md"})  // Plan reviews (if any)

// List directory to check for other files
mcp__plugin_workflow-skills_serena__list_dir({relative_path: ".", recursive: false})
```

#### 1B. Read and Parse Artifacts

For each file found:
```typescript
Read({file_path: artifactPath})

// Extract key information:
- Problem/feature description
- What was implemented
- Code changes made
- Technical decisions
- Tests added
- Dependencies added/changed
- Breaking changes
```

#### 1C. Organize Information

```typescript
// Use sequential thinking to organize
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "Analyzing workflow artifacts to determine documentation structure",
  thoughtNumber: 1,
  totalThoughts: 5,
  nextThoughtNeeded: true
})

// Determine:
- What goes in README (features, usage, API)
- What goes in CHANGELOG (changes by type)
- What goes in CLAUDE docs (decisions, patterns)
- What goes in Serena memory (technical context)
```

---

### Phase 2: README Update

**Objective**: Add new features, API, settings, etc. to README.

#### 2A. Find and Read Current README

```typescript
// Find README
mcp__plugin_workflow-skills_serena__find_file({file_mask: "README*", relative_path: "."})

// Read current README
Read({file_path: "README.md"})

// Identify sections:
- Features
- API documentation
- Configuration/Environment variables
- Installation/Setup
- Usage examples
- Dependencies
```

#### 2B. Prepare Updates

Prepare updates based on content extracted from workflow artifacts:

**Features Section**:
```markdown
## Features

### ✨ [New Feature Name] (Added: 2025-01-15)
[Brief description from plan/implementation]

**Key Capabilities:**
- [Capability 1]
- [Capability 2]

**Usage:**
\`\`\`typescript
[Code example]
\`\`\`
```

**API Documentation**:
```markdown
## API Reference

### New Endpoints

#### `POST /api/new-endpoint`
[Description]

**Request:**
\`\`\`json
{
  "field": "value"
}
\`\`\`

**Response:**
\`\`\`json
{
  "result": "success"
}
\`\`\`
```

**Configuration**:
```markdown
## Configuration

### Environment Variables

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `NEW_VAR` | [Description] | `default` | Yes |
```

#### 2C. Apply README Updates

```typescript
// Update README sections
Edit({
  file_path: "README.md",
  old_string: "## Features\n[old content]",
  new_string: "## Features\n\n### ✨ New Feature\n...\n\n[old content]"
})
```

---

### Phase 3: CHANGELOG Update

**Objective**: Add change history to CHANGELOG in Keep a Changelog format.

#### 3A. Find or Create CHANGELOG

```typescript
// Look for CHANGELOG
mcp__plugin_workflow-skills_serena__find_file({file_mask: "CHANGELOG*", relative_path: "."})

// If not found, create new one
Write({
  file_path: "CHANGELOG.md",
  content: `# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/ko/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/lang/ko/).

## [Unreleased]
`
})
```

#### 3B. Add New Entry

```markdown
## [Unreleased] - 2025-01-15

### Added
- [New feature from plan]
- [New API endpoint]
- [New configuration option]

### Changed
- [Updated feature from report]
- [Modified behavior]

### Fixed
- [Bug fix from issue report]
- Resolved: [ISSUE-123] [Issue title]

### Technical Details
- **Dependencies**: [New/updated dependencies]
- **Breaking Changes**: [If any]
- **Related Issues**: [ISSUE-123]

### Testing
- [New tests added]
- [Coverage improvements]
```

---

### Phase 4: CLAUDE Documentation Update

**Objective**: Add architecture decisions and troubleshooting guides to CLAUDE documentation.

#### 4A. Find CLAUDE Documentation

```typescript
// Look for CLAUDE docs
mcp__plugin_workflow-skills_serena__find_file({file_mask: "CLAUDE*", relative_path: "."})
// Or check .claude/ directory
```

#### 4B. Update Sections

**Architecture Decisions**:
```markdown
## 아키텍처 결정사항

### 2025-01-15 - [Decision Title]

**컨텍스트**: [Why this decision was made]

**결정**: [What was decided]

**영향**: [Impact on codebase]

**대안**: [Alternatives considered]
```

**Troubleshooting Guide**:
```markdown
## 문제 해결 가이드

### [Issue Title]

**증상**: [Problem description from report]

**원인**: [Root cause from analysis]

**해결방법**: [Solution from implementation]

**참고**: [Related files/documentation]
```

---

### Phase 5: Serena Memory Update

**Objective**: Save technical insights to Serena memory.

#### 5A. Store Technical Context

```typescript
// Architectural decisions
mcp__plugin_workflow-skills_serena__write_memory({
  memory_file_name: "architecture_decisions.md",
  content: `
## 2025-01-15 - [Feature/Fix Name]

### 결정 사항
[Key architectural decisions]

### 근거
[Why these decisions were made]

### 영향받는 컴포넌트
- [Component 1]: [How it's affected]

### 주의사항
[Important considerations]
`
})

// Known issues and solutions
mcp__plugin_workflow-skills_serena__write_memory({
  memory_file_name: "known_issues.md",
  content: `
## 2025-01-15 - [Issue Type]

### 이슈 설명
[Issue description]

### 근본 원인
[Root cause]

### 해결 방법
[How it was fixed]

### 재발 방지
[Prevention measures]

### 관련 코드
- [file.ts:123] - [Description]
`
})

// Code patterns
mcp__plugin_workflow-skills_serena__write_memory({
  memory_file_name: "code_patterns.md",
  content: `
## 2025-01-15 - [Pattern Name]

### 패턴 설명
[Pattern description]

### 사용 사례
[When to use]

### 예제 코드
\`\`\`typescript
[Code example]
\`\`\`

### 주의사항
[Pitfalls or considerations]
`
})

// Dependencies changelog
mcp__plugin_workflow-skills_serena__write_memory({
  memory_file_name: "dependencies_changelog.md",
  content: `
## 2025-01-15 - 의존성 변경

### 추가된 의존성
- \`package-name@version\`: [Why added]

### 업데이트된 의존성
- \`package@old\` → \`@new\`: [Why updated]

### 제거된 의존성
- \`package-name\`: [Why removed]
`
})

// Testing patterns
mcp__plugin_workflow-skills_serena__write_memory({
  memory_file_name: "testing_patterns.md",
  content: `
## 2025-01-15 - [Test Category]

### 테스트 전략
[Testing approach used]

### 테스트 예제
\`\`\`typescript
[Example test code]
\`\`\`

### 테스트 실행
[Commands to run tests]
`
})
```

---

### Phase 6: JIRA Issue Update

**Objective**: Summarize implementation completion and add comments to JIRA issue.

⚠️ **Important**: Execute only when JIRA issue ID can be found in workflow artifacts.

#### 6A. Extract JIRA Issue ID

```typescript
// From workflow artifacts, extract JIRA issue ID
// Look for patterns like: ISSUE-123, PROJECT-456, etc.
// Common locations:
// - Report file name: ISSUE-123_REPORT.md
// - Plan file "Based On" field
// - Branch name: feature/ISSUE-123-description

// Extract issue ID
const issueId = extractedFromArtifacts; // e.g., "PROJECT-123"
```

#### 6B. Get Current Issue Status

```typescript
// Get issue details
mcp__plugin_workflow-skills_atlassian__jira_get_issue({
  issue_key: issueId
})

// Check current status:
// - If "In Progress" → Can transition to "Done"
// - If "To Do" → Should be "In Progress" first
// - If "Done" → Just add comment
```

#### 6C. Prepare Implementation Summary

Write implementation summary based on workflow artifacts:

```markdown
## 구현 완료 요약

### 변경사항
- ✅ [주요 기능 1]: [설명]
- ✅ [주요 기능 2]: [설명]
- ✅ [버그 수정]: [설명]

### 구현 세부사항
**파일 변경:**
- `src/feature/module.ts`: [변경 내용]
- `src/api/endpoint.ts`: [새 엔드포인트 추가]

**테스트:**
- ✅ 단위 테스트 추가 ([X]개)
- ✅ 통합 테스트 추가 ([Y]개)
- ✅ 모든 테스트 통과

**문서:**
- ✅ README 업데이트
- ✅ CHANGELOG 업데이트
- ✅ API 문서 업데이트

### 관련 문서
- README: [변경된 섹션]
- CHANGELOG: [Unreleased] 섹션
- 기술 문서: [링크 또는 위치]

### 테스트 방법
```bash
# 테스트 실행 명령어
npm test

# 기능 확인 방법
[실행 예제]
```

### 배포 노트
- **Breaking Changes**: [있다면 명시]
- **Dependencies**: [새로 추가된 의존성]
- **Configuration**: [새 환경 변수나 설정]
```

#### 6D. Add Comment to JIRA Issue

```typescript
// Add comprehensive comment
mcp__plugin_workflow-skills_atlassian__jira_add_comment({
  issue_key: issueId,
  comment: `
## ✅ 구현 완료

### 변경사항
- ✅ [주요 기능 1]: [설명]
- ✅ [주요 기능 2]: [설명]

### 구현 세부사항
**파일 변경:**
- \`src/feature/module.ts\`: [변경 내용]

**테스트:**
- ✅ 단위 테스트: [X]개 추가
- ✅ 통합 테스트: [Y]개 추가
- ✅ 모든 테스트 통과

**문서:**
- ✅ README 업데이트 완료
- ✅ CHANGELOG 업데이트 완료

### 관련 커밋
- [commit hash or PR link if available]

### 테스트 방법
\`\`\`bash
npm test
\`\`\`

### 다음 단계
- [ ] 코드 리뷰
- [ ] QA 테스트
- [ ] 프로덕션 배포

---
*문서 업데이트: ${new Date().toISOString().split('T')[0]}*
`
})
```

#### 6E. Transition Issue Status (Optional)

```typescript
// Get available transitions
mcp__plugin_workflow-skills_atlassian__jira_get_transitions({
  issue_key: issueId
})

// If "Done" transition is available and appropriate:
mcp__plugin_workflow-skills_atlassian__jira_transition_issue({
  issue_key: issueId,
  transition_id: doneTransitionId  // From available transitions
})
```

#### 6F. Verification

```
- ✅ JIRA 이슈 코멘트 추가됨
- ✅ 구현 사항 상세히 기록됨
- ✅ 테스트 정보 포함됨
- ✅ 문서 링크 포함됨
- ✅ 이슈 상태 업데이트됨 (if applicable)
```

**⚠️ Important Notes**:
- Skip this step if JIRA issue ID cannot be found
- Issue status transition is optional depending on team workflow
- Do not include sensitive information (passwords, keys, etc.) in comments

---

### Phase 7: Additional Documentation

**Objective**: Create additional documentation as needed.

#### 6A. Migration Guide (if breaking changes)

```markdown
# Migration Guide: [Old Version] → [New Version]

## 개요
[What changed and why]

## 중단되는 변경사항

### [Change 1]

**변경 전:**
\`\`\`typescript
[Old code]
\`\`\`

**변경 후:**
\`\`\`typescript
[New code]
\`\`\`

**마이그레이션 단계:**
1. [Step 1]
2. [Step 2]
```

#### 6B. API Documentation (if API changed)

- Create/update OpenAPI/Swagger spec
- Generate API docs from code comments
- Add request/response examples

#### 6C. Architecture Diagrams (if significant changes)

- Update architecture documentation
- Create/update diagrams
- Document component interactions

---

### Phase 8: Verification and Quality Check

**Objective**: Verify documentation quality.

#### 8A. Completeness Check

```
- [ ] README reflects all new features
- [ ] README includes all API changes
- [ ] README has updated configuration
- [ ] CHANGELOG has proper entries
- [ ] CHANGELOG follows Keep a Changelog format
- [ ] CLAUDE docs updated with decisions
- [ ] Serena memories saved for key insights
- [ ] JIRA issue updated (if applicable)
- [ ] All code examples are correct
- [ ] All links work properly
```

#### 8B. Consistency Check

```
- [ ] Terminology is consistent
- [ ] Version numbers match
- [ ] Dates are correct
- [ ] Formatting is uniform
- [ ] Language (Korean) used consistently
```

#### 8C. Quality Check

```
- [ ] Information is clear and concise
- [ ] Technical details are accurate
- [ ] Examples are complete and runnable
- [ ] No sensitive information exposed
- [ ] Cross-references are valid
```

---

### Phase 9: Cleanup Workflow Artifacts

**Objective**: Clean up workflow artifacts.

⚠️ **Note**: The `execute` skill does not clean up files. This skill cleans up all workflow artifacts.

#### 9A. Identify Remaining Files

```typescript
// Find remaining artifacts
Glob({pattern: "*_REPORT.md"})
Glob({pattern: "*_PLAN.md"})
Glob({pattern: "*_REVIEW.md"})
```

#### 9B. Confirm with User

```
문서화가 완료되었습니다.

다음 정보가 저장되었습니다:
- README: [변경된 섹션 목록]
- CHANGELOG: [새 엔트리 추가됨]
- CLAUDE 문서: [업데이트된 섹션]
- Serena 메모리: [저장된 메모리 목록]

남아있는 임시 파일들을 정리할 수 있습니다:
- [REPORT_FILE_1]
- [PLAN_FILE_1]

옵션:
1. 삭제 (완전히 제거)
2. 아카이브 (.claude/archives/로 이동)
3. 유지 (그대로 두기)

어떻게 처리할까요?
```

#### 9C. Execute Cleanup

Based on user choice:

**Option 1: Delete**
```bash
rm [files]
```

**Option 2: Archive**
```bash
mkdir -p .claude/archives/$(date +%Y-%m)
mv [files] .claude/archives/$(date +%Y-%m)/
```

**Option 3: Keep**
- Do nothing

#### 9D. Git Commit and Push

After all documentation updates are complete:

```bash
echo "🔍 Checking changes..."

# Check for uncommitted changes
if [ -n "$(git status --porcelain)" ]; then
  echo "⚠️ There are uncommitted changes:"
  git status --short
  echo ""
  read -p "Do you want to commit all changes? [y/N] " -n 1 -r
  echo
  if [[ $REPLY =~ ^[Yy]$ ]]; then
    git add .
    git commit -m "docs: update documentation for $(git branch --show-current)

Generated by record skill"
    echo "✅ Changes committed"
  else
    echo "ℹ️ Uncommitted changes remain"
    echo "Commit manually later: git add . && git commit"
  fi
fi

# Check for unpushed commits
UNPUSHED=$(git rev-list @{u}..HEAD --count 2>/dev/null || echo "0")
if [ "$UNPUSHED" -gt 0 ]; then
  echo "⚠️ There are $UNPUSHED unpushed commits"
  echo ""
  read -p "Do you want to push now? [y/N] " -n 1 -r
  echo
  if [[ $REPLY =~ ^[Yy]$ ]]; then
    git push origin $(git branch --show-current)
    echo "✅ Push completed"
  else
    echo "ℹ️ Push manually later: git push"
  fi
else
  echo "✅ All changes have been pushed"
fi
```

**When to run**: After all documentation updates are complete (after Phase 1-8).

---

## Final Documentation Summary

Present comprehensive summary **in Korean**:

```markdown
# 문서화 완료 요약

## 업데이트된 문서

### 📘 README.md
- **Features 섹션**: [추가된 기능 목록]
- **API 섹션**: [추가/변경된 엔드포인트]
- **Configuration 섹션**: [새 환경 변수]
- **Breaking Changes**: [if any]

### 📝 CHANGELOG.md
- **버전**: [Unreleased] / [X.Y.Z]
- **Added**: [X개 항목]
- **Changed**: [Y개 항목]
- **Fixed**: [Z개 항목]

### 🤖 CLAUDE Documentation
- **아키텍처 결정**: [새 결정사항]
- **문제해결 가이드**: [새 이슈 해결방법]

### 🧠 Serena Memories
- `architecture_decisions.md`: [저장된 결정사항]
- `known_issues.md`: [저장된 이슈 정보]
- `code_patterns.md`: [저장된 패턴]
- `dependencies_changelog.md`: [의존성 변경]
- `testing_patterns.md`: [테스트 패턴]

### 📋 JIRA Issue (if applicable)
- **이슈**: [ISSUE-123]
- **코멘트 추가**: 구현 완료 사항, 테스트 정보, 문서 링크
- **상태 업데이트**: [In Progress → Done] (if applicable)

### 📚 추가 문서 (if created)
- [Migration Guide]
- [API Documentation]
- [Architecture Diagrams]

## 처리된 워크플로우 아티팩트

### 분석 리포트
- [REPORT files] → 문서에 반영

### 작업 계획
- [PLAN files] → 문서에 반영

### 구현 결과
- [Implementation details] → 모든 문서에 반영

## 정리 현황

### 아카이브됨
- [Files] → .claude/archives/YYYY-MM/

### 삭제됨
- [Files] → 완전히 제거됨

### 유지됨
- [Files] → 참고용으로 보관

## 문서 품질

- ✅ 완성도: 모든 주요 변경사항 문서화
- ✅ 일관성: 용어 및 형식 통일
- ✅ 정확성: 기술적 세부사항 검증
- ✅ 접근성: 명확하고 이해하기 쉬운 설명
- ✅ 유지보수성: 향후 업데이트 용이

## 다음 단계

1. 문서 검토 및 추가 수정
2. Git commit으로 문서 변경사항 저장:
   ```bash
   git add README.md CHANGELOG.md .claude/
   git commit -m "docs: update documentation after implementation"
   ```
3. 문서 배포 (if applicable)
4. 팀원들에게 변경사항 공유
```

---

## Important Guidelines

- **Be thorough**: Don't miss important changes
- **Be accurate**: Verify all technical details
- **Be organized**: Keep documentation structure clean
- **Be consistent**: Use same terminology and formatting
- **Be user-focused**: Write for developers who will read this later
- **Use Korean**: Use Korean for non-code/technical terms (per language policy)
- **Preserve history**: Archive instead of delete when possible
- **Think sequentially**: Use Sequential Thinking to organize information logically

---

## Integration with Workflow

**Typical Usage**:
```
analyze
  → plan
  → execute (code implementation + tests)
  → record (documentation + Git commit/push)
```

**When to Use**:
- After `execute` completes, when **documentation and Git operations are needed**
- When CHANGELOG update is required
- When architecture decisions need to be added to CLAUDE docs
- When detailed technical insights need to be saved to Serena memory
- When migration guide or additional API documentation is needed

---

## Error Handling

**If No Artifacts Found**:
- Inform user no documentation to process
- Suggest checking file locations
- Ask if user wants to manually specify files

**If Documentation Files Don't Exist**:
- Offer to create them (README, CHANGELOG, etc.)
- Use standard templates
- Ask user for project-specific information

**If Conflicting Information**:
- Flag conflicts for user review
- Present options for resolution
- Wait for user decision before proceeding

---

## Resources

This skill does not require additional resource directories (scripts/, references/, or assets/). All documentation logic is contained within this SKILL.md file, and the skill relies on Claude's ability to:

1. Use Glob/Read tools to find and read artifacts
2. Use Edit/Write tools to update documentation
3. Use Serena MCP tools for memory storage
4. Use Atlassian MCP tools for JIRA integration
5. Use Sequential Thinking for organization
6. Follow the 9-phase systematic documentation process
7. Maintain comprehensive documentation quality
8. Handle cleanup with user confirmation

The skill is self-contained and ready for use without external dependencies.
