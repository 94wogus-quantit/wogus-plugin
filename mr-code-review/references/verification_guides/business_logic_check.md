# 비즈니스 로직 정확성 검증 가이드

## 목표

JIRA 목표 대비 구현이 정확한지 검증합니다. 잘못된 로직, 엣지케이스 누락, 경계값 처리 오류 등을 탐지합니다.

## Sequential Thinking MCP 예시

### 예시 1: AC 조건 정확성 분석

```typescript
await mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "AC#1 '5회 실패 시 계정 잠금' 분석: 현재 코드에서 실패 횟수를 어떻게 카운트하는가? 5회 정확히 체크하는가? (>= 5 vs == 5) 리셋 조건은 무엇인가?",
  thoughtNumber: 1,
  totalThoughts: 6,
  nextThoughtNeeded: true
})
```

### 예시 2: 엣지케이스 검토

```typescript
await mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "엣지케이스 검토: 동시 로그인 시도 시 race condition이 있는가? 4회 실패 후 성공 시 카운트가 리셋되는가? 잠금 해제 조건이 명시되어 있는가?",
  thoughtNumber: 2,
  totalThoughts: 6,
  nextThoughtNeeded: true
})
```

### 예시 3: 부정 케이스 검증

```typescript
await mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "AC에 '~하면 안 된다'는 조건이 있는가? 예: '잠금된 계정은 로그인 시도가 불가해야 한다'. 이 부정 케이스가 코드에 구현되었는가?",
  thoughtNumber: 3,
  totalThoughts: 6,
  nextThoughtNeeded: true
})
```

### 예시 4: 데이터 정합성 검증

```typescript
await mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "계산 로직 검증: 할인율 계산이 정확한가? 소수점 처리는 어떻게 하는가? 집계 값이 개별 항목의 합과 일치하는가?",
  thoughtNumber: 4,
  totalThoughts: 6,
  nextThoughtNeeded: true
})
```

### 예시 5: 상태 전이 검증

```typescript
await mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "상태 전이 검증: 주문 상태가 PENDING → CONFIRMED → SHIPPED → DELIVERED 순서로만 변경 가능한가? 잘못된 상태 전이(예: PENDING → DELIVERED)를 방지하는가?",
  thoughtNumber: 5,
  totalThoughts: 6,
  nextThoughtNeeded: true
})
```

### 예시 6: 타임아웃/만료 조건 검증

```typescript
await mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking({
  thought: "시간 관련 조건 검증: AC에 '30분 후 자동 해제'가 있다면, 타이머/스케줄러가 구현되었는가? 시간대(timezone) 처리가 올바른가?",
  thoughtNumber: 6,
  totalThoughts: 6,
  nextThoughtNeeded: false
})
```

## Serena MCP 예시

### 예시 1: 관련 비즈니스 로직 검색

```typescript
await mcp__plugin_workflow-skills_serena__search_for_pattern({
  substring_pattern: "lockAccount|unlockAccount|failedAttempts",
  paths_include_glob: "**/*.ts"
})
```

### 예시 2: 기존 유사 로직 비교

```typescript
await mcp__plugin_workflow-skills_serena__find_symbol({
  name_path: "AuthService.login",
  relative_path: "src/services/auth.service.ts"
})
```

### 예시 3: 상태 관리 패턴 확인

```typescript
await mcp__plugin_workflow-skills_serena__search_for_pattern({
  substring_pattern: "status.*=.*|setState|updateState",
  paths_include_glob: "**/services/**/*.ts"
})
```

## 검증 체크리스트

| 검증 항목 | 설명 | 확인 포인트 |
|----------|------|------------|
| **로직 정확성** | AC에 명시된 조건이 코드에 정확히 구현되었는가? | 조건문, 비교 연산자, 임계값 |
| **경계값 처리** | 경계값(5회 = 정확히 5? 5 이상?) 처리가 올바른가? | `>=`, `>`, `==` 연산자 확인 |
| **엣지케이스** | 예외 상황(동시성, 타임아웃, null)이 고려되었는가? | null check, race condition, timeout |
| **부정 케이스** | "~하면 안 된다" 조건이 구현되었는가? | 유효성 검사, 가드 절 |
| **데이터 정합성** | 계산, 집계, 상태 변경이 정확한가? | 소수점, 반올림, 합계 검증 |
| **상태 전이** | 상태 변경 순서가 올바른가? | 상태 머신, 전이 조건 |
| **시간 조건** | 만료, 타임아웃, 스케줄 조건이 구현되었는가? | 타이머, cron, TTL |

## 이슈 발견 시 JSON 형식

```json
{
  "file": "src/services/auth.ts",
  "line": 45,
  "severity": "🔴 Critical",
  "title": "잠금 해제 조건 누락",
  "description": "AC에 따르면 30분 후 자동 해제되어야 하나, 해제 로직이 구현되지 않았습니다. 현재 코드는 계정을 잠그기만 하고 해제하지 않습니다.",
  "current_code": "if (failCount >= 5) {\n  lockAccount(userId);\n}",
  "suggested_code": "if (failCount >= 5) {\n  lockAccount(userId, { unlockAfter: 30 * 60 * 1000 });\n  scheduleUnlock(userId, 30 * 60 * 1000);\n}",
  "reason": "AC#1에 명시된 '30분 후 자동 해제' 요구사항을 충족하기 위해 타이머 또는 스케줄러 구현이 필요합니다."
}
```

## 일반적인 비즈니스 로직 오류 패턴

### 1. 경계값 오류 (Off-by-one)

```typescript
// 잘못된 예: 5회 초과에서 잠금 (6회부터)
if (failCount > 5) { lockAccount(); }

// 올바른 예: 5회 이상에서 잠금 (5회부터)
if (failCount >= 5) { lockAccount(); }
```

### 2. 상태 리셋 누락

```typescript
// 잘못된 예: 성공 시 실패 횟수 리셋 안 함
if (loginSuccess) {
  return { success: true };
}

// 올바른 예: 성공 시 실패 횟수 리셋
if (loginSuccess) {
  resetFailCount(userId);
  return { success: true };
}
```

### 3. 동시성 미고려

```typescript
// 잘못된 예: race condition 가능
const count = await getFailCount(userId);
await setFailCount(userId, count + 1);

// 올바른 예: atomic 연산 사용
await incrementFailCount(userId);
```

### 4. null/undefined 미처리

```typescript
// 잘못된 예: null 체크 없음
const discount = user.membership.discountRate * price;

// 올바른 예: optional chaining + default
const discount = (user?.membership?.discountRate ?? 0) * price;
```

### 5. 시간대 미고려

```typescript
// 잘못된 예: 로컬 시간 사용
const expiresAt = new Date();
expiresAt.setMinutes(expiresAt.getMinutes() + 30);

// 올바른 예: UTC 사용
const expiresAt = new Date(Date.now() + 30 * 60 * 1000);
```

## Best Practices

1. **AC를 코드로 번역하기 전에 충분히 분석**
   - 각 AC의 조건, 액션, 예외를 명확히 파악
   - 암묵적 요구사항 (예: 리셋 조건) 확인

2. **경계값 테스트 케이스 확인**
   - 4회, 5회, 6회 각각 테스트
   - 0, 음수, null 케이스 테스트

3. **상태 다이어그램으로 검증**
   - 가능한 모든 상태 전이 나열
   - 잘못된 전이 방지 로직 확인

4. **시간 관련 로직 특별 주의**
   - 타임존, DST(일광 절약 시간) 고려
   - 만료/갱신 로직 정확성 확인
