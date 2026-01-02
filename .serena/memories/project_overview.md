# wogus-plugin 프로젝트 개요

## 목적
Claude Code Skills, Agents, 커스텀 명령어를 포함하는 개인 플러그인 컬렉션 저장소.
체계적인 소프트웨어 개발 워크플로우를 위한 도구 모음.

## 버전
v3.11.0

## 저장소 구조

```
wogus-plugin/
├── .claude-plugin/
│   └── marketplace.json       # 카탈로그 (5 plugins)
├── plugins/                   # 모든 플러그인
│   ├── workflow-bundle/       # 메인 워크플로우 플러그인
│   │   ├── skills/            # 5개 Skills
│   │   │   ├── analyze/       # 분석 스킬
│   │   │   ├── plan/          # 계획 스킬
│   │   │   ├── execute/       # 실행 스킬
│   │   │   ├── record/        # 문서화 스킬
│   │   │   └── mr-review/     # MR 리뷰 스킬
│   │   └── agents/
│   │       └── requirement-validator.md
│   ├── terraform/             # Terraform MCP
│   ├── amplitude/             # Amplitude MCP
│   ├── slack/                 # Slack MCP
│   └── atlassian/             # Atlassian MCP
├── CLAUDE.md                  # 프로젝트 가이드
├── README.md                  # 사용자 문서
└── CHANGELOG.md               # 버전 히스토리
```

## 플러그인 구성 (5개)

1. **workflow-bundle**: 메인 워크플로우 (5 Skills + 1 Agent + sequential-thinking MCP)
2. **terraform**: Terraform MCP 서버
3. **amplitude**: Amplitude MCP 서버
4. **slack**: Slack MCP 서버
5. **atlassian**: Atlassian MCP 서버

## Skills (5개)

| Skill | 목적 | 출력 |
|-------|------|------|
| analyze | 체계적 근본 원인 분석 | `[ISSUE_ID]_REPORT.md` |
| plan | 고품질 구현 계획 수립 | `[FEATURE]_PLAN.md` |
| execute | 계획 실행 + TodoList 추적 | 코드 구현 + 테스트 결과 |
| record | 워크플로우 산출물 문서화 | README, CHANGELOG 업데이트 |
| mr-review | GitLab MR 코드 리뷰 | `INLINE_DISCUSSION.json` + `SUMMARY_COMMENT.md` |

## Agent (1개)

- **requirement-validator**: JIRA AC와 코드 자동 매핑, 요구사항 달성 검증

## 워크플로우

```
analyze → plan → execute → record
```

## 언어
- 모든 Skills/Agents 출력: 한국어
- 코드 스타일: Python, Markdown
