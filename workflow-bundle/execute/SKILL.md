---
name: execute
description: Execute approved implementation plans with TodoList tracking, test verification, and success criteria validation. Use when you have an approved *_PLAN.md file and need step-by-step implementation with comprehensive tracking. Outputs implemented code, test results, and execution summary. Documentation handled by record skill. Korean triggers: 계획 실행, 플랜 실행, 구현 시작, 개발 시작, 코딩 시작, 작업 시작, 실행해줘, 구현해줘, 만들어줘, 개발해줘, 코드 작성, 기능 구현, 태스크 실행.
---

# Execute Plan

## ⚠️ CRITICAL LANGUAGE POLICY

**DEFAULT LANGUAGE: KOREAN (한국어)**

ALL outputs, documentation, code comments, and communications MUST be in **KOREAN** unless explicitly requested otherwise by the user.

- ✅ **Code comments**: Write in Korean
- ✅ **Documentation**: Write in Korean
- ✅ **TodoList items**: Write in Korean
- ✅ **Progress updates**: Provide in Korean
- ✅ **User communication**: Respond in Korean

**Exception**: If the user writes in another language, match that language for responses.

**This is a MANDATORY requirement. Do NOT default to English.**

---

## When to Use This Skill

Use this skill when:
- You have an approved implementation plan file (e.g., `USER_AUTH_PLAN.md`)
- User requests "execute this plan", "implement the plan", "run execute"
- Need to systematically implement multiple related tasks
- Want automatic progress tracking with TodoList
- Need to ensure all success criteria and tests are verified

**Typical Workflow Position**:
```
analyze → plan → **execute** → record
```

## Overview

This skill executes approved implementation plans through a 7-phase systematic process:

0. **Branch Validation**: Verify working on a feature branch
1. **Plan Loading & Validation**: Load plan file, parse tasks, verify prerequisites
2. **TodoList Setup**: Create comprehensive TodoList from all plan tasks
3. **Task Execution**: Execute tasks sequentially, respecting dependencies
4. **Handle Dependencies**: Manage task dependencies and execution order
5. **Automated Test Generation** (Conditional): Detect missing tests and generate them
6. **Testing & Verification**: Run tests and verify success criteria

**Note**:
- Phase 4C (DB Migration Validation) and Phase 5 (Test Generation) are optional
- **Documentation (README, CHANGELOG, Git commit) is handled by `record` skill** - not in execute

---

## Workflow: 7-Phase Execution Process

### Phase 0: Branch Validation

⚠️ **CRITICAL: DO NOT SKIP PHASE 0**

> **MANDATORY REQUIREMENT**:
>
> - Phase 0 is the **FIRST step** of this skill
> - You **MUST** execute Phase 0 **BEFORE** proceeding to Phase 1
> - **DO NOT** assume you are on the correct branch
> - **ALWAYS** verify branch status explicitly by running the bash commands below
> - **NEVER** start code modification (Phase 1) without completing Phase 0
>
> **Why this matters**:
> - Code changes in main/master branch can cause merge conflicts
> - Modifying main/master while working on features is dangerous
> - Branch isolation prevents accidental commits to production branches

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
  echo "🔧 Recommended workflow:"
  echo "  1. First run analyze [JIRA-ID] to create a feature branch"
  echo "  2. Then run plan and execute"
  echo ""
  echo "⚠️ Modifying code on protected branches risks branch conflicts"
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

- Execute existing Phase 1-7
- Code modifications happen in the current feature branch

---

### Phase 1: Plan Loading and Validation

**Objective**: Load and understand the plan, validate prerequisites are met.

**Steps**:

1. **Load the Plan File**
   ```typescript
   // Use Read tool to load the plan
   Read(planFilePath)
   ```

2. **Parse Plan Structure**
   - Use `mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking` to understand:
     - Task breakdown and priorities (P0 → P1 → P2 → P3)
     - Dependencies between tasks
     - Success criteria for each task
     - Testing strategies
     - Risk mitigations
     - Expected tools and MCP servers

3. **Validate Prerequisites**
   - Check codebase context:
     ```typescript
     mcp__plugin_workflow-skills_serena__check_onboarding_performed()
     mcp__plugin_workflow-skills_serena__get_current_config()
     ```
   - Verify required MCP servers are accessible
   - Confirm tools specified in plan are available
   - Check that dependencies (libraries, services) are ready

4. **Report Validation Results**
   - ✅ All prerequisites met → Proceed to Phase 2
   - ❌ Blockers found → Report issues, get user guidance

---

### Phase 2: Setup TodoList from Plan

**Objective**: Create comprehensive TodoList mirroring the entire plan.

**TodoList Structure**:
```
TodoWrite({
  todos: [
    {content: "[P0] Setup authentication infrastructure", status: "in_progress", activeForm: "Setting up authentication infrastructure"},
    {content: "[P0] Configure OAuth2 providers (depends on setup)", status: "pending", activeForm: "Configuring OAuth2 providers"},
    {content: "[P1] Implement user session management", status: "pending", activeForm: "Implementing user session management"},
    {content: "[P1] Create login/logout endpoints", status: "pending", activeForm: "Creating login/logout endpoints"},
    {content: "[P2] Add user profile page", status: "pending", activeForm: "Adding user profile page"},
    {content: "[P3] Implement remember me feature", status: "pending", activeForm: "Implementing remember me feature"}
  ]
})
```

**TodoList Principles**:
- Include ALL tasks from the plan (no skipping)
- Order by priority: P0 → P1 → P2 → P3
- Note dependencies in task descriptions
- Start with first task as "in_progress"
- Exactly ONE task should be "in_progress" at any time

---

### Phase 3: Execute Tasks Sequentially

**Objective**: Implement each task following the plan's approach.

**For Each Task:**

#### 3A. Start Task
```typescript
// Mark current task as in_progress
TodoWrite({todos: [...previousTodos, {content: taskName, status: "in_progress"}]})

// Review task details
- Goal: What this task achieves
- Success Criteria: How to verify completion
- Dependencies: What must be done first
- Implementation Approach: Step-by-step guide
- Testing Strategy: How to test this task
```

#### 3B. Gather Context

Use appropriate tools based on task needs:

**Code Reading/Understanding**:
```typescript
// Get file overview
mcp__plugin_workflow-skills_serena__get_symbols_overview({relative_path: "src/auth/service.ts"})

// Find specific symbols
mcp__plugin_workflow-skills_serena__find_symbol({name_path: "AuthService/login", include_body: true})

// Find references
mcp__plugin_workflow-skills_serena__find_referencing_symbols({name_path: "login", relative_path: "src/auth/service.ts"})

// Search patterns
mcp__plugin_workflow-skills_serena__search_for_pattern({substring_pattern: "OAuth.*config"})
```

**Documentation**:
```typescript
// Get library docs
mcp__plugin_workflow-skills_context7__resolve-library-id({libraryName: "passport"})
mcp__plugin_workflow-skills_context7__get-library-docs({context7CompatibleLibraryID: "/jaredhanson/passport"})
```

**Project Management**:
```typescript
// Get JIRA details
mcp__plugin_workflow-skills_atlassian__jira_get_issue({issue_key: "PROJ-123"})

// Check Sentry errors
mcp__plugin_workflow-skills_sentry__get_issue_details({organizationSlug, issueId})
```

#### 3C. Implement Task

Follow the implementation approach defined in the plan:

**Code Editing**:
```typescript
// Replace symbol body
mcp__plugin_workflow-skills_serena__replace_symbol_body({
  name_path: "AuthService/login",
  relative_path: "src/auth/service.ts",
  body: newImplementation
})

// Insert new code
mcp__plugin_workflow-skills_serena__insert_after_symbol({
  name_path: "AuthService",
  relative_path: "src/auth/service.ts",
  body: newMethodCode
})

// Rename symbols
mcp__plugin_workflow-skills_serena__rename_symbol({
  name_path: "oldName",
  relative_path: "src/auth/service.ts",
  new_name: "newName"
})
```

**Apply Risk Mitigations**:
- Refer to risk mitigation strategies in the plan
- Implement safeguards as specified
- Add error handling as noted

#### 3D. Verify Success Criteria

Check ALL success criteria for the task:

```typescript
// Use thinking tool to assess
mcp__plugin_workflow-skills_serena__think_about_task_adherence()

// For each criterion:
- [ ] Criterion 1: [Check if met]
- [ ] Criterion 2: [Check if met]
- [ ] Criterion 3: [Check if met]
```

**Run Tests**:
```bash
# Execute test commands from task's Testing Strategy
npm test src/auth/login.test.ts
pytest tests/test_auth.py -v
```

**Verify Test Results**:
- All tests pass ✅
- No regressions introduced ✅
- Coverage meets targets ✅

#### 3E. Complete Task

```typescript
// Mark task as completed
TodoWrite({todos: [...previousTodos, {content: taskName, status: "completed"}]})

// Document deviations (if any)
"✅ Task completed: [Task Name]
 - All success criteria met
 - Tests passing: [Test results]
 - Deviations: [None / List deviations]"

// Move to next task
"🔄 Moving to next task: [Next Task Name]"
```

**Progress Reporting**:
```
📊 Progress: [X/Y tasks completed]
✅ Completed: [Recent task]
🔄 Current: [Next task description]
⏳ Next: [Following task]
🚫 Blocked: [Any blocked tasks]
```

---

### Phase 4: Handle Dependencies

**Objective**: Manage task dependencies and execution order.

**Dependency Rules**:
1. Before starting a task, verify ALL dependencies are completed
2. If dependency not met, skip to next available task
3. Track blocked tasks and revisit when unblocked
4. Follow critical path identified in plan

**Example Flow**:
```
Task A (completed) → Task B (in_progress) → Task D (pending, blocked by B)
                  ↘ Task C (pending, no deps) → Can start C in parallel
```

**Handling Blocked Tasks**:
- Mark task as "pending" (not "in_progress" if blocked)
- Document blocker reason
- Move to next unblocked task
- Return to blocked task when dependency resolves

---

### Phase 4C: Database Migration Validation (Optional)

**Objective**: Automatically detect and block dangerous DB migration patterns

**Execution Condition**: When the plan contains DB migration related tasks

**Steps**:

**1. Detect Migration Files**

```bash
# Find SQL/migration files in migrations/ or db/ directories
find . -path "*/migrations/*.sql" -o -path "*/db/migrate/*.rb" -o -path "*/migrations/*.ts"
```

**2. Analyze Dangerous Patterns**

Detect dangerous patterns using the following regular expressions:

```typescript
const dangerousPatterns = {
  // CRITICAL: Data loss risk
  notNull: /ADD COLUMN .* NOT NULL(?! DEFAULT)/i,
  dropColumn: /DROP COLUMN/i,
  dropTable: /DROP TABLE/i,

  // HIGH: Performance issues
  alterType: /ALTER COLUMN .* TYPE/i,
  addIndex: /CREATE INDEX(?! CONCURRENTLY)/i
};

// Scan each migration file
for (const file of migrationFiles) {
  const content = readFile(file);

  for (const [risk, pattern] of Object.entries(dangerousPatterns)) {
    if (pattern.test(content)) {
      warnings.push({
        file,
        risk,
        severity: (risk === 'notNull' || risk === 'dropColumn' || risk === 'dropTable')
          ? 'CRITICAL'
          : 'HIGH'
      });
    }
  }
}
```

**3. Warning and Approval Request**

When dangerous patterns are found, output warning messages in the following format:

```markdown
## ⚠️ Database Migration Risk Detected

### CRITICAL Risk
- **File**: migrations/20231209_add_user_email.sql
  - **Pattern**: `ADD COLUMN email VARCHAR(255) NOT NULL`
  - **Problem**: Existing rows cannot have NULL → Migration fails
  - **Solution**: Add DEFAULT or 2-step migration (1. ADD COLUMN with DEFAULT, 2. ALTER COLUMN DROP DEFAULT)

### HIGH Risk
- **File**: migrations/20231209_alter_user_type.sql
  - **Pattern**: `ALTER COLUMN user_type TYPE VARCHAR(50)`
  - **Problem**: Table lock occurs, long duration on large tables
  - **Solution**: Add new column → Copy data → Drop old column (Zero-downtime migration)

**Action Required**:
- Stop execution on CRITICAL risk
- Proceed with user approval for HIGH risk
```

**4. Execution Stop Logic**

```typescript
if (warnings.some(w => w.severity === 'CRITICAL')) {
  console.log('❌ CRITICAL migration risk detected - execution stopped');
  console.log('Please fix migration files and try again.');
  // Exit without returning to Phase 3 Task Execution
  process.exit(1);
}

if (warnings.some(w => w.severity === 'HIGH')) {
  console.log('⚠️ HIGH migration risk detected - user approval required');
  // Request user approval
}
```

**5. Detect Dangerous Patterns with Grep (Implementation Example)**

```bash
# NOT NULL without DEFAULT
grep -rE 'ADD COLUMN .* NOT NULL(?! DEFAULT)' migrations/

# DROP COLUMN/TABLE
grep -rE 'DROP (COLUMN|TABLE)' migrations/

# ALTER TYPE
grep -rE 'ALTER COLUMN .* TYPE' migrations/

# Non-concurrent INDEX
grep -rE 'CREATE INDEX(?! CONCURRENTLY)' migrations/
```

**Best Practices**:
- Run Phase 4C right after Phase 4, before Phase 5 (Test Generation)
- Skip this Phase if no migration files exist
- Stop immediately on CRITICAL risk (user safety first)
- For HIGH risk, output warning only and proceed (user judgment)

---

### Phase 5: Automated Test Generation (Conditional)

**Objective**: Detect files missing tests and generate them

**Execution Condition**:
- After Phase 3 (Task Execution) completion, before Phase 6 (Testing)
- **Conditional**: When test files don't exist for changed files

**Steps**:

**1. Check Changed Files**

```bash
# Get list of modified files via Git
git diff --name-only HEAD
# Or compare with recent commit
git diff --name-only HEAD~1..HEAD
```

**2. Check Test File Existence**

```bash
# Find changed files without corresponding test files
for file in $(git diff --name-only HEAD | grep -E 'src/.*\.(ts|js)$'); do
  testfile=$(echo $file | sed 's/\.ts$/.test.ts/' | sed 's/\.js$/.test.js/')
  if [ ! -f "$testfile" ]; then
    echo "⚠️ Missing test: $file"
  fi
done
```

**3. Design Test Cases with Sequential Thinking**

When files missing tests are found, systematically analyze with Sequential Thinking:

```typescript
// Step 1: Analyze function signature
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "Analyzing processPayment function: input is amount (number), output is Promise<PaymentResult>",
  thoughtNumber: 1,
  totalThoughts: 6,
  nextThoughtNeeded: true
})

// Step 2: Analyze input constraints
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "Input constraints: amount must be >= 0 and <= 1,000,000",
  thoughtNumber: 2,
  totalThoughts: 6,
  nextThoughtNeeded: true
})

// Step 3: Analyze dependencies
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "Dependencies: calls PaymentAPI.process() → needs mocking",
  thoughtNumber: 3,
  totalThoughts: 6,
  nextThoughtNeeded: true
})

// Step 4: Identify Edge Cases
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "Edge cases: 1) amount = 0 (boundary), 2) amount = 1000000 (max), 3) amount = -1 (negative), 4) amount = 1000001 (overflow)",
  thoughtNumber: 4,
  totalThoughts: 6,
  nextThoughtNeeded: true
})

// Step 5: Identify Error Cases
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "Error cases: 1) API call failure, 2) network timeout, 3) invalid response format",
  thoughtNumber: 5,
  totalThoughts: 6,
  nextThoughtNeeded: true
})

// Step 6: Complete test case design
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "Test case design complete: Happy path (2), Edge cases (4), Error cases (2) - Total 8 cases",
  thoughtNumber: 6,
  totalThoughts: 6,
  nextThoughtNeeded: false
})
```

**4. Test Case Classification**

| Category | Test Case | Description |
|----------|--------------|------|
| **Happy path** | Normal amount | Returns expected result with valid input |
| **Happy path** | Minimum amount | Success with minimum valid value |
| **Edge cases** | Boundary (0) | Test minimum boundary value |
| **Edge cases** | Maximum (1000000) | Test maximum allowed value |
| **Edge cases** | Negative (-1) | Test negative input rejection |
| **Edge cases** | Overflow (1000001) | Test max value exceeded rejection |
| **Error handling** | API failure | Handle external API failure |
| **Error handling** | Timeout | Handle network timeout |

**5. Write Test Code with AAA Pattern**

**AAA Pattern (Arrange-Act-Assert)**:
- **Arrange**: Prepare objects and data needed for test
- **Act**: Execute the function/method under test
- **Assert**: Verify result matches expected value

```typescript
// Test code example (Jest)
describe('processPayment', () => {
  // ========== Happy Path ==========
  describe('정상 처리', () => {
    it('정상 금액(100)으로 결제 성공', async () => {
      // Arrange: Prepare test data
      const amount = 100;
      const expectedResult = { success: true, transactionId: 'TX123' };
      (PaymentAPI.process as jest.Mock).mockResolvedValue(expectedResult);

      // Act: Execute function
      const result = await processPayment(amount);

      // Assert: Verify result
      expect(result).toEqual(expectedResult);
      expect(PaymentAPI.process).toHaveBeenCalledWith(expect.objectContaining({
        amount: 100
      }));
    });
  });

  // ========== Edge Cases ==========
  describe('경계값 테스트', () => {
    it('음수 금액(-1)으로 에러 발생', async () => {
      // Arrange
      const amount = -1;

      // Act & Assert
      await expect(processPayment(amount))
        .rejects
        .toThrow('Invalid amount');
    });
  });

  // ========== Error Cases ==========
  describe('에러 처리', () => {
    it('API 호출 실패 시 에러 전파', async () => {
      // Arrange
      const amount = 100;
      const apiError = new Error('API Error');
      (PaymentAPI.process as jest.Mock).mockRejectedValue(apiError);

      // Act & Assert
      await expect(processPayment(amount))
        .rejects
        .toThrow('API Error');
    });
  });
});
```

**6. Given/When/Then Format (Alternative)**

```typescript
// BDD style test
describe('processPayment', () => {
  it('정상 금액으로 결제 성공', async () => {
    // Given: Valid amount is provided
    const amount = 100;
    const expectedResult = { success: true };
    mockPaymentAPI(expectedResult);

    // When: Payment processing is executed
    const result = await processPayment(amount);

    // Then: Success result is returned
    expect(result).toEqual(expectedResult);
  });
});
```

**7. Verify Generated Tests**

```bash
# Run generated test file
npm test -- payment.test.ts

# Check coverage
npm test -- payment.test.ts --coverage
```

**8. Update Report**

Add generated test results to execution report:

```markdown
## 📝 Auto-Generated Tests

### src/api/payment.test.ts (NEW)
- **Test Cases**: 8 generated
- **Coverage**: Line 92%, Branch 88%
- **Result**: ✅ 8/8 passed
- **Generation Time**: 45s

### src/utils/validator.test.ts (NEW)
- **Test Cases**: 12 generated
- **Coverage**: Line 95%, Branch 90%
- **Result**: ✅ 12/12 passed
- **Generation Time**: 30s

**Total Tests Added**: 20
**Average Coverage**: 93.5%
```

**Best Practices**:
- Run Phase 5 right after Phase 3 (Implementation), before Phase 6 (Testing)
- Skip this Phase if no missing tests
- If test generation fails, output warning only and proceed (non-blocking)
- Always run generated tests to verify

---

### Phase 6: AC Achievement Report (Required)

**Objective**: Automatically verify and report AC achievement after implementation

**Execution Condition**:
- After Phase 5 (Test Generation) completion
- When linked to a JIRA issue

**Steps**:

**1. Call requirement-validator Agent (Mode 3)**

```typescript
// Notify user
"🤖 Verifying AC achievement with requirement-validator agent..."

// Collect changed files via Git diff
Bash({ command: "git diff --name-only HEAD" })

// Call Agent
// Mode 3: Post-validation
// Input: JIRA issue key, git diff result
// Output: AC implementation status report
```

**2. Analyze Results and Update TodoList**

```typescript
// Add unimplemented AC to TodoList
IF (unimplemented AC exists):
    TodoWrite({
      todos: [
        ...existing_todos,
        {
          content: "Handle unimplemented AC: Implement AC#2 'Account lock after 5 failures'",
          status: "pending",
          activeForm: "Handling unimplemented AC"
        }
      ]
    })
```

**3. Output AC Achievement Report**

```markdown
## 📊 AC Implementation Status

| AC | Location | Status | Test | Coverage |
|----|----------|--------|------|----------|
| AC#1 | [UserService.ts:42](src/auth/UserService.ts#L42) | ✅ Done | ✅ Yes | 85% |
| AC#2 | ❌ Not implemented | Not implemented | ❌ No | - |
| AC#3 | [TokenService.ts:15](src/auth/TokenService.ts#L15) | ✅ Done | ✅ Yes | 90% |

**Total AC Achievement**: 66% (2/3)

### Next Actions

🔴 **CRITICAL**: AC#2 "Account lock after 5 failures" is not implemented!

**Recommendations**:
1. Implement LoginAttemptService
2. Add failure counter Redis storage logic
3. Implement account lock API after 5 failures
4. Write tests (Happy path + Edge cases)
5. Re-run Phase 6 to re-verify

**Added to TodoList** ✅
```

**4. Graceful Degradation When No JIRA Issue**

```typescript
IF (no JIRA issue):
    "ℹ️ Skipping AC verification - no JIRA issue linked."
    → Skip Phase 6, proceed to Phase 7
```

---

### Phase 7: Testing and Verification

**Objective**: Comprehensively test all implemented functionality.

**Testing Phases**:

1. **Unit Tests**
   ```bash
   # Run unit tests for modified modules
   npm test src/**/*.test.ts --coverage
   pytest tests/unit/ -v --cov
   ```

2. **Integration Tests**
   ```bash
   # Test component interactions
   npm run test:integration
   pytest tests/integration/ -v
   ```

3. **Manual Tests**
   - Follow manual test cases from plan
   - Verify UI/UX behavior
   - Check error messages and edge cases

4. **Performance Tests** (if specified)
   ```bash
   npm run test:performance
   ```

5. **Security Tests** (if specified)
   ```bash
   npm run test:security
   ```

**Verification Checklist**:
```
- ✓ All tasks marked as completed in TodoList
- ✓ All success criteria met for all tasks
- ✓ Unit tests passing
- ✓ Integration tests passing
- ✓ Manual tests verified
- ✓ No regression in existing functionality
- ✓ Code follows project standards
- ✓ No console errors or warnings
- ✓ Performance acceptable
- ✓ Security checks passed (if applicable)
```

---

## ✅ Execution Complete - Next: Run `record` Skill

**After execute skill completion, you MUST run `record` skill**:

```
Run record skill
```

Tasks handled by `record` skill:
- ✅ Update README
- ✅ Write CHANGELOG
- ✅ Add architectural decisions to CLAUDE.md
- ✅ Save learnings to Serena memory
- ✅ Clean up workflow artifacts (*_PLAN.md, *_REPORT.md)
- ✅ Git commit/push
- ✅ Update JIRA issue

**⚠️ IMPORTANT**: Do not perform documentation in execute. Documentation is handled centrally in record.

---

## Error Handling

### If a Task Fails

1. **Document the Failure**
   ```
   "❌ Task Failed: [Task Name]

   Error Details:
   - Error message: [error]
   - Context: [what was being attempted]
   - Stack trace: [if applicable]
   ```

2. **Check Rollback Procedure**
   - Refer to plan's rollback procedure (if defined)
   - Undo changes if necessary
   - Restore to known good state

3. **Mark Task as Blocked**
   ```typescript
   TodoWrite({todos: [...previousTodos, {content: taskName, status: "pending"}]})
   ```
   - Do NOT mark as "completed"
   - Document blocker reason

4. **Create Sub-tasks to Resolve**
   ```typescript
   TodoWrite({todos: [
     ...previousTodos,
     {content: "[BLOCKER] Fix [specific issue]", status: "in_progress", activeForm: "Fixing blocker"},
     {content: "[RETRY] [Original Task]", status: "pending", activeForm: "Retrying original task"}
   ]})
   ```

5. **Ask for User Guidance**
   ```
   "⚠️ Blocker Encountered

   I've encountered an issue with [Task Name]:
   [Description of problem]

   Options:
   1. [Option 1 with pros/cons]
   2. [Option 2 with pros/cons]
   3. [Option 3 with pros/cons]

   Which approach would you like me to take?"
   ```

### If Plan Needs Adjustment

1. **Pause Execution**
   ```
   "⏸️ Execution Paused

   I need to adjust the plan because:
   [Reason for adjustment]"
   ```

2. **Explain the Issue**
   ```
   "Issue: [Clear description]

   Why adjustment needed:
   - [Reason 1]
   - [Reason 2]

   Current plan assumes: [assumption]
   Reality: [actual situation]"
   ```

3. **Propose Modifications**
   ```
   "Proposed plan adjustments:

   Original approach:
   [Original plan excerpt]

   Modified approach:
   [Proposed change]

   Impact:
   - Timeline: [change]
   - Dependencies: [affected tasks]
   - Risk: [new risks or mitigations]"
   ```

4. **Wait for Approval**
   - Do NOT proceed without user approval
   - Present options clearly
   - Explain trade-offs

---

## Execution Principles

1. **Follow the Plan**: Stick to defined approach unless blocked
2. **Track Everything**: Use TodoWrite after EACH task (not in batches)
3. **One Task at a Time**: Exactly ONE task "in_progress" at any moment
4. **Verify Thoroughly**: Check ALL success criteria before marking complete
5. **Communicate Issues**: Report problems immediately, don't guess solutions
6. **Test Continuously**: Verify each task doesn't break existing functionality
7. **Run `record` After Completion**: Documentation and cleanup handled by record skill

---

## Progress Tracking Format

**Continuous Updates** (after each task):
```
📊 Progress: [X/Y tasks completed]

✅ Completed:
- [Task 1]: [Brief result]
- [Task 2]: [Brief result]

🔄 Current: [Task being worked on]

⏳ Next: [Next task in queue]

🚫 Blocked: [Any blocked tasks]
- [Blocked Task]: Waiting for [dependency/resolution]
```

---

## Final Execution Summary Template

```markdown
# Execution Summary: [Plan Name]

**Date**: [Completion date]
**Duration**: [Total time]

---

## ✅ Tasks Completed: [X/Y]

### Priority 0 (Critical)
- [Task 1] ✅ [Brief outcome]
- [Task 2] ✅ [Brief outcome]

### Priority 1 (High)
- [Task 3] ✅ [Brief outcome]

### Priority 2 (Medium)
- [Task 4] ✅ [Brief outcome]

### Priority 3 (Low)
- [Task 5] ✅ [Brief outcome]

---

## ✅ Success Criteria Met: [X/Y]

- [Criterion 1] ✅ Verified
- [Criterion 2] ✅ Verified
- [Criterion 3] ✅ Verified

---

## 🧪 Testing Results

### Unit Tests
- Status: ✅ All passing
- Coverage: [X%]
- Files tested: [count]

### Integration Tests
- Status: ✅ All passing
- Test cases: [count]

### Manual Tests
- Status: ✅ Verified
- Test scenarios: [count]

### Performance Tests
- Status: ✅ Passed (if applicable)
- Metrics: [key metrics]

### Security Tests
- Status: ✅ Passed (if applicable)
- Checks: [security checks performed]

---

## 🔄 Next Step: Run `record` Skill

**⚠️ IMPORTANT**: Run `record` skill after execute completion!

Handled by `record` skill:
- ✅ Update README, CHANGELOG, CLAUDE.md
- ✅ Save learnings to Serena memory
- ✅ Clean up workflow artifacts
- ✅ Git commit/push
- ✅ Update JIRA issue

---

## ⚠️ Issues Encountered

### Issue 1: [Issue description]
- **Impact**: [How it affected execution]
- **Resolution**: [How it was resolved]
- **Prevention**: [How to avoid in future]

### Issue 2: [Issue description]
- **Impact**: [How it affected execution]
- **Resolution**: [How it was resolved]
- **Prevention**: [How to avoid in future]

---

## 🎯 Project Management Updates

### JIRA
- ✅ Issue [ISSUE-ID] updated to "Done"
- ✅ Implementation notes added to issue
- ✅ Time logged (if applicable)

### Sentry
- ✅ Related errors resolved (if applicable)
- ✅ Issues closed: [issue IDs]

### GitHub
- ✅ Pull request created (if applicable): [PR link]
- ✅ Commits linked to issues

---

## 📊 Metrics

- **Total tasks**: [count]
- **Tasks completed**: [count]
- **Success rate**: [X%]
- **Test coverage**: [X%]
- **Time estimate vs actual**: [comparison]
- **Blocked tasks resolved**: [count]

---

## 🔄 Next Steps

### Immediate
- [Next step 1]
- [Next step 2]

### Future Considerations
- [Future consideration 1]
- [Future consideration 2]

---

## 💡 Lessons Learned

### What Went Well
- [Success 1]
- [Success 2]

### What Could Be Improved
- [Improvement area 1]
- [Improvement area 2]

### Recommendations for Future Plans
- [Recommendation 1]
- [Recommendation 2]

---

**Execution Complete** ✅

All plan objectives achieved. Project documentation updated. Temporary files cleaned up.
```

---

## Tips for Successful Execution

### Before Starting
- ✓ Ensure plan is approved (not draft)
- ✓ Verify all dependencies available
- ✓ Check required tools and access
- ✓ Review risk mitigations in plan
- ✓ Understand critical path

### During Execution
- ✓ Update TodoList after EACH task (not batched)
- ✓ Keep exactly ONE task as "in_progress"
- ✓ Verify success criteria before marking complete
- ✓ Run tests continuously, not just at the end
- ✓ Document issues as they occur
- ✓ Ask for guidance when blocked, don't guess

### After Execution
- ✓ Verify ALL tests pass
- ✓ Present comprehensive execution summary
- ✓ **Run `record` skill** for documentation and cleanup

---

## Resources

This skill does not require additional resource directories (scripts/, references/, or assets/). All execution logic is contained within this SKILL.md file, and the skill relies on Claude's ability to:

1. Use TodoWrite for task tracking
2. Use Serena MCP tools for code operations
3. Use Atlassian MCP tools for project management
4. Use Sentry MCP tools for error tracking
5. Use Context7 MCP tools for documentation
6. Follow the 7-phase systematic execution process
7. Maintain comprehensive progress tracking

**Note**: Documentation and file cleanup are handled by `record` skill.
