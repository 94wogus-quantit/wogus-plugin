# 작업 완료 시 해야 할 일

## 체크리스트

### 1. 버전 업데이트
- [ ] `.claude-plugin/marketplace.json`의 `metadata.version` 업데이트
- [ ] 각 플러그인의 `plugin.json` 버전 확인

### 2. 문서 업데이트
- [ ] CHANGELOG.md에 변경사항 기록
- [ ] README.md 업데이트 (필요시)
- [ ] CLAUDE.md 아키텍처 결정사항 추가 (중요 변경시)

### 3. Git 커밋
```bash
# 변경사항 확인
git status
git diff

# 커밋
git add .
git commit -m "feat/fix/refactor: 변경 내용 (vX.Y.Z)"

# 푸시
git push origin main
```

### 4. 사용자 알림
- Marketplace 사용자: `/marketplace refresh` 실행 필요

## 주의사항

### 보호된 브랜치
- main, master, staging 브랜치에서 직접 작업 금지
- feature 브랜치에서 작업 후 MR/PR 생성

### Breaking Changes
- Major 버전 업데이트 필요
- CHANGELOG.md에 마이그레이션 가이드 포함
- README.md에 호환성 정보 추가

## 워크플로우 완료 후

```
analyze → plan → execute → record
```

record 스킬 실행 시:
1. README, CHANGELOG, CLAUDE docs 자동 업데이트
2. Git commit/push 옵션 제공
3. Serena 메모리에 기술 인사이트 저장
