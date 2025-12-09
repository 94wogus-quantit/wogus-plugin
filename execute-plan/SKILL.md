---
name: execute-plan
description: Systematically execute approved implementation plans by managing task flow, tracking progress with TodoList, running tests, and ensuring all success criteria are met. Focus purely on implementation and testing. Use this skill when you have an approved plan file (e.g., *_PLAN.md) and need to implement it step-by-step with comprehensive tracking and verification. Documentation and cleanup are handled by the 'document' skill.
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
- User requests "execute this plan", "implement the plan", "run /execute-plan"
- Need to systematically implement multiple related tasks
- Want automatic progress tracking with TodoList
- Need to ensure all success criteria and tests are verified

**Typical Workflow Position**:
```
analyze-issue → plan-builder → **execute-plan** → document
```

## Overview

This skill executes approved implementation plans through a 7-phase systematic process:

1. **Plan Loading & Validation**: Load plan file, parse tasks, verify prerequisites
2. **TodoList Setup**: Create comprehensive TodoList from all plan tasks
3. **Task Execution**: Execute tasks sequentially, respecting dependencies
4. **Handle Dependencies**: Manage task dependencies and execution order
5. **Automated Test Generation** (선택적): Detect missing tests and auto-generate using test-generator agent
6. **Testing & Verification**: Run tests and verify success criteria
7. **Documentation**: Update code documentation and save learnings

**Note**:
- Phase 4C (DB Migration Validation) and Phase 5 (Test Generation) are optional
- Project documentation and file cleanup are handled by the `document` skill, not this skill

---

## Workflow: 7-Phase Execution Process

### Phase 1: Plan Loading and Validation

**Objective**: Load and understand the plan, validate prerequisites are met.

**Steps**:

1. **Load the Plan File**
   ```typescript
   // Use Read tool to load the plan
   Read(planFilePath)
   ```

2. **Parse Plan Structure**
   - Use `mcp__sequential-thinking__sequentialthinking` to understand:
     - Task breakdown and priorities (P0 → P1 → P2 → P3)
     - Dependencies between tasks
     - Success criteria for each task
     - Testing strategies
     - Risk mitigations
     - Expected tools and MCP servers

3. **Validate Prerequisites**
   - Check codebase context:
     ```typescript
     mcp__serena__check_onboarding_performed()
     mcp__serena__get_current_config()
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
mcp__serena__get_symbols_overview({relative_path: "src/auth/service.ts"})

// Find specific symbols
mcp__serena__find_symbol({name_path: "AuthService/login", include_body: true})

// Find references
mcp__serena__find_referencing_symbols({name_path: "login", relative_path: "src/auth/service.ts"})

// Search patterns
mcp__serena__search_for_pattern({substring_pattern: "OAuth.*config"})
```

**Documentation**:
```typescript
// Get library docs
mcp__context7__resolve_library_id({libraryName: "passport"})
mcp__context7__get_library_docs({context7CompatibleLibraryID: "/jaredhanson/passport"})
```

**Project Management**:
```typescript
// Get JIRA details
mcp__atlassian__getJiraIssue({cloudId, issueIdOrKey: "PROJ-123"})

// Check Sentry errors
mcp__sentry__get_issue_details({organizationSlug, issueId})
```

#### 3C. Implement Task

Follow the implementation approach defined in the plan:

**Code Editing**:
```typescript
// Replace symbol body
mcp__serena__replace_symbol_body({
  name_path: "AuthService/login",
  relative_path: "src/auth/service.ts",
  body: newImplementation
})

// Insert new code
mcp__serena__insert_after_symbol({
  name_path: "AuthService",
  relative_path: "src/auth/service.ts",
  body: newMethodCode
})

// Rename symbols
mcp__serena__rename_symbol({
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
mcp__serena__think_about_task_adherence()

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

### Phase 4C: Database Migration Validation (선택적)

**목적**: 위험한 DB 마이그레이션 패턴 자동 탐지 및 차단

**실행 조건**: Plan에 DB migration 관련 작업이 있을 때

**Steps**:

**1. 마이그레이션 파일 탐지**

```bash
# migrations/ 또는 db/ 디렉토리에서 SQL/migration 파일 찾기
find . -path "*/migrations/*.sql" -o -path "*/db/migrate/*.rb" -o -path "*/migrations/*.ts"
```

**2. 위험 패턴 분석**

다음 정규표현식으로 위험 패턴 탐지:

```typescript
const dangerousPatterns = {
  // CRITICAL: 데이터 손실 위험
  notNull: /ADD COLUMN .* NOT NULL(?! DEFAULT)/i,
  dropColumn: /DROP COLUMN/i,
  dropTable: /DROP TABLE/i,

  // HIGH: 성능 문제
  alterType: /ALTER COLUMN .* TYPE/i,
  addIndex: /CREATE INDEX(?! CONCURRENTLY)/i
};

// 각 마이그레이션 파일 스캔
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

**3. 경고 및 승인 요청**

위험 패턴 발견 시 다음 형식으로 경고 메시지 출력:

```markdown
## ⚠️ Database Migration 위험 탐지

### CRITICAL 위험
- **File**: migrations/20231209_add_user_email.sql
  - **Pattern**: `ADD COLUMN email VARCHAR(255) NOT NULL`
  - **Problem**: 기존 row에 NULL 값 불가 → Migration 실패
  - **Solution**: DEFAULT 값 추가 또는 2단계 migration (1. ADD COLUMN with DEFAULT, 2. ALTER COLUMN DROP DEFAULT)

### HIGH 위험
- **File**: migrations/20231209_alter_user_type.sql
  - **Pattern**: `ALTER COLUMN user_type TYPE VARCHAR(50)`
  - **Problem**: Table lock 발생, 대용량 테이블에서 장시간 소요
  - **Solution**: 새 컬럼 추가 → 데이터 복사 → 기존 컬럼 삭제 (Zero-downtime migration)

**Action Required**:
- CRITICAL 위험이 있으면 실행 중단
- HIGH 위험은 사용자 승인 후 진행
```

**4. 실행 중단 로직**

```typescript
if (warnings.some(w => w.severity === 'CRITICAL')) {
  console.log('❌ CRITICAL migration 위험 발견 - 실행 중단');
  console.log('마이그레이션 파일 수정 후 다시 시도하세요.');
  // Phase 3 Task Execution으로 돌아가지 않고 종료
  process.exit(1);
}

if (warnings.some(w => w.severity === 'HIGH')) {
  console.log('⚠️ HIGH migration 위험 발견 - 사용자 승인 필요');
  // 사용자에게 승인 요청
}
```

**5. Grep으로 위험 패턴 탐지 (실제 구현 예시)**

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
- Phase 4C는 Phase 4 직후, Phase 5 (Test Generation) 이전에 실행
- 마이그레이션 파일이 없으면 이 Phase는 skip
- CRITICAL 위험 발견 시 즉시 중단 (사용자 안전 우선)
- HIGH 위험은 warning만 출력하고 진행 (사용자 판단)

---

### Phase 5: Automated Test Generation (선택적)

**목적**: 테스트 누락된 파일 탐지 및 자동 생성

**실행 조건**: Phase 3 (Task Execution) 완료 후, Phase 6 (Testing) 이전

**Steps**:

**1. 변경된 파일 확인**

```bash
# Git으로 수정된 파일 목록 가져오기
git diff --name-only HEAD
# 또는 최근 커밋과 비교
git diff --name-only HEAD~1..HEAD
```

**2. 테스트 파일 존재 확인**

각 변경된 파일에 대해 테스트 파일이 존재하는지 확인:

```typescript
// 변경된 파일 목록
const modifiedFiles = ["src/api/payment.ts", "src/utils/validator.ts"];

// 각 파일에 대해 테스트 파일 찾기
for (const file of modifiedFiles) {
  // 패턴: *.test.ts, *.spec.ts, *.test.js, *.spec.js
  const testPatterns = [
    file.replace(/\.(ts|js)$/, '.test.$1'),
    file.replace(/\.(ts|js)$/, '.spec.$1'),
    file.replace(/^src\//, 'tests/').replace(/\.(ts|js)$/, '.test.$1')
  ];

  let testFileExists = false;
  for (const pattern of testPatterns) {
    if (fileExists(pattern)) {
      testFileExists = true;
      break;
    }
  }

  if (!testFileExists) {
    console.log(`⚠️ 테스트 누락: ${file}`);
    missingTests.push(file);
  }
}
```

**3. Glob/Grep으로 실제 탐지**

```bash
# 변경된 파일 목록
git diff --name-only HEAD | grep -E '\.(ts|js)$' | grep -v '.test.' | grep -v '.spec.'

# 각 파일에 대해 테스트 파일 존재 확인
for file in $(git diff --name-only HEAD | grep -E 'src/.*\.(ts|js)$'); do
  testfile=$(echo $file | sed 's/\.ts$/.test.ts/' | sed 's/\.js$/.test.js/')
  if [ ! -f "$testfile" ]; then
    echo "⚠️ 테스트 누락: $file"
  fi
done
```

**4. test-generator Agent 자동 호출**

테스트가 누락된 파일이 발견되면 test-generator agent를 자동으로 호출:

```markdown
🤖 **test-generator agent 자동 실행 중...**

**파일**: src/api/payment.ts
**프레임워크**: Jest (package.json에서 자동 탐지)
**예상 테스트 케이스**: 8-12개
**테스트 유형**: Happy path, Edge cases, Error handling

[test-generator agent 실행 중...]
```

Agent 호출 예시:
```
"test-generator agent를 사용하여 src/api/payment.ts에 대한 테스트를 생성해줘"
```

**5. 생성된 테스트 검증**

```bash
# 생성된 테스트 파일 실행
npm test -- payment.test.ts

# 커버리지 확인
npm test -- payment.test.ts --coverage
```

**6. 보고서 업데이트**

생성된 테스트 결과를 실행 보고서에 추가:

```markdown
## 📝 자동 생성된 테스트

### src/api/payment.test.ts (NEW)
- **테스트 케이스**: 8개 생성
- **커버리지**: Line 92%, Branch 88%
- **실행 결과**: ✅ 8/8 통과
- **생성 시간**: 45초

### src/utils/validator.test.ts (NEW)
- **테스트 케이스**: 12개 생성
- **커버리지**: Line 95%, Branch 90%
- **실행 결과**: ✅ 12/12 통과
- **생성 시간**: 30초

**총 테스트 추가**: 20개
**평균 커버리지**: 93.5%
```

**Best Practices**:
- Phase 5는 Phase 3 (Implementation) 직후, Phase 6 (Testing) 이전에 실행
- 테스트 누락이 없으면 이 Phase는 skip
- test-generator agent가 실패하면 warning만 출력하고 진행 (blocking하지 않음)
- 생성된 테스트는 반드시 실행하여 검증

---

### Phase 6: Testing and Verification

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

### Phase 7: Documentation Updates

**Objective**: Update code documentation and capture learnings.

**Note**: Project-level documentation (README, CHANGELOG) is handled by the 'document' skill.

#### 7A. Code Documentation
- Add/update inline comments
- Update function/class documentation
- Add README sections if new modules created

#### 7B. Capture Learnings

```typescript
// Save key insights to Serena memory
mcp__serena__write_memory({
  memory_file_name: "auth_implementation_learnings.md",
  content: `
# Authentication Implementation Learnings

## Key Decisions
- Chose Passport.js for OAuth integration due to [reasons]
- Implemented JWT with 24h expiry based on security requirements

## Challenges Encountered
- [Challenge 1]: Resolved by [solution]
- [Challenge 2]: Resolved by [solution]

## Best Practices Applied
- [Practice 1]
- [Practice 2]

## Future Considerations
- [Consideration 1]
- [Consideration 2]
`
})
```

#### 7C. Update Project Management

```typescript
// Update JIRA issue
mcp__atlassian__editJiraIssue({
  cloudId,
  issueIdOrKey: "PROJ-123",
  fields: {
    status: {name: "Done"},
    comment: "Implementation completed. All tests passing."
  }
})

// Add implementation notes
mcp__atlassian__addCommentToJiraIssue({
  cloudId,
  issueIdOrKey: "PROJ-123",
  commentBody: `
## Implementation Summary
- [Summary of what was implemented]
- All success criteria met
- Tests: ✅ Passing
- Documentation: ✅ Updated
`
})
```

#### 7D. Final Review

```typescript
// Verify everything is done
mcp__serena__think_about_whether_you_are_done()
```

**Checklist**:
- All plan objectives achieved ✅
- All tasks completed ✅
- All tests passing ✅
- Documentation updated ✅
- Learnings captured ✅

**Next Step**: Run the `document` skill to update project documentation (README, CHANGELOG, CLAUDE.md) and clean up workflow artifact files.

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
6. **Document Learnings**: Use memory tools to capture insights for future
7. **Test Continuously**: Verify each task doesn't break existing functionality
8. **Update README**: Always update project documentation with changes
9. **Clean Up Files**: Always verify and delete plan/report files after execution

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

## 📚 Documentation Updates

### Code Documentation
- ✅ Inline comments added
- ✅ Function/class documentation updated
- ✅ Module documentation updated (if applicable)

### Serena Memory
- ✅ Key learnings saved to: [memory file name]
- ✅ Best practices documented
- ✅ Challenges and solutions captured

**Note**: Project-level documentation (README, CHANGELOG) and file cleanup are handled by the 'document' skill

---

## 🔄 Next: Run Document Skill

**IMPORTANT**: After execute-plan completes, run the `document` skill to:
- ✅ Update README with new features
- ✅ Add CHANGELOG entries
- ✅ Update CLAUDE.md with architectural decisions
- ✅ Clean up workflow artifacts (*_PLAN.md, *_REPORT.md)
- ✅ Update JIRA issues (if applicable)

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
- ✓ Update project README (CRITICAL - don't skip)
- ✓ Verify README has all plan information
- ✓ Clean up plan and report files after verification
- ✓ Capture learnings in Serena memory
- ✓ Update JIRA/project management tools
- ✓ Present comprehensive execution summary

---

## Resources

This skill does not require additional resource directories (scripts/, references/, or assets/). All execution logic is contained within this SKILL.md file, and the skill relies on Claude's ability to:

1. Use TodoWrite for task tracking
2. Use Serena MCP tools for code operations
3. Use Atlassian MCP tools for project management
4. Use Sentry MCP tools for error tracking
5. Use Context7 MCP tools for documentation
6. Follow the 8-phase systematic execution process
7. Maintain comprehensive progress tracking
8. Update project documentation automatically
9. Verify and clean up temporary files

The skill is self-contained and ready for use without external dependencies.
