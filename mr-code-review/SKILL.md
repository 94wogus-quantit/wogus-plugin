---
name: mr-code-review
description: GitLab MR의 코드 변경사항을 분석하여 맥락 기반 종합 리뷰를 수행하고 MR_CODE_REVIEW.md 리포트를 생성합니다. Sequential Thinking과 Serena Context7 MCP를 활용하여 아키텍처 일관성, 과거 이슈 패턴, JIRA 요구사항, 보안, 테스트를 검증합니다.
---

# MR Code Review

## ⚠️ CRITICAL LANGUAGE POLICY

**DEFAULT LANGUAGE: KOREAN (한국어)**

ALL outputs, reports, analysis, and communications MUST be in **KOREAN** unless explicitly requested otherwise by the user.

- ✅ **MR_CODE_REVIEW.md**: Write in Korean
- ✅ **Issue descriptions**: Write in Korean
- ✅ **Improvement suggestions**: Write in Korean
- ✅ **Analysis comments**: Write in Korean
- ✅ **User communication**: Respond in Korean

**Exception**: If the user writes in another language, match that language for responses.

**This is a MANDATORY requirement. Do NOT default to English.**

---

## Overview

GitLab MR(Merge Request)의 변경사항을 분석하여 단순 문법 체크를 넘어선 맥락 기반 종합 코드 리뷰를 수행하고 **MR_CODE_REVIEW.md** 리포트를 생성합니다.

**핵심 차별점**: 프로젝트 문서(README, CHANGELOG, CLAUDE.md), Serena memory, JIRA, Confluence를 활용한 심층 분석

**주요 기능**:
- 📋 **6가지 종합 검증**: 아키텍처, 컨벤션, 이슈 패턴, JIRA 요구사항, 보안, 테스트
- 🔐 **의존성 보안 분석** (선택적): npm audit 기반 CRITICAL/HIGH 취약점 자동 탐지
- 🤖 **MCP 기반 심화 분석**: Sequential Thinking + Serena Context7 + Atlassian 적극 활용
- 📝 **리포트 생성**: MR_CODE_REVIEW.md (3단계 위험도: 🔴 Critical, 🟡 High, 🟢 Medium)
- 💡 **개선 제안**: 각 이슈별 위치, 설명, 개선 방법 제공

---

## ⚠️ CRITICAL MCP USAGE POLICY

> **모든 분석 단계에서 Sequential Thinking과 Serena MCP를 필수적으로 적극 활용해야 합니다.**

### Sequential Thinking MCP (필수)

**사용 목적**: 다각도 체계적 사고 및 가설 검증

**✅ DO - 반드시 사용해야 할 시나리오**:
- 아키텍처 일관성 검증 시: 레이어 분리, 의존성 방향 체계적 검토
- 컨벤션 준수 확인 시: 네이밍, 스타일, import 순서 단계별 검증
- 보안 검토 시: OWASP Top 10 각 항목별 순차 분석
- JIRA 요구사항 검증 시: Acceptance Criteria 각각 개별 검증
- 과거 이슈 패턴 대조 시: known_issues 패턴별 대조

**사용 예시**:
```typescript
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "변경된 코드가 기존 아키텍처 레이어 분리 원칙을 준수하는가? Controller → Service → Repository 패턴을 따르는가?",
  thoughtNumber: 1,
  totalThoughts: 7,
  nextThoughtNeeded: true
})

mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "SQL Injection 취약점: 사용자 입력이 쿼리에 직접 사용되는가? Prepared Statement를 사용하는가?",
  thoughtNumber: 3,
  totalThoughts: 10,
  nextThoughtNeeded: true
})
```

**❌ DON'T - 사용하지 말아야 할 경우**:
- 간단한 파일 읽기나 단순 정보 조회
- MCP 호출 자체 (도구 사용은 별도)
- 단순 로깅이나 상태 업데이트

### Serena Context7 MCP (필수)

**사용 목적**: 코드베이스 심화 이해 및 메모리 관리

**✅ DO - 반드시 사용해야 할 시나리오**:
- 프로젝트 전체 맥락 이해 시: `get_project_context` 사용
- 아키텍처 결정사항 조회 시: `read_memory({memory_file_name: "architecture_decisions.md"})`
- 코드 패턴 검색 시: `search_for_pattern` 사용
- 심볼 의존성 확인 시: `find_referencing_symbols` 사용
- 과거 버그 패턴 조회 시: `read_memory({memory_file_name: "known_issues.md"})`

**사용 예시**:
```typescript
// Memory에서 과거 이슈 패턴 읽기
mcp__plugin_workflow-skills_serena__read_memory({
  memory_file_name: "known_issues.md"
})

// 심볼 의존성 검색
mcp__plugin_workflow-skills_serena__find_referencing_symbols({
  name_path: "UserService",
  relative_path: "src/services/user.service.ts"
})

// 코드 패턴 검색
mcp__plugin_workflow-skills_serena__search_for_pattern({
  substring_pattern: "async.*await",
  paths_include_glob: "**/*.ts"
})
```

**❌ DON'T - 대신 다른 도구를 사용해야 할 경우**:
- 일반 파일 읽기: `Read` tool 사용
- Git 작업: `Bash` tool의 git 명령어 사용
- 간단한 코드 검색: `Grep` tool 사용

### Atlassian MCP (선택적, JIRA 관련 시 필수)

**사용 목적**: JIRA 이슈 및 Confluence 문서 연동

**✅ DO**:
- MR과 연결된 JIRA 이슈 조회: `getJiraIssue`
- Acceptance Criteria 확인: JIRA 이슈 description 파싱
- Confluence 기술 문서 검색: `searchConfluence`

**사용 예시**:
```typescript
// JIRA 이슈 조회
mcp__plugin_workflow-skills_atlassian__jira_get_issue({
  issue_key: "PROJ-123"
})

// Confluence 검색
mcp__plugin_workflow-skills_atlassian__confluence_search({
  query: "title ~ \"API 명세\" AND space = \"TECH\""
})
```

---

## When to Use This Skill

**✅ 사용해야 할 때**:
- GitLab MR 코드 리뷰가 필요할 때
- 맥락 기반 종합 리뷰가 필요한 중요한 MR (아키텍처 변경, 신규 기능)
- 프로젝트 문서와 JIRA 요구사항을 종합 검증해야 할 때
- 보안, 품질, 테스트 커버리지를 체계적으로 검증하고 싶을 때

**❌ 사용하지 말아야 할 때**:
- 단순 typo 수정이나 문서 변경만 있는 MR
- 빠른 검토가 필요한 경우 (기본 code-reviewer agent 사용)
- JIRA 이슈와 무관한 간단한 hotfix

**사용 방법**:
```bash
# 로컬에서 직접 실행
claude-code exec "Use mr-code-review skill to review this MR. Branch: feature/user-auth"

# 또는 대화형으로
# "mr-code-review skill로 이 MR 리뷰해줘"
```

---

## Review Workflow

### Phase 1: Context Gathering (맥락 수집)

**목표**: MR과 관련된 모든 맥락 정보 수집 및 **브랜치 목표 명확화**

**주요 MCP**: Serena (Context7, Memory), Atlassian

**프로세스**:

1. **브랜치 목표 파악 (Branch Objective) - 최우선**

   > ⚠️ **CRITICAL**: 코드 분석 전에 반드시 "이 브랜치가 무엇을 달성해야 하는지"를 먼저 파악해야 합니다.

   **1-1. 브랜치명에서 JIRA 이슈 추출**
   ```bash
   # 브랜치명 패턴: feature/PROJ-123-description, bugfix/PROJ-456
   git branch --show-current | grep -oE '[A-Z]+-[0-9]+'
   ```

   **1-2. MR description에서 JIRA 이슈 추출**
   ```bash
   # GitLab MR description 확인
   glab mr view --json description | jq -r '.description' | grep -oE '[A-Z]+-[0-9]+'
   ```

   **1-3. JIRA 이슈 상세 조회 (Atlassian MCP)**
   ```typescript
   // JIRA 이슈 조회
   mcp__plugin_workflow-skills_atlassian__jira_get_issue({
     issue_key: "PROJ-123"
   })
   ```

   **1-4. 브랜치 목표 정리 (Sequential Thinking)**
   ```typescript
   mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
     thought: "브랜치 목표 분석: PROJ-123 이슈의 목적은 무엇인가? Acceptance Criteria는 무엇인가? 이 MR이 달성해야 할 구체적 목표는?",
     thoughtNumber: 1,
     totalThoughts: 3,
     nextThoughtNeeded: true
   })
   ```

   **1-5. 브랜치 목표 요약 작성**

   다음 정보를 수집하여 리포트 최상단에 배치:
   - **JIRA 이슈 키 및 제목**
   - **이슈 유형** (Story / Bug / Task / Epic)
   - **Acceptance Criteria 목록**
   - **연관 이슈** (Parent Epic, Linked Issues)
   - **스프린트/마일스톤** (해당 시)

2. **Git 변경사항 분석**
   - `git diff` 실행하여 변경된 파일 목록 확인
   - Sequential Thinking으로 변경 범위 및 영향도 분석
   - Serena로 변경된 심볼 및 의존성 파악

3. **프로젝트 문서 수집**
   - README.md, CHANGELOG, CLAUDE.md 읽기
   - Serena Context7으로 프로젝트 전체 맥락 이해
   - Serena memory에서 모든 관련 메모리 읽기 (`architecture_decisions.md`, `code_patterns.md`, `known_issues.md`)

4. **Confluence 문서 검색** (필요시)
   - Atlassian MCP로 관련 기술 문서 검색
   - Sequential Thinking으로 기술 명세 추출

---

### Phase 2: Code Analysis (코드 분석)

**목표**: 6가지 검증 항목을 체계적으로 분석

**주요 MCP**: Sequential Thinking (필수), Serena

**6가지 검증 항목**:

각 검증 항목의 상세 프로세스는 `references/verification_guides/`에 분리되어 있습니다:

1. **[아키텍처 일관성 검증](references/verification_guides/architecture_check.md)**
   - CLAUDE.md와 Serena memory의 아키텍처 결정사항과 비교
   - Sequential Thinking으로 레이어 분리 원칙 검증
   - Serena로 의존성 관계 확인

2. **[컨벤션 준수 확인](references/verification_guides/convention_check.md)**
   - README와 CLAUDE.md의 코딩 컨벤션 준수 확인
   - Sequential Thinking으로 네이밍, 스타일 체계적 검증
   - Serena로 유사 코드 패턴 검색

3. **[알려진 이슈 패턴 대조](references/verification_guides/known_issues_check.md)**
   - Serena memory에서 known_issues 읽기
   - Sequential Thinking으로 과거 버그 패턴 대조
   - Serena로 과거 버그와 유사 패턴 검색

4. **JIRA 요구사항 검증 (자동화)**

   **목적**: MR이 JIRA AC를 충족하는지 자동 검증

   **프로세스**:

   **4-1. requirement-validator Agent 호출 (Mode 4: Final Gate)**

   ```typescript
   // 사용자에게 알림
   "🤖 requirement-validator agent로 AC 최종 검증 중..."

   // MR 브랜치 변경사항 수집
   Bash({ command: "git diff main...HEAD --name-only" })

   // Agent 호출
   // Mode 4: Final Gate
   // Input: JIRA 이슈 키, MR 전체 변경사항
   // Output: AC별 상세 검증 리포트 (코드 품질, 보안, 테스트 포함)
   ```

   **4-2. 리포트를 MR_CODE_REVIEW.md에 통합**

   requirement-validator의 출력을 그대로 포함:

   ```markdown
   ## 🎯 JIRA 요구사항 검증

   ### AC 달성 요약
   - **총 AC**: 3개
   - **구현 완료**: 2개 (66%)
   - **미구현**: 1개 (AC#2)

   ### 상세 분석

   #### AC#1: 이메일 로그인 ✅
   [requirement-validator Mode 4 출력 참조]

   #### AC#2: 5회 실패 시 계정 잠금 ❌
   [requirement-validator Mode 4 출력 참조]

   ### 최종 판정

   🔴 **MR BLOCKED** - AC#2 미구현으로 인해 merge 금지
   ```

   **4-3. 추가 검증 (기존 로직 유지)**

   requirement-validator 결과 외에도:
   - 보안 취약점 (OWASP Top 10)
   - 코드 컨벤션 준수
   - 테스트 커버리지

   이들은 기존 Phase 2-5, 2-6에서 계속 수행

5. **[보안 및 품질 리뷰](references/verification_guides/security_review.md)**
   - Sequential Thinking으로 OWASP Top 10 체계적 분석
   - Serena로 보안 패턴 및 취약점 검색
   - 주요 보안 항목: SQL Injection, XSS, CSRF, Authentication, Authorization

6. **[테스트 커버리지 평가](references/verification_guides/test_coverage.md)**
   - Sequential Thinking으로 테스트 품질 평가
   - Serena로 테스트 파일 및 미테스트 함수 찾기
   - 변경된 코드의 테스트 커버리지 확인

---

### Phase 3: Report Generation (리포트 생성)

**목표**: MR_CODE_REVIEW.md 리포트 생성

**프로세스**:
1. **리뷰 요약 작성**
   - 총 리뷰 항목 개수
   - 위험도별 분류: 🔴 Critical, 🟡 High, 🟢 Medium

2. **상세 분석 결과 작성**
   - 각 이슈별 제목, 설명, 위치, 개선 제안 포함
   - 위치 표기: `[src/api/user.ts:42](src/api/user.ts#L42)`

3. **리포트 구조** (references/review_template.md 참조):
   ```markdown
   # MR Code Review: [MR 제목]

   ## 요약
   - 총 리뷰 항목: N개
   - 🔴 Critical: N개 - 반드시 수정 필요
   - 🟡 High: N개 - 수정 강력 권장
   - 🟢 Medium: N개 - 개선 권장

   ## 🔴 Critical Issues

   ### 1. [이슈 제목]
   **위치**: [file_path.ts:123](path/to/file.ts#L123)
   **설명**: ...
   **개선 제안**: ...

   ## 🟡 High Priority Issues
   ...

   ## 🟢 Medium Priority Issues
   ...

   ## ✅ 잘된 점
   ...
   ```

**출력**: `MR_CODE_REVIEW.md`

---

### Phase 4: Dependency Security Analysis (선택적)

**목적**: 의존성 취약점 자동 탐지 및 보고

**실행 조건**: MR에 package.json, package-lock.json, yarn.lock 변경 포함 시

**주요 MCP**: Sequential Thinking

**프로세스**:

**1. package.json 변경 확인**

```bash
# Git diff로 의존성 파일 변경 확인
git diff --name-only | grep -E "package\.json|package-lock\.json|yarn\.lock|pnpm-lock\.yaml"
```

변경이 있으면 Phase 4 실행, 없으면 skip.

**2. npm audit 실행**

```bash
# npm audit 실행 및 JSON 결과 저장
npm audit --json > audit-result.json

# Critical/High 취약점 필터링
jq '.vulnerabilities | to_entries[] | select(.value.severity == "critical" or .value.severity == "high")' audit-result.json
```

**3. 취약점 분석 (Sequential Thinking 활용)**

각 취약점을 Sequential Thinking으로 분석:

```typescript
// 취약점 발견 시
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "axios@0.21.0에서 CVE-2021-3749 (SSRF) 취약점 발견. severity: CRITICAL. 영향: Server-Side Request Forgery 가능",
  thoughtNumber: 1,
  totalThoughts: 3,
  nextThoughtNeeded: true
})

mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "해결 방법: axios@0.21.2 이상으로 업데이트. Breaking changes 없음 (CHANGELOG 확인). 즉시 적용 권장",
  thoughtNumber: 2,
  totalThoughts: 3,
  nextThoughtNeeded: true
})

mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "영향 범위: src/api/*.ts 파일 11개에서 사용. 프로덕션 환경 포함. 긴급 수정 필요",
  thoughtNumber: 3,
  totalThoughts: 3,
  nextThoughtNeeded: false
})
```

**4. 보고서 섹션 추가**

MR_CODE_REVIEW.md에 다음 섹션 추가:

```markdown
## 🔐 의존성 보안 분석

### CRITICAL 취약점 (즉시 수정 필요 🔴)

#### 1. axios@0.21.0: CVE-2021-3749 (SSRF)
**위치**: package.json:15
**영향**: Server-Side Request Forgery 공격 가능. 공격자가 임의의 내부 서버에 요청을 보낼 수 있음
**해결 방법**:
- `npm install axios@latest` (v0.21.2 이상)
- Breaking changes 없음
- 프로덕션 배포 전 반드시 수정 필요

**영향 범위**:
- src/api/user.ts:12
- src/api/payment.ts:8
- src/utils/http-client.ts:5
(총 11개 파일)

---

### HIGH 취약점 (우선 수정 권장 🟡)

#### 1. lodash@4.17.19: Prototype Pollution
**위치**: package.json:18
**영향**: 객체 프로토타입 오염 가능. 애플리케이션 로직 변조 가능
**해결 방법**:
- `npm install lodash@4.17.21`
- Breaking changes 없음
- 24시간 내 수정 권장

**영향 범위**:
- src/utils/data.ts:7
- src/services/transform.ts:12
(총 5개 파일)

---

### 권장사항

1. **즉시 조치**: CRITICAL 취약점 0개 될 때까지 MR merge 금지
2. **24시간 내**: HIGH 취약점 모두 수정
3. **정기 점검**: 매주 `npm audit` 실행 권장
4. **CI/CD 통합**: 취약점 발견 시 자동으로 PR 실패하도록 설정 권장

**Dependencies Updated**:
```bash
npm install axios@latest lodash@latest
npm audit fix --force  # 자동 수정 시도
```
```

**Best Practices**:
- Phase 4는 Phase 3 (Report Generation) 직후 실행
- 의존성 파일 변경이 없으면 이 Phase는 skip
- CRITICAL 취약점 발견 시 MR 승인 보류 권장
- HIGH 취약점은 warning으로 표시하되 blocking하지 않음

---

## Resources

이 skill은 다음 리소스를 포함합니다:

### references/

문서 및 가이드:

- `review_template.md`: MR_CODE_REVIEW.md 리포트 템플릿
- `review_checklist.md`: 6가지 검증 항목별 체크리스트
- `inline_comment_format.md`: GitLab discussion 포맷 가이드 (선택적 사용)

### references/verification_guides/

6가지 검증 로직의 상세 프로세스 (각각 별도 파일):

1. `architecture_check.md`: 아키텍처 일관성 검증 프로세스
2. `convention_check.md`: 컨벤션 준수 확인 프로세스
3. `known_issues_check.md`: 알려진 이슈 패턴 대조 프로세스
4. `jira_validation.md`: JIRA 요구사항 검증 프로세스
5. `security_review.md`: 보안 및 품질 리뷰 프로세스
6. `test_coverage.md`: 테스트 커버리지 평가 프로세스

각 파일에는 다음이 포함됩니다:
- Sequential Thinking MCP 사용 예시 (최소 3개)
- Serena MCP 사용 예시 (최소 3개)
- Atlassian MCP 사용 예시 (해당 시)
- 검증 항목 체크리스트
- TypeScript 코드 예시

---

## Best Practices

### 리뷰 품질 향상

1. **Sequential Thinking 적극 활용**
   - 각 검증 항목을 7-10개 사고 단계로 분해
   - thoughtNumber와 totalThoughts를 명확히 설정
   - 체계적 순차 분석으로 누락 방지

2. **Serena Memory 활용**
   - 프로젝트별로 architecture_decisions.md, code_patterns.md, known_issues.md 유지
   - 리뷰 과정에서 발견한 새로운 패턴/이슈를 memory에 저장
   - 다음 MR 리뷰 시 누적된 지식 활용

3. **JIRA 연동**
   - MR description에 JIRA 이슈 ID 포함 (예: "Closes PROJ-123")
   - Acceptance Criteria 각각을 개별 검증
   - 미충족 요구사항은 Critical 이슈로 표시

### 성능 최적화

1. **API 비용 절감**
   - Serena Context7 호출을 최소화 (캐싱 활용)
   - Sequential Thinking은 필요한 경우에만 사용
   - 간단한 파일 읽기는 Read tool 사용

2. **실행 시간 단축**
   - 변경된 파일만 집중 분석
   - 전체 프로젝트 스캔은 필요시에만
   - 병렬 처리 가능한 검증 항목은 동시 실행

### False Positive 감소

1. **프롬프트 튜닝**
   - verification_guides/*.md의 Sequential Thinking 프롬프트 개선
   - 프로젝트별 특성 반영
   - 개발자 피드백 수집 및 반영

2. **컨텍스트 정확성**
   - CLAUDE.md를 최신 상태로 유지
   - Serena memory를 주기적으로 업데이트
   - 프로젝트 규칙 변경 시 즉시 반영

---

## Troubleshooting

### MCP 서버 연결 실패

**증상**: "MCP server not available" 오류

**해결 방법**:
1. 환경 변수 확인 (`ANTHROPIC_API_KEY`, `ATLASSIAN_CLOUD_ID`)
2. `claude-code mcp list` 실행하여 서버 목록 확인
3. `claude-code mcp test serena` 실행하여 연결 테스트

### JIRA/Confluence 조회 실패

**증상**: JIRA 이슈 또는 Confluence 문서 조회 실패

**해결 방법**:
1. `ATLASSIAN_CLOUD_ID`, `ATLASSIAN_API_TOKEN` 환경 변수 확인
2. Token 권한 확인 (read:jira-user, read:confluence-content.summary 필요)
3. Atlassian MCP 테스트: `claude-code mcp test atlassian`

### Serena Memory 읽기 실패

**증상**: "Memory file not found" 오류

**해결 방법**:
1. Serena memory 디렉토리 확인
2. `mcp__plugin_workflow-skills_serena__list_memories()` 실행하여 사용 가능한 메모리 확인
3. 필요한 메모리 파일 생성 (architecture_decisions.md, code_patterns.md, known_issues.md)

---

## Versioning & Updates

**Current Version**: 1.0.0

**Update Strategy**:
- **프롬프트 튜닝**: verification_guides/*.md 파일 수정
- **워크플로우 변경**: SKILL.md의 Phase 섹션 수정
- **재패키징**: `package_skill.py mr-code-review` 실행

**Feedback Loop**:
- False positive 발견 시:
  1. 피드백 패턴 분석
  2. verification_guides/*.md의 Sequential Thinking 프롬프트 개선
  3. 재패키징 및 로컬 테스트
  4. 개선 효과 확인
