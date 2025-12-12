# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Personal plugin collection repository containing Claude Code Skills, Agents, and custom commands for systematic software development workflows.

**Key Artifacts (v3.6.0):**
- **Skills**: Workflow orchestrators for multi-step processes (분석, 계획, 실행, 문서화)
- **Agents**: AC (Acceptance Criteria) traceability (requirement-validator만 유지)
- **Custom Commands**: Workflow automation commands (별도 설치)
- **Reference Materials**: Templates and pattern catalogs

## Repository Structure

```
wogus-plugin/  (v3.6.0)
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

### analyze-issue (v3.5.0)
Systematic root cause analysis with branch validation.
- **브랜치 자동 생성** (main/master/staging 감지 시)
- **Output**: `[ISSUE_ID]_REPORT.md`
- **Integration**: First step in workflow

### plan-builder (v3.5.0)
Create high-quality, thoroughly reviewed implementation plans.
- **Iterative review loop** (ZERO 이슈까지 반복)
- **브랜치 검증** (feature 브랜치 확인)
- **Output**: `[FEATURE]_PLAN.md`
- **Integration**: Second step in workflow

### execute-plan (v3.5.0)
Execute approved implementation plans with TodoList tracking.
- **브랜치 검증** (보호된 브랜치 경고)
- **Output**: Code implementation + test results
- **Integration**: Third step in workflow

### document (v3.5.0)
Consolidate workflow artifacts and update project documentation.
- **브랜치 검증** + Git commit/push
- **Output**: Updated README, CHANGELOG, CLAUDE docs
- **Integration**: Final step in workflow

### mr-code-review (v3.6.0)
GitLab MR의 코드 변경사항을 분석하여 맥락 기반 종합 리뷰 수행.
- **7가지 검증**: 아키텍처, 비즈니스 로직, 컨벤션, 이슈 패턴, JIRA, 보안, 테스트
- **2개 파일 출력**: `INLINE_DISCUSSION.json` + `SUMMARY_COMMENT.md`
- **Trivy 범용 보안 스캔**: 모든 언어 지원
- **Phase별 중간 산출물**: `.mr-review/` 디렉토리

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
- **Version**: Semantic versioning (current: v3.6.0)
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

### v3.6.0 - mr-code-review 대규모 개선 (2025-12-12)

**컨텍스트**:
mr-code-review가 단일 파일(MR_CODE_REVIEW.md) 출력, 6가지 검증, 언어별 보안 도구 사용으로 제한적이었음.

**문제점**:
- **출력 형식 제한**: 마크다운 단일 파일로 GitLab Inline Discussion 자동화 어려움
- **비즈니스 로직 검증 부재**: JIRA 목표 대비 구현 정확성 검증 누락
- **언어별 보안 도구**: npm audit은 JavaScript 전용, 다른 언어 지원 필요
- **Context 손실**: 긴 리뷰에서 Phase 간 맥락 손실 발생

**결정**: 4가지 주요 개선

1. **2개 파일 출력**:
   - `INLINE_DISCUSSION.json`: GitLab Inline Discussion 자동화용
   - `SUMMARY_COMMENT.md`: 전체 요약 마크다운

2. **7가지 검증으로 확장**:
   - 기존 6가지 + "비즈니스 로직 정확성 검증" 추가
   - JIRA 목표 대비 구현 정확성, 엣지케이스, 경계값 검증

3. **Trivy 범용 보안 스캔**:
   - npm audit, pip-audit 등 언어별 도구 → Trivy로 통합
   - JavaScript, Python, Go, Java, Rust 등 모든 언어 지원

4. **Phase별 중간 산출물**:
   - `.mr-review/1_CONTEXT.md`: 맥락 수집 결과
   - `.mr-review/2_CODE_ANALYSIS.md`: 코드 분석 결과
   - `.mr-review/3_SECURITY_ANALYSIS.md`: 보안 분석 결과
   - Phase 4에서 중간 파일 읽어 최종 리포트 생성

**영향**:
- GitLab Inline Discussion 자동화 가능
- 비즈니스 로직 정확성 검증으로 품질 향상
- 모든 언어 프로젝트에서 보안 스캔 가능
- Context 손실 없이 긴 리뷰 수행 가능
- Breaking Change: 출력 파일 변경 (MR_CODE_REVIEW.md → 2개 파일)

**관련 파일**:
- mr-code-review/SKILL.md: 전체 재구성
- mr-code-review/references/inline_discussion_template.json: 신규
- mr-code-review/references/summary_comment_template.md: 신규
- mr-code-review/references/verification_guides/business_logic_check.md: 신규

**버전**: v3.5.3 → v3.6.0

---

### v3.5.0 - 브랜치 검증으로 단순화 (Worktree 제거) (2025-12-11)

**컨텍스트**:
v3.4.x에서 도입한 Git Worktree 자동 관리 기능이 실무 워크플로우와 맞지 않음. 사용자는 브랜치 분리만으로 충분하며, Worktree는 오히려 복잡도를 증가시킴.

**문제점**:
- **불필요한 복잡도**: Worktree 생성/관리/삭제 로직이 복잡하고 디버깅 어려움
- **사용자 혼란**: Worktree 디렉토리 구조가 낯설고 이해하기 어려움
- **브랜치 보호 부족**: Worktree는 있지만 main/master/staging 브랜치 직접 수정 방지는 부족
- **실무 미스매치**: 대부분 사용자는 브랜치만 분리하면 충분

**결정**: Worktree 기능 완전 제거, 브랜치 검증으로 대체

**Phase 0 변경사항** (4개 스킬 모두):
- **이전**: Worktree 생성/확인/이동
- **이후**: 보호된 브랜치 (main/master/staging) 검증

**브랜치 검증 로직**:
```bash
# main, master, staging 브랜치인지 확인
if [[ "$CURRENT_BRANCH" == "main" ]] || [[ "$CURRENT_BRANCH" == "master" ]] || [[ "$CURRENT_BRANCH" == "staging" ]]; then
  echo "⚠️ 경고: $CURRENT_BRANCH 브랜치에서 작업 중입니다!"
  echo "⚠️ main/master/staging 브랜치에서는 작업할 수 없습니다."
  # 사용자 확인 후 진행 또는 중단
fi
```

**Skill별 Phase 0 동작**:
- **analyze-issue**: 보호된 브랜치 감지 시 새 feature 브랜치 자동 생성
- **plan-builder**: 보호된 브랜치 경고 및 권장 워크플로우 안내
- **execute-plan**: 보호된 브랜치 경고 (코드 수정 위험 강조)
- **document**: 보호된 브랜치 경고 (문서 커밋 위험)

**영향**:
- **단순화**: Worktree 관련 코드 500+ 라인 제거
- **명확성**: 브랜치 검증만으로 충분한 안전장치 제공
- **보호 강화**: main/master/staging 3개 브랜치 모두 보호
- **사용자 친화**: 익숙한 브랜치 워크플로우 유지
- **Breaking Change**: Worktree 의존 워크플로우는 영향받음 (소수 사용자)

**제거된 기능**:
- analyze-issue Phase 0: Worktree 자동 생성
- document Phase 9: Worktree 정리 (삭제/아카이브)
- Git commit 강제 로직 (Phase 6 직후)

**유지된 기능**:
- 브랜치 검증 (보호된 브랜치 경고)
- Git commit/push 옵션 (document Phase 9)
- CRITICAL 강제 블록 (Phase 0 건너뛰기 방지)

**관련 파일**:
- analyze-issue/SKILL.md:43-122 - Phase 0 브랜치 검증
- plan-builder/SKILL.md:67-116 - Phase 0 브랜치 검증
- execute-plan/SKILL.md:61-112 - Phase 0 브랜치 검증
- document/SKILL.md:85-131 - Phase 0 브랜치 검증
- document/SKILL.md:790-834 - Phase 9 Git commit/push (Worktree 제거)

**버전**: v3.4.1 → v3.5.0

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

- **Current version**: v3.6.0 (Skills + Agents + 브랜치 보호 + mr-code-review 개선)
- Skills use MCP servers (serena, atlassian, sentry, context7, sequential-thinking, terraform, amplitude, chrome-devtools)
- Agents use MCP servers (serena, sequential-thinking, context7, atlassian)
- All skills and agents designed for Korean language output
- Reference files loaded on-demand to manage context efficiently
- Marketplace distribution requires GitHub public repository
- Version updates reflected when users run `/marketplace refresh`
