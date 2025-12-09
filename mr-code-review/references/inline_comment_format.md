# Inline Comment Format Guide

GitLab MR에 inline discussion을 생성하는 방법입니다.

## GitLab Discussion API 포맷

### glab CLI 사용

```bash
# 특정 파일의 특정 라인에 코멘트 생성
glab mr note create <MR_IID> \
  --message "코멘트 내용" \
  --file "src/path/to/file.ts" \
  --line 123

# 여러 라인에 걸친 코멘트 (line range)
glab mr note create <MR_IID> \
  --message "코멘트 내용" \
  --file "src/path/to/file.ts" \
  --line-start 120 \
  --line-end 125
```

### GitLab API 직접 사용

```bash
# POST /projects/:id/merge_requests/:merge_request_iid/discussions
curl --request POST \
  --header "PRIVATE-TOKEN: ${GITLAB_TOKEN}" \
  --header "Content-Type: application/json" \
  --data '{
    "body": "🔴 **Critical**: SQL Injection 위험\n\n사용자 입력이 직접 쿼리에 연결되어 있습니다. Prepared statement를 사용하세요.",
    "position": {
      "base_sha": "'"${BASE_SHA}"'",
      "start_sha": "'"${START_SHA}"'",
      "head_sha": "'"${HEAD_SHA}"'",
      "position_type": "text",
      "new_path": "src/api/user.controller.ts",
      "new_line": 45
    }
  }' \
  "https://gitlab.com/api/v4/projects/${PROJECT_ID}/merge_requests/${MR_IID}/discussions"
```

## Comment 메시지 포맷

### 위험도별 포맷

#### 🔴 Critical

```markdown
🔴 **Critical**: [이슈 제목]

**문제**:
[상세 설명]

**위험**:
[어떤 위험이 있는지]

**개선 제안**:
```typescript
// 권장 코드
```

**참고**: [관련 문서 링크]
```

#### 🟡 High Priority

```markdown
🟡 **High**: [이슈 제목]

**설명**:
[이슈 상세]

**개선 제안**:
[권장 방법]
```

#### 🟢 Medium Priority

```markdown
🟢 **Medium**: [이슈 제목]

**제안**:
[개선 방향]
```

## Severity Level 표시

### 이모지 사용

- 🔴 Critical
- 🟡 High
- 🟢 Medium
- ✅ Good (칭찬)
- 💡 Suggestion (아이디어)
- ⚠️ Warning (주의)

### 텍스트 사용

- `[CRITICAL]`
- `[HIGH]`
- `[MEDIUM]`
- `[INFO]`

## 실제 예시

### 예시 1: SQL Injection

```markdown
🔴 **Critical**: SQL Injection 취약점

**위치**: `src/api/user.controller.ts:45`

**문제**:
사용자 입력 `email`이 직접 SQL 쿼리에 연결되어 SQL Injection 공격에 취약합니다.

**현재 코드**:
```typescript
const query = `SELECT * FROM users WHERE email = '${email}'`;
```

**개선 제안**:
```typescript
const query = 'SELECT * FROM users WHERE email = ?';
const result = await db.execute(query, [email]);
```

**참고**: https://owasp.org/www-community/attacks/SQL_Injection
```

### 예시 2: 네이밍 개선

```markdown
🟢 **Medium**: 변수명 개선 권장

**설명**:
변수명 `d`가 의미가 불명확합니다. 더 명확한 이름을 사용하면 가독성이 향상됩니다.

**개선 제안**:
```typescript
// Before
const d = new Date();

// After
const currentDate = new Date();
```
```

### 예시 3: 에러 처리

```markdown
🟡 **High**: 에러 처리 누락

**설명**:
비동기 함수에서 에러 처리가 없어 예외 발생 시 앱이 크래시할 수 있습니다.

**개선 제안**:
```typescript
try {
  const result = await fetchUserData(userId);
  return result;
} catch (error) {
  logger.error('Failed to fetch user data:', error);
  throw new UserDataError('Unable to fetch user data');
}
```
```

## 스크립트 예시

### create-inline-comments.sh

```bash
#!/bin/bash
set -e

MR_IID=$1
REVIEW_FILE="MR_${MR_IID}_CODE_REVIEW.md"

# MR_CODE_REVIEW.md에서 위치 정보 추출 및 코멘트 생성
# 예: `file_path.ts:123` 형식의 위치를 파싱

while IFS= read -r line; do
  if [[ $line =~ \`([^\`]+):([0-9]+)\` ]]; then
    FILE="${BASH_REMATCH[1]}"
    LINE="${BASH_REMATCH[2]}"

    # 다음 섹션까지 읽어서 코멘트 내용으로 사용
    # (실제 구현은 더 복잡할 수 있음)

    echo "Creating comment on ${FILE}:${LINE}"

    # glab mr note create 실행
    # glab mr note create ${MR_IID} \
    #   --message "${COMMENT_BODY}" \
    #   --file "${FILE}" \
    #   --line ${LINE}
  fi
done < "${REVIEW_FILE}"
```

## 주의사항

1. **파일 경로**: 저장소 루트 기준 상대 경로 사용
2. **라인 번호**: MR의 변경된 라인에만 코멘트 가능
3. **SHA 값**: GitLab API 사용 시 정확한 commit SHA 필요
4. **권한**: `GITLAB_TOKEN`에 충분한 권한 필요 (api scope)
5. **Rate Limiting**: API 호출 횟수 제한 고려
