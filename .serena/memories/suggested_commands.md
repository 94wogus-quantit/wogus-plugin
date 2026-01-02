# 추천 명령어

## 시스템 명령어 (Darwin/macOS)

```bash
# 디렉토리 조회
ls -la
ls -R plugins/

# 파일 검색
find . -name "*.md" -type f
find . -name "SKILL.md"

# 텍스트 검색
grep -r "pattern" .
grep -rn "keyword" plugins/

# Git 명령어
git status
git diff
git log --oneline -10
git branch -a
```

## 플러그인 관리

```bash
# Marketplace에서 설치
/marketplace add git@github.com:94wogus-quantit/wogus-plugin.git
/marketplace refresh

# 플러그인 설치
/plugin install wogus-plugins:workflow-bundle
/plugin install wogus-plugins:terraform
/plugin install wogus-plugins:amplitude
/plugin install wogus-plugins:slack
/plugin install wogus-plugins:atlassian
```

## Skills 호출

```
# 분석 스킬
/analyze

# 계획 스킬
/plan

# 실행 스킬
/execute

# 문서화 스킬
/record

# MR 리뷰 스킬
/mr-review
```

## 개발 명령어

이 저장소는 주로 Markdown 문서로 구성되어 있어 빌드/테스트 명령어는 없음.

```bash
# 버전 확인
grep -r "version" .claude-plugin/marketplace.json

# 플러그인 구조 확인
cat plugins/workflow-bundle/.claude-plugin/plugin.json
```

## 문서 편집

- CLAUDE.md: 프로젝트 가이드 (아키텍처 결정사항 포함)
- README.md: 사용자 문서
- CHANGELOG.md: 버전 히스토리
