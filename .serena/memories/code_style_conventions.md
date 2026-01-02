# 코드 스타일 및 컨벤션

## 언어 규칙

- **모든 Skills/Agents 출력**: 한국어 필수
- **문서 작성**: 한국어 (CLAUDE.md, README.md, CHANGELOG.md)
- **코드 주석**: 한국어/영어 혼용 가능

## Skill 작성 가이드

### 메타데이터 품질
- `description`: Claude가 언제 스킬을 사용할지 결정
- 트리거 시나리오를 구체적으로 명시
- 3인칭 사용 ("you should" X → "To accomplish X, do Y" O)

### 지시문 스타일
- 명령형/부정사 형태 사용 (동사 우선)
- 예: "사용자에게 확인을 요청한다" (O)
- 예: "사용자에게 확인을 요청해야 합니다" (X)

### Progressive Disclosure
- SKILL.md는 간결하게 유지 (<5k words)
- 상세 정보는 `references/` 폴더로 분리

## Agent 작성 가이드

- 단일 책임 원칙 (여러 Skills에서 공유할 때만 생성)
- 명확한 트리거 조건
- 한국어 필수
- 최대 4-5 phases

## 디렉토리 구조 규칙

```
plugins/
└── {plugin-name}/
    ├── .claude-plugin/
    │   └── plugin.json       # 플러그인 메타데이터
    ├── .mcp.json             # MCP 서버 설정 (선택)
    ├── skills/               # 스킬 폴더 (자동 인식)
    │   └── {skill-name}/
    │       ├── SKILL.md      # 스킬 정의
    │       └── references/   # 참조 자료
    ├── agents/               # 에이전트 폴더
    │   └── {agent-name}.md
    └── README.md             # 플러그인 문서
```

## 버전 관리

- Semantic Versioning (Major.Minor.Patch)
- Breaking Change → Major 버전 증가
- 새 기능 → Minor 버전 증가
- 버그 수정 → Patch 버전 증가

## Git 커밋 규칙

```
feat: 새 기능 추가
fix: 버그 수정
refactor: 리팩토링
docs: 문서 수정
chore: 기타 작업
```
