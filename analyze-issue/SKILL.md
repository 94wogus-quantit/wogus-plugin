---
name: analyze-issue
description: Systematically analyze the root cause of bugs and issues using multi-perspective investigation. Use this skill when analyzing JIRA issues, Sentry errors, or investigating bug reports to identify root causes and provide actionable remediation recommendations. Generates detailed analysis reports with code locations, reproduction steps, and fix recommendations.
---

# Analyze Issue Root Cause

## ⚠️ CRITICAL LANGUAGE POLICY

**DEFAULT LANGUAGE: KOREAN (한국어)**

ALL outputs, documentation, reports, and communications MUST be in **KOREAN** unless explicitly requested otherwise by the user.

- ✅ **Report files**: Write in Korean
- ✅ **Analysis**: Perform in Korean
- ✅ **Comments**: Write in Korean
- ✅ **Explanations**: Provide in Korean
- ✅ **User communication**: Respond in Korean

**Exception**: If the user writes in another language, match that language for responses.

**This is a MANDATORY requirement. Do NOT default to English.**

---

## Overview

Systematically analyze bugs and issues to identify root causes using a structured, multi-perspective approach. This skill combines JIRA/Atlassian integration, Sentry error tracking, codebase investigation with Serena, and sequential thinking to produce comprehensive analysis reports.

**Output**: `[ISSUE_ID]_REPORT.md` file containing root cause analysis, affected code locations, reproduction steps, and remediation recommendations.

## When to Use This Skill

Use this skill when:
- Analyzing JIRA issues or bug reports
- Investigating Sentry errors or production incidents
- User requests "analyze this issue", "what's causing this bug", "investigate [ISSUE-ID]"
- Need systematic root cause analysis before planning a fix
- Debugging complex problems requiring multi-source investigation

## Analysis Workflow

### Phase 1: Context Gathering

Gather comprehensive context from all available sources:

**1. JIRA Issue Analysis** (if JIRA link/ID provided)
- Use `mcp__atlassian__getJiraIssue` to fetch issue details
- Extract:
  - Issue summary and description
  - Reproduction steps
  - Expected vs actual behavior
  - Environment details
  - Attachments and linked resources
  - Comments and discussion threads
- Check for linked issues or sub-tasks

**2. Sentry Error Investigation** (if Sentry URL or error mentioned)

⚠️ **IMPORTANT**: ALWAYS search Sentry for related errors, even if not explicitly mentioned in JIRA.

**A. Search for Related Errors by Natural Language**

```typescript
// Search for errors related to the issue description
// Use natural language queries based on the issue
mcp__sentry__search_events({
  organizationSlug: 'your-org',
  naturalLanguageQuery: 'database connection timeout errors in the last 7 days'
  // or: 'authentication failures for user login'
  // or: 'null pointer exceptions in payment service'
})
```

**B. Search for Specific Error Patterns**

```typescript
// If you have error message or stack trace
mcp__sentry__search_events({
  organizationSlug: 'your-org',
  naturalLanguageQuery: 'TypeError Cannot read property of undefined in checkout.js'
})

// Search by time range
mcp__sentry__search_events({
  organizationSlug: 'your-org',
  naturalLanguageQuery: 'all errors in payment-service from last 24 hours'
})
```

**C. Get Detailed Error Information**

```typescript
// If Sentry URL provided (e.g., https://sentry.io/issues/PROJECT-123/)
mcp__sentry__get_issue_details({
  issueUrl: 'https://sentry.io/issues/PROJECT-123/'
})

// Or use issue ID
mcp__sentry__get_issue_details({
  organizationSlug: 'your-org',
  issueId: 'PROJECT-123'
})
```

**D. Analyze Error Counts and Statistics**

```typescript
// Count frequency of specific errors
mcp__sentry__search_events({
  organizationSlug: 'your-org',
  naturalLanguageQuery: 'count of database timeout errors today'
})

// Check error trends
mcp__sentry__search_events({
  organizationSlug: 'your-org',
  naturalLanguageQuery: 'total errors in authentication module this week'
})
```

**E. Extract Key Information**

From Sentry results, extract:
- **Stack traces**: 정확한 에러 발생 위치 (파일명, 라인 번호)
- **Error messages**: 에러 메시지 및 타입
- **Breadcrumbs**: 에러 발생 전 사용자 행동 추적
- **Context**: 요청 정보, 환경 변수, 사용자 데이터
- **Frequency**: 에러 발생 빈도 및 영향받는 사용자 수
- **Trends**: 시간대별 에러 증감 패턴
- **Related events**: 같은 사용자/세션의 다른 에러

**F. Common Search Patterns**

```typescript
// By component/service
'errors in payment-service'
'exceptions in user-authentication'

// By error type
'TypeError exceptions'
'database connection errors'
'404 not found errors'

// By time and severity
'critical errors in the last hour'
'all exceptions since deployment yesterday'

// By user impact
'errors affecting more than 100 users'
'high frequency errors today'
```

**3. Additional Context**
- Read any file paths or code references provided
- Analyze error logs or screenshots
- Check Confluence documentation if referenced

### Phase 2: Hypothesis Generation

Use `mcp__sequential-thinking__sequentialthinking` to systematically explore multiple perspectives:

**Generate Initial Hypotheses**

Consider various possible root causes:
- Code logic errors (off-by-one, null checks, type mismatches)
- Race conditions or concurrency issues
- Resource leaks or memory problems
- Configuration issues
- Dependency version conflicts
- Environmental differences
- Data quality or edge cases
- Integration issues with external services

**Multi-Perspective Analysis**

For each hypothesis, evaluate:
- **Likelihood**: How probable given the symptoms?
- **Evidence**: What observations support or contradict this?
- **Impact**: How severe would this issue be?
- **Complexity**: How difficult to fix?

**Systematic Elimination**

Use logical reasoning to eliminate unlikely causes:
- Cross-reference with stack traces
- Check timing of deployments vs issue occurrence
- Verify affected components
- Consider scope (all users vs specific conditions)

### Phase 3: Codebase Investigation

Use Serena tools efficiently for targeted code exploration:

**1. Targeted Code Exploration**
- `mcp__serena__get_symbols_overview` - Understand file structure
- `mcp__serena__find_symbol` - Locate specific functions/classes
- `mcp__serena__find_referencing_symbols` - Trace execution flow
- `mcp__serena__search_for_pattern` - Find error messages or patterns

**2. Execution Flow Tracing**
- Map the path from user action to error
- Identify all components in the flow
- Look for conditional branches
- Check error handling and validation logic

**3. Recent Changes Analysis** (if applicable)
- Use GitHub MCP to find recent changes
- Correlate timing with issue occurrence
- Review related code modifications

### Phase 3D: Code Complexity Assessment (조건부 필수)

**목적**: 복잡한 코드를 탐지하여 리팩토링 제안 생성

**실행 조건**:
- Phase 3에서 affected files 확인 완료 후
- **조건부 필수**: Cyclomatic complexity > 10 OR 함수 길이 > 50줄인 코드 발견 시

**Steps**:

**1. Serena로 복잡도 분석**

```typescript
// 영향받는 파일의 심볼 정보 가져오기
mcp__serena__find_symbol({
  name_path_pattern: "UserService/login",
  relative_path: "src/services/UserService.ts",
  include_body: true
})
```

**2. Sequential Thinking으로 복잡도 측정 및 리팩토링 전략 수립**

```typescript
// Step 1: 복잡도 측정
mcp__sequential-thinking__sequentialthinking({
  thought: "UserService/login 함수의 Cyclomatic complexity 측정: if문 8개, for문 2개, switch문 1개 → complexity = 11 (임계값 10 초과)",
  thoughtNumber: 1,
  totalThoughts: 5,
  nextThoughtNeeded: true
})

// Step 2: 함수 길이 분석
mcp__sequential-thinking__sequentialthinking({
  thought: "함수 길이 측정: 총 85줄 → 임계값(50줄) 초과. 가독성 저하, 여러 책임 담당 가능성",
  thoughtNumber: 2,
  totalThoughts: 5,
  nextThoughtNeeded: true
})

// Step 3: 책임 분석 (SRP)
mcp__sequential-thinking__sequentialthinking({
  thought: "책임 분석: 1) 사용자 인증, 2) 세션 관리, 3) 로깅, 4) 에러 처리 → 4개 책임 (SRP 위반)",
  thoughtNumber: 3,
  totalThoughts: 5,
  nextThoughtNeeded: true
})

// Step 4: Code Smells 탐지
mcp__sequential-thinking__sequentialthinking({
  thought: "Code Smells 탐지: 중복 코드 3회 발견 (validateUser 로직), Magic Number 2개 (3.14, 0.05)",
  thoughtNumber: 4,
  totalThoughts: 5,
  nextThoughtNeeded: true
})

// Step 5: 리팩토링 전략 결정
mcp__sequential-thinking__sequentialthinking({
  thought: "리팩토링 전략: 1) Extract Method로 validateUser() 분리, 2) Extract Class로 SessionManager 분리, 3) Replace Magic Number with Named Constant",
  thoughtNumber: 5,
  totalThoughts: 5,
  nextThoughtNeeded: false
})
```

**3. 리팩토링 필요 판단 기준**

| 지표 | 임계값 | 설명 |
|------|--------|------|
| **Cyclomatic complexity** | > 10 | 조건문/반복문이 너무 많아 테스트 복잡도 증가 |
| **함수 길이** | > 50줄 | 가독성 저하, 여러 책임 담당 가능성 |
| **SRP 위반** | > 2책임 | 하나의 함수/클래스가 여러 책임 수행 |
| **중복 코드** | > 2회 | 동일 로직 반복 → Extract Method 필요 |
| **Magic Numbers** | > 0개 | 하드코딩된 상수 → Named Constant 필요 |

**4. 리팩토링 기법 선택 가이드**

| 문제 | 기법 | 적용 조건 |
|------|------|----------|
| 긴 함수 | **Extract Method** | 함수 > 50줄 또는 논리적 블록 분리 가능 |
| 여러 책임 | **Extract Class** | 클래스가 2개 이상 책임 담당 |
| 중복 코드 | **Extract Method** | 동일 로직 2회 이상 반복 |
| Magic Numbers | **Replace with Constant** | 하드코딩된 숫자/문자열 |
| 복잡한 조건문 | **Decompose Conditional** | 복잡한 if/else 체인 |

**5. 보고서에 리팩토링 제안 추가**

복잡도가 높은 파일이 발견되면 보고서의 "Recommendations" 섹션에 다음 형식으로 추가:

```markdown
## 🔧 리팩토링 제안

### 복잡도 분석 결과

#### `src/services/UserService.ts:15-110` - `login()` 함수

**메트릭**:
| 지표 | 현재 | 임계값 | 상태 |
|------|------|--------|------|
| Cyclomatic Complexity | 12 | 10 | ⚠️ 초과 |
| 함수 길이 | 95줄 | 50줄 | ⚠️ 초과 |
| 책임 수 (SRP) | 4개 | 2개 | ⚠️ 위반 |

**탐지된 Code Smells**:
- 중복 코드: `validateUser` 로직 3회 반복
- Magic Numbers: `3.14`, `0.05` 하드코딩

**권장 리팩토링**:

1. **Extract Method: `validateUser()`**
   - 중복된 검증 로직을 별도 함수로 추출
   - 예상 효과: 중복 제거, 테스트 가능성 향상

2. **Extract Class: `SessionManager`**
   - 세션 관리 로직을 별도 클래스로 분리
   - 예상 효과: SRP 준수, 응집도 향상

3. **Replace Magic Numbers**
   - `3.14` → `SESSION_TIMEOUT_HOURS`
   - `0.05` → `RETRY_INTERVAL_SECONDS`

**예상 개선 효과**:
- Complexity: 12 → 5 (-58%)
- 함수 길이: 95줄 → 40줄 (-58%)
- 테스트 가능성: 향상 (작은 단위로 분리)
```

### Phase 3E: Requirement Reverse Tracing (선택적)

**목적**: 버그와 연관된 JIRA AC 역추적

**실행 조건**:
- JIRA 이슈와 연결된 경우
- Phase 3 (Codebase Investigation)에서 버그 발생 위치 확인 완료

**Steps**:

**1. requirement-validator Agent 호출 (Mode 1)**

```typescript
// 사용자에게 알림
"🤖 requirement-validator agent로 AC 역추적 중..."

// Agent 호출 (Claude Code에서 자동)
// Mode 1: Reverse Tracing
// Input: 버그 발생 파일 경로, 함수명
// Output: 연관 AC 목록
```

**2. 결과를 보고서에 추가**

보고서 `[ISSUE_ID]_REPORT.md`의 Phase 4 (Root Cause Analysis) 섹션에 추가:

```markdown
## 🎯 요구사항 추적

### 연관 AC
- **AC#2**: "비밀번호 5회 실패 시 계정 잠금"
  - **관련 코드**: [LoginAttemptService.ts:15-45](src/auth/LoginAttemptService.ts#L15-L45)
  - **버그 원인**: 실패 카운터 로직 오류 (Redis 키 만료 시간 미설정)
  - **AC 충족 여부**: ❌ 미충족 - 계정 잠금 안 됨

### 영향
- 이 버그로 인해 AC#2가 충족되지 않습니다
- 보안 요구사항 위반 (무차별 대입 공격 방어 실패)

### 권장 조치
1. LoginAttemptService의 카운터 TTL 설정 (5분)
2. AC#2 테스트 케이스 추가 (현재 누락)
3. MR 리뷰 시 requirement-validator로 재검증
```

### Phase 4: Root Cause Determination

Based on systematic analysis, identify:

**1. Primary Root Cause**
- Exact location: `[file_path:line_number](file_path#Lline_number)`
- Technical mechanism explaining why it occurs
- Trigger conditions

**2. Contributing Factors**
- Secondary issues exacerbating the problem
- Missing safeguards or error handling
- Architectural weaknesses

**3. Impact Assessment**
- Affected users and use cases
- Frequency and reproducibility
- Data integrity implications
- Performance or security considerations

### Phase 5: Recommendations

Provide actionable recommendations:

**1. Immediate Actions**
- Quick fixes or workarounds
- Rollback considerations
- Monitoring or alerting to add

**2. Proper Fix**
- Detailed solution with code-level specifics
- Files and functions requiring modification
- Edge cases to handle
- Testing strategy

**3. Prevention**
- Unit tests to add (specific assertions)
- Integration tests for the flow
- Code review checklist items
- Documentation updates needed

**4. Related Areas to Review**
- Similar code patterns with same issue
- Related features sharing problematic code
- Upstream/downstream dependencies

## Report Generation

Create a comprehensive markdown report using the template in `references/report_template.md`:

### Report Structure

```markdown
# Issue Analysis: [Issue Title/ID]

## Summary
[One-paragraph executive summary]

## Context
- JIRA: [link and key details]
- Sentry: [error details if applicable]
- Additional files: [user-provided context]

## Investigation Process
[Summary of hypotheses considered and eliminated]

## Root Cause
**Location**: [file.ts:123](file.ts#L123)
**Explanation**: [Why this happens]
**Trigger**: [What causes it]

## Recommendations

### Immediate Fix
[Specific code changes or workarounds]

### Long-term Solution
[Architectural or design improvements]

### Testing
- [ ] Unit test: [specific test case]
- [ ] Integration test: [specific scenario]

### Related Code to Review
- [file1.ts:45](file1.ts#L45) - Similar pattern
- [file2.ts:89](file2.ts#L89) - Shared dependency
```

### File Naming Convention

Save the report as:
- `[ISSUE_ID]_REPORT.md` (e.g., `PROJ-1234_REPORT.md`)
- If no JIRA ID: `[DESCRIPTIVE_NAME]_REPORT.md` (e.g., `LOGIN_ERROR_REPORT.md`)

Save in current working directory or `docs/` folder if it exists.

## Best Practices

**Efficiency**
- Use symbolic tools to read only necessary code, not entire files
- Start with high-level overview before diving into details
- Use pattern search for quick discovery

**Thoroughness**
- Use sequential thinking to explore all angles
- Document reasoning process
- Include eliminated hypotheses (shows rigor)

**Specificity**
- Always provide exact file paths and line numbers
- Include code snippets as evidence
- Link to external resources (JIRA, Sentry)

**Actionability**
- Focus on concrete, implementable recommendations
- Provide code examples for fixes
- Suggest specific tests to add

**Documentation**
- Create detailed `*_REPORT.md` file at the end
- Use proper markdown formatting
- Include clickable code location links
- Format actionable items as checklists

## Integration with Workflow

This skill is typically the first step in a larger workflow:

```
/analyze-issue [JIRA]
  → Creates: [ISSUE_ID]_REPORT.md
  → Next: /plan [REPORT]
  → Next: /execute-plan [PLAN]
  → Next: /document
```

The generated report becomes input for the `/plan` command to create an implementation plan.

## Resources

### references/

This skill includes reference materials to support the analysis process:

- `report_template.md` - Detailed template for analysis reports
- `common_bug_patterns.md` - Catalog of frequently encountered bug patterns and their characteristics

These references can be loaded into context when needed for comprehensive analysis.
