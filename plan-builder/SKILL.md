---
name: plan-builder
description: Create high-quality, thoroughly reviewed implementation plans through iterative refinement. Automatically cycles through plan creation, critical review, and feedback application until the plan meets all quality standards. Use when creating implementation plans from analysis reports or requirements, especially for complex features or critical bug fixes that need thorough planning.
---

# Plan Builder - Iterative Plan Refinement

## ⚠️ CRITICAL LANGUAGE POLICY

**DEFAULT LANGUAGE: KOREAN (한국어)**

ALL outputs, documentation, plans, and communications MUST be in **KOREAN** unless explicitly requested otherwise by the user.

- ✅ **Plan files**: Write in Korean
- ✅ **Reviews**: Perform in Korean
- ✅ **Feedback**: Provide in Korean
- ✅ **Task descriptions**: Write in Korean
- ✅ **User communication**: Respond in Korean

**Exception**: If the user writes in another language, match that language for responses.

**This is a MANDATORY requirement. Do NOT default to English.**

---

## ⛔ MANDATORY: Feedback Loop Until Perfect

> **NEVER stop after just one feedback cycle.**
>
> After reviewing the plan, if there are any changes needed:
> 1. Apply feedback
> 2. Review again
> 3. Still have changes? → Go back to step 1
> 4. **Only exit when there are ZERO remaining issues**
>
> It is NORMAL to iterate **at least 2-3 times**.
> If the first review gives "Approve", the review was too lenient.

---

## Overview

Create production-ready implementation plans through systematic refinement cycles. This skill combines plan creation, critical review, and feedback application in an automated loop, continuously improving the plan until it achieves high quality standards.

**Process**: Plan → Review → Apply Feedback → Review Again → Apply Feedback → Review Again → ... → Final Approved Plan

**Output**:
- `[FEATURE]_PLAN.md` - Thoroughly reviewed, production-ready implementation plan
- Intermediate review artifacts automatically cleaned up

**Key Feature**: Iterates automatically until **zero issues remain** in the review process. NOT just once, but repeatedly until perfect.

## When to Use This Skill

Use this skill when:
- Creating implementation plans from `*_REPORT.md` analysis files
- Planning complex features or architectural changes
- Need high-confidence plans before execution
- User requests "create a plan", "plan this feature", "how should we implement this"
- Starting from JIRA requirements or GitHub issues
- Want automated quality assurance before plan execution

**vs. Simple Planning**: Use this skill when quality and thoroughness matter more than speed.

## Planning Workflow

### Phase 1: Initial Plan Creation

Create a structured task plan from requirements or analysis reports.

**1. Context Gathering**

Collect all necessary context:

- **Analysis Reports** (if available):
  - Read `*_REPORT.md` files using Read tool
  - Extract root cause, affected code locations
  - Identify recommended fixes and testing requirements

- **JIRA/Atlassian**:
  - Use `mcp__atlassian__getJiraIssue` for requirements
  - Extract acceptance criteria and constraints
  - Check linked issues and dependencies

- **GitHub Repository** (if provided):
  - Use `mcp__github__repository_info` for structure
  - Use `mcp__github__list_files` to explore codebase
  - Use `mcp__github__search_repository` for patterns
  - Review recent PRs and issues

- **Codebase** (using Serena):
  - Use `mcp__serena__check_onboarding_performed`
  - Use `mcp__serena__list_memories` for project knowledge
  - Use `mcp__serena__find_symbol` for affected components
  - Use `mcp__serena__search_for_pattern` for similar code

- **Framework Documentation** (using Context7):
  - Use `mcp__context7__get-library-docs` for best practices
  - Verify API usage and patterns

**2. Plan Structure Creation**

Use `mcp__sequential-thinking__sequentialthinking` to organize the plan.

Reference the template in `references/plan_template.md` for complete structure.

**Critical Requirements**:
- ✅ Every task MUST have Testing Strategy
- ✅ Tasks must be independently verifiable
- ✅ Clear success criteria for each task
- ✅ Explicit dependencies (or "None")

**3. Save Initial Plan**

- Save as `[FEATURE]_PLAN.md`
- Use UPPERCASE for feature name
- Examples: `USER_AUTH_PLAN.md`, `API_REFACTOR_PLAN.md`

### Phase 2: Iterative Review & Refinement

⚠️ **This phase does NOT end after one cycle. Repeat until ZERO changes remain.**

Automatically enter review-refinement loop until plan meets quality standards.

**Review Loop Process:**

```
┌─────────────────────────────────────────────────────────┐
│  ⚠️ This loop repeats until 0 issues remain             │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  ┌──────────────────┐                                   │
│  │ 1. Review Plan   │ ← Phase 2A                        │
│  └────────┬─────────┘                                   │
│           ▼                                             │
│  ┌──────────────────┐                                   │
│  │ 2. Check Result  │ ← Phase 2B                        │
│  └────────┬─────────┘                                   │
│           │                                             │
│     ┌─────┴─────┐                                       │
│     ▼           ▼                                       │
│  Changes?    No Changes?                                │
│     │           │                                       │
│     ▼           ▼                                       │
│  ┌──────────┐  ┌──────────┐                             │
│  │ Apply    │  │ Phase 3  │                             │
│  │ Feedback │  │ Done!    │                             │
│  └────┬─────┘  └──────────┘                             │
│       │                                                 │
│       └────────────► Back to Step 1 (Review Again!)     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**⛔ FORBIDDEN**: Going straight to Phase 3 after the first review

#### Phase 2A: Critical Plan Review

Perform comprehensive plan review using `references/review_checklist.md`.

**Use `mcp__sequential-thinking__sequentialthinking` to analyze:**

**1. GitHub Repository Validation** (if applicable)
- Code style alignment
- Architecture compatibility
- Dependency conflicts
- Test coverage impact

**2. Completeness Analysis**
- Missing components or steps
- Overlooked edge cases
- Error handling
- Rollback procedures
- Data migration needs
- Backward compatibility

**3. Task Independence Review** (CRITICAL)

See `references/task_independence_guide.md` for detailed guidance.

For each task, verify:
- ✅ Can be implemented independently
- ✅ Dependencies explicit and clear
- ✅ Has isolated test scope
- ✅ Success measurable without other tasks

**4. Testing Strategy Verification** (CRITICAL)

See `references/testing_strategy_guide.md` for detailed guidance.

For each task, check:
- ✅ Testing Strategy section exists
- ✅ Test type appropriate
- ✅ Test cases specific and concrete
- ✅ Acceptance criteria measurable
- ✅ Test commands provided
- ✅ Covers happy path AND edge cases

**5. Feasibility Assessment**
- Technical viability
- Resource requirements
- Time estimates
- Required skills

**6. Risk Analysis**
- Critical risks identified
- Likelihood and impact assessed
- Mitigation strategies present

**7. Quality & Best Practices**
- Code quality standards
- Security considerations
- Performance implications
- Maintainability

**Review Output Structure:**

```markdown
# Plan Review: [Plan Name]

## Executive Summary
- **Overall Assessment**: Strong / Good / Needs Improvement / Major Concerns
- **Recommendation**: Approve / Approve with Changes / Major Revision Needed

## Critical Findings

### Required Changes (Must Fix) 🔴
1. [Critical issue that must be fixed]

### Suggested Improvements (Should Fix) 🟡
1. [Important improvement]

### Optional Enhancements (Nice to Have) 🟢
1. [Enhancement]

### Questions for Clarification ❓
1. [Ambiguous aspect]
```

**Save review as**: `[FEATURE]_PLAN_REVIEW.md`

#### Phase 2B: Assessment Decision

Analyze the review recommendation:

**⛔ STRICT Approval Criteria (ALL must be true):**
- Overall Assessment: "Strong" only (NOT "Good" - that needs more work)
- Recommendation: "Approve" only (NOT "Approve with Changes")
- Required Changes (🔴): ZERO
- Suggested Improvements (🟡): ZERO or trivial only
- No open questions remaining

**If ANY issues remain → Apply feedback and review again!**

**If Approved (truly ZERO issues):**
- Announce: "✅ Plan approved after [N] iterations - ZERO remaining issues!"
- Proceed to Phase 3 (Finalization)

**If Changes Needed (even minor ones):**
- Announce: "🔄 Applying review feedback (Iteration N)..."
- Proceed to Phase 2C (Apply Feedback)
- **Then return to Phase 2A for another review**

#### Phase 2C: Apply Review Feedback

Systematically apply all review feedback to improve the plan.

**1. Parse Review Feedback**

Extract and categorize:
- Required Changes (Must Fix) - Priority 1
- Testing Strategy Issues - Priority 1
- Task Independence Issues - Priority 1
- Suggested Improvements - Priority 2
- Optional Enhancements - Priority 3

**2. Address Required Changes**

For each required change:
1. Locate section in plan using Read tool
2. Apply recommended change using Edit tool
3. Verify change addresses feedback
4. Ensure consistency with rest of plan

**3. Fix Testing Strategies**

For tasks missing or having inadequate testing, refer to `references/testing_strategy_guide.md` for examples and add:

- Test type (Unit/Integration/Manual/E2E)
- Specific, concrete test cases
- Measurable acceptance criteria
- Runnable test commands

**4. Resolve Task Independence Issues**

Refer to `references/task_independence_guide.md` for guidance on:
- Splitting tightly coupled tasks
- Defining clear interfaces
- Extracting shared functionality
- Making tasks independently testable

**5. Apply Suggested Improvements**

Evaluate and apply improvements that add value while maintaining plan consistency.

**6. Verification After Changes**

Use `mcp__sequential-thinking__sequentialthinking` to verify:
- [ ] All required changes applied
- [ ] All testing strategies complete
- [ ] All task independence issues resolved
- [ ] Plan structure maintained
- [ ] Dependencies still correct

**7. Update and Clean Up**

- Save updated `[FEATURE]_PLAN.md`
- **Delete `[FEATURE]_PLAN_REVIEW.md` immediately** (outdated after applying feedback)
- **⚠️ MANDATORY: Loop back to Phase 2A for another review**
  - Do NOT skip this step
  - The new review will check if applied changes are correct
  - Continue until review finds ZERO issues

### Phase 3: Finalization

When plan receives approval, finalize and clean up.

**1. Final Quality Check**

Verify approved plan meets all standards using checklist in `references/review_checklist.md`.

**2. Clean Up Review Artifacts**

```bash
# Delete any remaining review files
rm [FEATURE]_PLAN_REVIEW.md
```

**3. Final Output Summary**

```markdown
# ✅ Plan Creation Complete

## Final Plan
- **File**: [FEATURE]_PLAN.md
- **Quality**: Approved after [N] review iterations
- **Tasks**: [X] tasks across [Y] priorities

## Review Iterations
1. Initial Plan → [Issues found]
2. Iteration 1 → [Improvements made]
3. Iteration N → [Approved] ✅

## Key Improvements Made
- [Improvement 1]
- [Improvement 2]

## Ready for Execution
Run: `/execute-plan [FEATURE]_PLAN.md`
```

## Best Practices

**Planning:**
- Start with comprehensive context gathering
- Use sequential thinking for organization
- Be specific about testing requirements
- Make tasks independently verifiable

**Reviewing:**
- Be thorough and critical
- Focus on Required Changes vs Nice-to-Have
- Verify every task has testing strategy
- Check task independence carefully

**Applying Feedback:**
- Address Required Changes first
- Don't skip testing strategy fixes
- Verify changes after applying
- Maintain plan consistency

**Iteration (⚠️ CRITICAL):**
- **NEVER stop after just 1 iteration** - this is almost always wrong
- Minimum 2-3 iterations is NORMAL and expected
- Complex plans may need 4-5+ iterations
- Each iteration should show improvement
- Only stop when review shows **ZERO remaining issues**
- Delete review file after each feedback application
- Quality over speed - rushing leads to bad plans

**When to Ask User:**
- Clarifying ambiguous requirements
- Choosing between valid approaches
- Confirming major architectural decisions
- Resolving conflicting constraints

## Integration with Workflow

```
1. analyze-issue [JIRA]
   └─> [ISSUE_ID]_REPORT.md

2. plan-builder [uses REPORT]
   └─> [FEATURE]_PLAN.md (approved)
   └─> Auto-iterates until high quality

3. /execute-plan [PLAN]
   └─> Implementation

4. /document
   └─> Final documentation
```

**Key Benefit**: Automatically ensures plan quality before execution.

## Common Iteration Patterns

**Why Multiple Iterations Are Necessary:**
- First draft ALWAYS has issues - that's normal
- Each review catches different problems
- Fixing one issue often reveals others
- Quality compounds with each iteration

**Typical Pattern:**
| Iteration | Common Issues Found | Action |
|-----------|---------------------|--------|
| 1 | Missing testing strategies, vague acceptance criteria | Apply feedback → Review again |
| 2 | Task coupling, missing edge cases, incomplete error handling | Apply feedback → Review again |
| 3 | Minor polish, edge cases, documentation gaps | Apply feedback → Review again |
| 4+ | Complex edge cases (if any remain) | Continue until ZERO issues |

**⚠️ Red Flag**: If iteration 1 shows "Approve" → Review was too lenient, be more critical!

## Resources

### references/

- `plan_template.md` - Comprehensive template for task plans
- `review_checklist.md` - Detailed review evaluation criteria
- `testing_strategy_guide.md` - How to write effective testing strategies
- `task_independence_guide.md` - Ensuring tasks can be developed and tested independently

These references support each phase of the planning and review cycle.
