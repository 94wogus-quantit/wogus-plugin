# 아키텍처 일관성 검증 가이드

## 목표

변경된 코드가 프로젝트의 아키텍처 원칙과 레이어 분리 규칙을 준수하는지 검증합니다.

## Sequential Thinking MCP 예시

### 예시 1: 레이어 분리 원칙 검증

```typescript
await mcp__sequential-thinking__sequentialthinking({
  thought: "변경된 코드가 기존 아키텍처 레이어 분리 원칙을 준수하는가? Controller → Service → Repository 패턴을 따르는가?",
  thoughtNumber: 1,
  totalThoughts: 7,
  nextThoughtNeeded: true
})
```

### 예시 2: 의존성 방향 검증

```typescript
await mcp__sequential-thinking__sequentialthinking({
  thought: "의존성 방향이 올바른가? 고수준 모듈이 저수준 모듈에 의존하지 않는가? Domain 레이어가 Infrastructure 레이어를 직접 참조하지 않는가?",
  thoughtNumber: 2,
  totalThoughts: 7,
  nextThoughtNeeded: true
})
```

### 예시 3: 디렉토리 구조 규칙 검증

```typescript
await mcp__sequential-thinking__sequentialthinking({
  thought: "새로 추가된 파일이 프로젝트의 디렉토리 구조 규칙을 따르는가? API 엔드포인트는 src/api/에, 비즈니스 로직은 src/services/에 위치하는가?",
  thoughtNumber: 3,
  totalThoughts: 7,
  nextThoughtNeeded: true
})
```

### 예시 4: 아키텍처 패턴 일관성 검증

```typescript
await mcp__sequential-thinking__sequentialthinking({
  thought: "프로젝트가 Clean Architecture/Hexagonal Architecture를 사용한다면, 새 코드가 해당 패턴의 경계를 준수하는가? Port와 Adapter가 명확히 분리되었는가?",
  thoughtNumber: 4,
  totalThoughts: 7,
  nextThoughtNeeded: true
})
```

## Serena MCP 예시

### 예시 1: 프로젝트 아키텍처 컨텍스트 조회

```typescript
await mcp__serena__get_project_context({
  query: "프로젝트 레이어 아키텍처 및 디렉토리 구조 규칙",
  depth: 7
})
```

### 예시 2: 아키텍처 결정사항 메모리 읽기

```typescript
await mcp__serena__read_memory({
  memory_file_name: "architecture_decisions.md"
})
```

### 예시 3: 변경된 파일의 심볼 및 의존성 추적

```typescript
await mcp__serena__find_referencing_symbols({
  symbol_name: "UserService" // 새로 추가된 클래스/함수명
})
```

### 예시 4: 기존 패턴 검색

```typescript
await mcp__serena__search_for_pattern({
  pattern: "service layer patterns",
  file_mask: "*.service.ts"
})
```

## 검증 항목 체크리스트

### 디렉토리 구조 규칙

- [ ] API 엔드포인트가 올바른 디렉토리(`src/api/`, `src/routes/`)에 위치
- [ ] 비즈니스 로직이 서비스 레이어(`src/services/`, `src/domain/`)에 구현
- [ ] 데이터 접근 로직이 Repository/DAO 레이어에 분리
- [ ] 유틸리티 함수가 `src/utils/`에 위치
- [ ] 타입 정의가 `src/types/`에 위치

### 레이어 분리 원칙

- [ ] Controller는 HTTP 요청/응답 처리만 담당 (비즈니스 로직 없음)
- [ ] Service는 비즈니스 로직만 담당 (데이터베이스 접근 없음)
- [ ] Repository는 데이터 접근만 담당 (비즈니스 로직 없음)
- [ ] 각 레이어가 명확히 구분되고 책임이 분리됨

### 의존성 방향 규칙

- [ ] 저수준 모듈이 고수준 모듈에 의존 (역방향 의존성 없음)
- [ ] Domain 레이어가 Infrastructure 레이어를 직접 참조하지 않음
- [ ] 순환 의존성이 없음
- [ ] 인터페이스를 통한 의존성 역전 원칙 준수

### 기존 패턴과의 일관성

- [ ] 동일한 유형의 기능이 동일한 패턴으로 구현됨
- [ ] 네이밍 규칙이 기존 코드와 일관됨 (예: `UserController`, `ProductService`)
- [ ] 에러 핸들링 방식이 기존 코드와 일관됨
- [ ] DI(Dependency Injection) 방식이 기존 코드와 일관됨

### 아키텍처 문서 준수

- [ ] CLAUDE.md의 아키텍처 가이드라인 준수
- [ ] README.md의 프로젝트 구조 설명과 일치
- [ ] ADR(Architecture Decision Record)의 결정사항 준수

## TypeScript 코드 예시

### 올바른 레이어 분리 예시

```typescript
// ✅ GOOD: Controller는 HTTP만 처리
// src/api/user.controller.ts
export class UserController {
  constructor(private userService: UserService) {}

  async createUser(req: Request, res: Response) {
    const userData = req.body;
    const user = await this.userService.createUser(userData);
    res.status(201).json(user);
  }
}

// ✅ GOOD: Service는 비즈니스 로직만 처리
// src/services/user.service.ts
export class UserService {
  constructor(private userRepository: UserRepository) {}

  async createUser(userData: CreateUserDto): Promise<User> {
    // 비즈니스 로직: 이메일 중복 확인, 비밀번호 해싱 등
    await this.validateEmail(userData.email);
    const hashedPassword = await this.hashPassword(userData.password);

    return this.userRepository.save({
      ...userData,
      password: hashedPassword
    });
  }
}

// ✅ GOOD: Repository는 데이터 접근만 처리
// src/repositories/user.repository.ts
export class UserRepository {
  async save(user: User): Promise<User> {
    // 데이터베이스 접근 로직만
    return await this.db.users.create(user);
  }
}
```

### 잘못된 레이어 분리 예시

```typescript
// ❌ BAD: Controller에 비즈니스 로직이 있음
// src/api/user.controller.ts
export class UserController {
  async createUser(req: Request, res: Response) {
    const userData = req.body;

    // ❌ 비즈니스 로직이 Controller에 있으면 안됨
    if (await this.db.users.findByEmail(userData.email)) {
      throw new Error('Email already exists');
    }

    // ❌ 데이터베이스 접근이 Controller에 있으면 안됨
    const user = await this.db.users.create(userData);
    res.status(201).json(user);
  }
}

// ❌ BAD: Service가 데이터베이스에 직접 접근
// src/services/user.service.ts
export class UserService {
  async createUser(userData: CreateUserDto): Promise<User> {
    // ❌ Service가 DB에 직접 접근하면 안됨
    return await this.db.users.create(userData);
  }
}
```

### 의존성 방향 올바른 예시

```typescript
// ✅ GOOD: 의존성 역전 원칙 (Dependency Inversion Principle) 준수
// src/domain/user.service.ts
export class UserService {
  // 인터페이스에 의존 (구체적인 구현체가 아님)
  constructor(private userRepository: IUserRepository) {}

  async findUser(id: string): Promise<User> {
    return this.userRepository.findById(id);
  }
}

// src/domain/interfaces/user-repository.interface.ts
export interface IUserRepository {
  findById(id: string): Promise<User>;
  save(user: User): Promise<User>;
}

// src/infrastructure/user.repository.ts
export class UserRepository implements IUserRepository {
  // 구체적인 구현
  async findById(id: string): Promise<User> {
    return await this.db.users.findOne({ id });
  }

  async save(user: User): Promise<User> {
    return await this.db.users.create(user);
  }
}
```

## 실제 검증 절차

1. **Serena Context7으로 프로젝트 아키텍처 이해**
   - `mcp__serena__get_project_context()`로 전체 구조 파악
   - `mcp__serena__read_memory()`로 아키텍처 결정사항 확인

2. **Sequential Thinking으로 각 레이어 검증**
   - 각 변경 파일에 대해 레이어 분리 원칙 체계적 검증
   - 의존성 방향이 올바른지 단계별 확인

3. **Serena로 기존 패턴과 비교**
   - `mcp__serena__search_for_pattern()`으로 유사 코드 패턴 찾기
   - 새 코드가 기존 패턴과 일관되는지 비교

4. **검증 결과 문서화**
   - 위반 사항 발견 시 파일명:라인번호 형식으로 기록
   - 권장 수정 방향 제시
