# Personal Claude Code Plugins

Claude Code의 확장 기능(Plugins)을 모아둔 저장소입니다. Skills를 중심으로 체계적인 개발 워크플로우를 지원합니다.

## 🔌 Plugin이란?

**Plugin**은 Claude Code를 확장하는 모든 기능의 총칭입니다:

- **🤖 Skills**: AI 기반 특화 기능 (분석, 계획, 실행, 문서화 등)
- **⚙️ Custom Commands**: 워크플로우 자동화 커맨드 (별도 설치 필요)
- **🔗 MCP Servers**: 외부 도구/서비스 통합 (별도 설정 필요)

이 저장소는 **Skills**를 제공하며, Custom Commands와 MCP Servers는 별도로 설치/설정해야 합니다.

## 🌐 언어 정책

**모든 스킬은 기본적으로 한국어로 작동합니다.**

- ✅ 모든 문서, 리포트, 계획서는 **한국어**로 생성
- ✅ 사용자 응답과 설명은 **한국어**로 제공
- ✅ 코드 주석과 문서화는 **한국어**로 작성
- 🔄 예외: 사용자가 다른 언어로 작성하면 해당 언어로 응답

이는 모든 스킬에 강제 적용되는 **필수 정책**입니다.

## 🚀 Getting Started

### 마켓플레이스로 설치 (권장)

1. Claude Code에서 marketplace 추가:
   ```bash
   /marketplace add git@github.com:94wogus-quantit/wogus-plugin.git
   ```

2. 원하는 스킬 설치:
   ```bash
   /plugin install analyze-issue
   /plugin install plan-builder
   /plugin install execute-plan
   /plugin install document
   /plugin install mr-code-review
   ```

3. 설치 확인:
   ```bash
   /plugin list
   ```

### 로컬 패키징으로 설치

1. 스킬을 패키징하여 `.zip` 파일 생성:
   ```bash
   python3 ~/.claude/plugins/marketplaces/anthropic-agent-skills/skill-creator/scripts/package_skill.py analyze-issue
   ```

2. Claude Code에서 설치:
   ```bash
   /plugin install analyze-issue.zip
   ```

## 📦 Available Skills

### analyze-issue

버그와 이슈의 근본 원인을 체계적으로 분석하는 스킬입니다.

**주요 기능:**
- JIRA 이슈 및 Sentry 에러 조사
- 다각도 가설 수립 및 검증
- 코드베이스 탐색을 통한 문제 지점 파악
- 상세한 분석 리포트 자동 생성 (`*_REPORT.md`)

**사용 시점:**
- JIRA 이슈나 버그 리포트 분석 시
- Sentry 에러나 프로덕션 인시던트 조사 시
- 복잡한 문제의 근본 원인 분석이 필요할 때

**설치:**
```bash
# Claude Code에 스킬 설치
/plugin install analyze-issue.zip
```

### mr-code-review

GitLab MR의 코드 변경사항을 분석하여 맥락 기반 종합 리뷰를 수행하는 스킬입니다.

**주요 기능:**
- **6가지 종합 검증**: 아키텍처, 컨벤션, 이슈 패턴, JIRA 요구사항, 보안, 테스트
- **MCP 기반 심화 분석**: Sequential Thinking + Serena Context7 + Atlassian 적극 활용
- **리포트 생성**: MR_CODE_REVIEW.md (3단계 위험도: 🔴 Critical, 🟡 High, 🟢 Medium)
- **개선 제안**: 각 이슈별 위치, 설명, 개선 방법 제공
- 프로젝트 문서(README, CLAUDE.md), Serena memory 활용

**사용 시점:**
- GitLab MR 코드 리뷰가 필요할 때
- 맥락 기반 종합 리뷰가 필요한 중요한 MR (아키텍처 변경, 신규 기능)
- 프로젝트 문서와 JIRA 요구사항을 종합 검증해야 할 때
- 보안, 품질, 테스트 커버리지를 체계적으로 검증하고 싶을 때

**사용 방법:**
```bash
# 로컬에서 직접 실행
claude-code exec "Use mr-code-review skill to review this MR. Branch: feature/user-auth"

# 또는 대화형으로
# "mr-code-review skill로 이 MR 리뷰해줘"
```

**설치:**
```bash
# Claude Code에 스킬 설치
/plugin install mr-code-review.zip
```

### plan-builder

자동 반복 검토를 통해 고품질 구현 계획을 생성하는 스킬입니다.

⚠️ **v1.6.0 Breaking Change**: 피드백 루프가 더 엄격해졌습니다. 이제 **최소 2-3회 이상 반복**하며, **ZERO 이슈**가 될 때까지 강제로 반복합니다.

**주요 기능:**
- **명시적 WHILE 루프**: 계획 생성 → 검토 → 피드백 반영 무한 반복 (ZERO 이슈까지)
- **엄격한 품질 기준**: "Approve"는 ZERO 이슈일 때만 가능 (Good ≠ Strong)
- **버전 추적**: 각 반복마다 `*_PLAN_REVIEW_v[N].md` 파일 생성 및 보존
- **Iteration Status Report**: 각 반복 후 이슈 카운트 및 결정 사항 출력
- 모든 태스크에 테스팅 전략 필수 포함
- 태스크 독립성 검증

**v1.6.0 변경사항 (2025-12-09)**:
- ✅ Phase 2를 WHILE 루프 구조로 완전 재작성
- ✅ "Approve with Changes" 제거 → Binary decision (Approve / Needs Iteration)
- ✅ 리뷰 파일 버전 추적 메커니즘 추가
- ✅ Loop 다이어그램 및 테스트 시나리오 추가
- ⚠️ 이전보다 더 많은 반복이 발생할 수 있으나, 계획 품질이 크게 향상됨

**사용 시점:**
- `*_REPORT.md`에서 구현 계획 생성 시
- 복잡한 기능이나 아키텍처 변경 계획 시
- 실행 전 고신뢰도 계획이 필요할 때
- 품질과 완성도가 속도보다 중요할 때

**설치:**
```bash
# Claude Code에 스킬 설치
/plugin install plan-builder.zip
```

### execute-plan

승인된 구현 계획을 체계적으로 실행하는 스킬입니다.

**주요 기능:**
- TodoList 자동 생성 및 진행 추적
- 6단계 체계적 실행 프로세스 (로드 → TodoList 설정 → 실행 → 테스트 → 문서화 → 요약)
- 자동 테스트 실행 및 검증
- 코드 문서화 및 Serena 메모리 저장
- **순수 구현에만 집중** (문서 정리는 document 스킬에서 처리)

**사용 시점:**
- 승인된 `*_PLAN.md` 파일 실행 시
- 체계적인 진행 추적이 필요할 때
- 모든 성공 기준 검증이 필요할 때
- 코드 구현에만 집중하고 싶을 때

**Note**: 문서 업데이트와 파일 정리는 `document` 스킬에서 처리합니다.

**설치:**
```bash
# Claude Code에 스킬 설치
/plugin install execute-plan.zip
```

### document

워크플로우 아티팩트를 수집하여 프로젝트 문서를 종합적으로 업데이트하는 스킬입니다.

**주요 기능:**
- 9단계 체계적 문서화 프로세스
- **README, CHANGELOG, CLAUDE 문서 자동 업데이트**
- **JIRA 이슈에 구현 완료 사항 정리 및 코멘트**
- Serena 메모리에 기술 인사이트 저장
- 워크플로우 아티팩트 아카이브/정리
- Keep a Changelog 형식 준수

**사용 시점:**
- **`execute-plan` 완료 후 반드시 실행** (README/CHANGELOG 업데이트)
- 프로젝트 문서화가 필요한 경우
- 아키텍처 결정사항 문서화 시
- 마이그레이션 가이드 생성 시
- git commit 전 최종 문서화

**설치:**
```bash
# Claude Code에 스킬 설치
/plugin install document.zip
```

**워크플로우 통합:**
```
analyze-issue
  → *_REPORT.md 생성
  → plan-builder (자동 반복 검토)
    └─> *_PLAN.md (승인된 계획)
  → execute-plan (코드 구현 및 테스트)
    └─> 구현 완료, 코드 문서화, 파일 정리
  → document (필수: 프로젝트 문서화)
    └─> README, CHANGELOG, CLAUDE 문서, Serena 메모리
```

## 📋 권장 워크플로우

### 표준 워크플로우
```
1. analyze-issue [JIRA/버그 리포트]
   └─> *_REPORT.md 생성

2. plan-builder [REPORT 참조]
   └─> 자동 반복 검토 (계획 → 검토 → 개선 → 재검토...)
   └─> *_PLAN.md (승인된 고품질 계획)

3. execute-plan [PLAN]
   └─> TodoList 생성 및 실행
   └─> 코드 구현 및 테스트
   └─> 코드 문서화 (inline comments, JSDoc 등)

4. document (필수)
   └─> README 업데이트 (기능, API, 설정 등)
   └─> CHANGELOG 업데이트 (변경 이력)
   └─> CLAUDE 문서 업데이트 (아키텍처 결정사항)
   └─> Serena 메모리 저장 (기술 인사이트)
   └─> JIRA 이슈 업데이트
   └─> 워크플로우 아티팩트 정리 (*_PLAN.md, *_REPORT.md)
```

**중요**:
- `plan-builder`는 자동으로 피드백 루프를 반복하여 고품질 계획을 보장합니다
- `execute-plan`은 코드 구현에, `document`는 문서화에 집중하도록 역할이 분리되어 있습니다
- 완전한 워크플로우를 위해서는 두 단계를 모두 실행해야 합니다

### MR 리뷰 워크플로우
```
mr-code-review [Branch/MR URL]
└─> MR_CODE_REVIEW.md 생성
└─> 아키텍처, 보안, 테스트 등 6가지 검증
```


## 📦 Marketplace Distribution

이 저장소는 **Claude Code Marketplace**로 배포되어 있습니다.

### 마켓플레이스 설정

마켓플레이스 구성은 [.claude-plugin/marketplace.json](.claude-plugin/marketplace.json)에 정의되어 있습니다:

```json
{
  "name": "wogus-plugins",
  "owner": {
    "name": "94wogus",
    "email": "94wogus@quantit.io"
  },
  "metadata": {
    "description": "체계적인 개발 워크플로우를 위한 Claude Code 스킬 모음 - 이슈 분석, 계획 수립, MR 리뷰, 실행, 문서화 (한국어 기본)",
    "version": "1.6.0",
    "repository": "https://github.com/94wogus-quantit/wogus-plugin",
    "homepage": "https://github.com/94wogus-quantit/wogus-plugin#readme",
    "license": "Private"
  },
  "plugins": [
    {
      "name": "workflow-skills",
      "description": "체계적인 개발 워크플로우를 위한 스킬 모음 - 이슈 분석, 계획 수립, MR 리뷰, 실행, 문서화",
      "source": "./",
      "skills": [
        "./analyze-issue",
        "./plan-builder",
        "./execute-plan",
        "./document",
        "./mr-code-review"
      ]
    }
  ]
}
```

### 마켓플레이스 사용 방법

**사용자 입장:**

1. 마켓플레이스 추가:
   ```bash
   /marketplace add git@github.com:94wogus-quantit/wogus-plugin.git
   ```

2. 사용 가능한 스킬 확인:
   ```bash
   /marketplace list
   ```

3. 원하는 스킬 설치:
   ```bash
   /plugin install workflow-skills:analyze-issue
   /plugin install workflow-skills:plan-builder
   # 또는 짧은 형식
   /plugin install analyze-issue
   ```

**배포자 입장:**

1. **GitHub Public 저장소 설정**
   - 저장소를 public으로 설정
   - `.claude-plugin/marketplace.json` 파일 작성
   - 스킬 소스 디렉토리 구조 유지

2. **버전 관리**
   - `marketplace.json`의 `metadata.version` 업데이트
   - 변경사항 커밋 및 푸시
   - 사용자는 마켓플레이스 갱신으로 최신 버전 확인 가능

3. **스킬 추가/수정**
   ```bash
   # 새 스킬 생성
   python3 ~/.claude/.../init_skill.py new-skill --path .

   # marketplace.json의 skills 배열에 추가
   # "skills": [..., "./new-skill"]

   # Git 커밋 및 푸시
   git add .
   git commit -m "feat: add new-skill"
   git push
   ```

### 마켓플레이스 vs 로컬 패키징

| 방식 | 장점 | 단점 |
|------|------|------|
| **Marketplace** | ✅ 자동 업데이트<br>✅ 중앙 관리<br>✅ 간편한 설치 | ⚠️ GitHub 의존성<br>⚠️ Public 저장소 필요 |
| **로컬 패키징** | ✅ 오프라인 가능<br>✅ 버전 고정 | ⚠️ 수동 업데이트<br>⚠️ 패키징 필요 |

**권장**: 개인/팀 사용은 Marketplace, 특정 버전 고정이 필요한 경우 로컬 패키징 사용

## 📁 Repository Structure

```
plugins/  (구 skills/)
├── .claude-plugin/         # Marketplace 설정
│   └── marketplace.json    # 플러그인 목록 및 메타데이터
│
├── analyze-issue/          # 이슈 분석 스킬
│   ├── SKILL.md           # 스킬 설명 및 가이드
│   └── references/        # 참조 문서
│       ├── report_template.md
│       └── common_bug_patterns.md
│
├── mr-code-review/        # MR 코드 리뷰 스킬 ✨ NEW
│   ├── SKILL.md
│   └── references/
│       ├── review_template.md
│       ├── review_checklist.md
│       ├── inline_comment_format.md
│       └── verification_guides/
│           ├── architecture_check.md
│           ├── convention_check.md
│           ├── known_issues_check.md
│           ├── jira_validation.md
│           ├── security_review.md
│           └── test_coverage.md
│
├── plan-builder/          # 계획 생성 스킬
│   ├── SKILL.md
│   └── references/
│       ├── plan_template.md
│       ├── review_checklist.md
│       ├── testing_strategy_guide.md
│       └── task_independence_guide.md
│
├── execute-plan/          # 계획 실행 스킬
│   └── SKILL.md
│
├── document/              # 문서화 스킬
│   └── SKILL.md
│
├── .gitignore            # Git 제외 설정
├── CLAUDE.md             # Claude Code 가이드
└── README.md             # 이 파일
```

## 🛠 Development

### 새로운 스킬 만들기

```bash
# 1. skill-creator로 템플릿 생성
python3 ~/.claude/plugins/marketplaces/anthropic-agent-skills/skill-creator/scripts/init_skill.py <skill-name> --path .

# 2. 스킬 커스터마이징 (SKILL.md, references 등 편집)

# 3. 패키징 (배포용 .zip 생성)
python3 ~/.claude/plugins/marketplaces/anthropic-agent-skills/skill-creator/scripts/package_skill.py <skill-folder>
```

### 스킬 구조

```
skill-name/
├── SKILL.md (required)      # 메타데이터 + 사용 가이드
├── scripts/ (optional)      # 실행 가능한 스크립트
├── references/ (optional)   # 참조 문서
└── assets/ (optional)       # 템플릿, 에셋
```

### Git 워크플로우

**버전 관리 대상:**
- ✅ 스킬 소스 디렉토리 (`analyze-issue/`, `plan-builder/` 등)
- ✅ 문서 파일 (`CLAUDE.md`, `README.md`)
- ✅ `.gitignore`

**제외 항목** (`.gitignore`로 관리):
- ❌ `.zip` 파일 (빌드 결과물)
- ❌ `.claude/` (개인 설정)
- ❌ IDE 설정, 로그, 캐시 등

**워크플로우:**
```bash
# 소스만 커밋
git add analyze-issue/ plan-builder/
git commit -m "feat: add new skill"

# 배포는 로컬에서 패키징
python3 ~/.claude/.../package_skill.py analyze-issue
/plugin install analyze-issue.zip
```

## 📝 License

개인 사용을 위한 저장소입니다.
