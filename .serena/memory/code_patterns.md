# 코드 패턴

## 2025-12-10 - v3.0.0 Skills 강화 패턴

### 1. 복잡도 분석 패턴 (analyze-issue Phase 3D)

**적용 시점**: 코드 분석 중 복잡한 파일 발견 시

**임계값**:
| 지표 | 임계값 | 조치 |
|------|--------|------|
| Cyclomatic complexity | > 10 | 리팩토링 권장 |
| 함수 길이 | > 50줄 | Extract Method 권장 |
| 클래스 책임 | > 2개 | Extract Class 권장 |

**Sequential Thinking 활용**:
```
1. 영향받는 파일 목록 수집
2. 각 파일의 복잡도 지표 분석
3. 임계값 초과 여부 판단
4. 리팩토링 기법 선택 (Extract Method/Class)
5. 구체적 리팩토링 가이드 제공
```

### 2. 테스트 생성 패턴 (execute-plan Phase 5)

**적용 시점**: 구현 완료 후 테스트 누락 파일 발견 시

**AAA 패턴 (Arrange-Act-Assert)**:
```typescript
describe('함수명', () => {
  it('테스트 케이스 설명', async () => {
    // Arrange: 테스트 데이터 준비
    const input = { ... };
    const expectedResult = { ... };

    // Act: 함수 실행
    const result = await targetFunction(input);

    // Assert: 결과 검증
    expect(result).toEqual(expectedResult);
  });
});
```

**테스트 케이스 분류**:
| 카테고리 | 설명 | 예시 |
|----------|------|------|
| Happy path | 정상 입력, 정상 결과 | 유효한 이메일로 로그인 성공 |
| Edge cases | 경계값, 특수 상황 | 빈 문자열, 최대 길이, null |
| Error handling | 예외 상황, 에러 처리 | 네트워크 오류, 인증 실패 |

### 3. AC Traceability 패턴 (requirement-validator)

**4가지 모드**:
- Mode 1 (Reverse): 코드 → AC 역추적
- Mode 2 (Pre): 계획 → AC coverage
- Mode 3 (Post): git diff → AC 구현 확인
- Mode 4 (Final): MR → AC 최종 게이트

**통합 지점**:
- analyze-issue Phase 3E
- plan-builder Step C-2
- execute-plan Phase 6
- mr-code-review Phase 2-4
