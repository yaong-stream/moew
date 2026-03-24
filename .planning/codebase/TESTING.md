# Testing Patterns

**Analysis Date:** 2026-03-24

## Test Framework

**Runner:**
- Bun's built-in test runner (`bun test`) — no separate jest/vitest config file
- Config: none (uses Bun defaults, spec files discovered by `**/*.spec.ts` and `**/*.e2e.spec.ts` patterns)
- `@nestjs/testing` for NestJS module test utilities

**Assertion Library:**
- Jest-compatible `expect` API provided by Bun's test runner

**HTTP Testing (E2E):**
- `supertest` ^7.0.0 for HTTP integration assertions against the live NestJS app

**Run Commands:**
```bash
# From apps/api
bun test                  # Run all unit tests (*.spec.ts)
bun test --watch          # Watch mode
bun test --coverage       # Coverage report
bun test ./test/          # Run E2E tests only
```

**From monorepo root, test scripts are per-app** — no root-level test script exists.

## Test File Organization

**Location:**
- Unit tests: co-located alongside source files in `apps/api/src/`
  - Example: `apps/api/src/app.controller.spec.ts` sits next to `apps/api/src/app.controller.ts`
- E2E tests: in a dedicated top-level `test/` directory under the app
  - Example: `apps/api/test/app.e2e.spec.ts`

**Naming:**
- Unit test files: `<name>.spec.ts`
- E2E test files: `<name>.e2e.spec.ts`

**Structure:**
```
apps/api/
├── src/
│   ├── app.controller.ts
│   ├── app.controller.spec.ts    ← unit test co-located
│   ├── app.service.ts
│   ├── app.module.ts
│   └── main.ts
└── test/
    └── app.e2e.spec.ts           ← e2e tests isolated
```

**Web app (`apps/web`) has no test files** — no testing framework is installed in that package.

## Test Structure

**Unit Test Suite Organization:**
```typescript
// apps/api/src/app.controller.spec.ts
import { Test, type TestingModule } from "@nestjs/testing";
import { AppController } from "./app.controller";
import { AppService } from "./app.service";

describe("AppController", () => {
  let appController: AppController;

  beforeEach(async () => {
    const app: TestingModule = await Test.createTestingModule({
      controllers: [AppController],
      providers: [AppService],
    }).compile();

    appController = app.get<AppController>(AppController);
  });

  describe("root", () => {
    it('should return "Hello World!"', () => {
      expect(appController.getHello()).toBe("Hello World!");
    });
  });
});
```

**E2E Test Suite Organization:**
```typescript
// apps/api/test/app.e2e.spec.ts
import type { INestApplication } from "@nestjs/common";
import { Test, type TestingModule } from "@nestjs/testing";
import request from "supertest";
import type { App } from "supertest/types";
import { AppModule } from "./../src/app.module";

describe("AppController (e2e)", () => {
  let app: INestApplication<App>;

  beforeEach(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    await app.init();
  });

  afterEach(async () => {
    await app.close();
  });

  it("/ (GET)", () => {
    return request(app.getHttpServer())
      .get("/")
      .expect(200)
      .expect("Hello World!");
  });
});
```

**Patterns:**
- Setup via `beforeEach` — fresh module/app instance per test
- Teardown via `afterEach` for E2E: `await app.close()` to release server resources
- No `afterAll` observed; prefer `afterEach` for cleanup
- `describe` blocks nest logically: outer describes the class, inner describes the method/route

## Mocking

**Framework:** `@nestjs/testing` `Test.createTestingModule()` is the primary mechanism

**Unit test approach — real dependencies:**
- Current unit tests provide real providers (not mocks) when the dependency is simple:
  ```typescript
  const app: TestingModule = await Test.createTestingModule({
    controllers: [AppController],
    providers: [AppService],   // real AppService, not a mock
  }).compile();
  ```

**When to mock:**
- Replace real providers with mock objects when the dependency has side effects (DB calls, HTTP calls, file I/O)
- Use `{ provide: ServiceClass, useValue: mockObject }` in the `providers` array:
  ```typescript
  const mockService = { getHello: () => "mocked" };
  const app: TestingModule = await Test.createTestingModule({
    controllers: [AppController],
    providers: [{ provide: AppService, useValue: mockService }],
  }).compile();
  ```

**What NOT to mock:**
- Pure value-returning functions with no side effects
- Simple DTOs and data models
- Entire modules in unit tests (use real module only in E2E)

## Fixtures and Factories

**Test Data:**
- No fixture files or factory utilities detected at current scaffold stage
- Inline values used directly in assertions: `"Hello World!"`

**Location:**
- When added, place shared fixtures/factories under `apps/api/test/fixtures/` or `apps/api/test/factories/`

## Coverage

**Requirements:** No minimum enforced — no coverage threshold configuration detected

**View Coverage:**
```bash
bun test --coverage
```

## Test Types

**Unit Tests (`*.spec.ts`):**
- Scope: individual controller or service in isolation
- Approach: create a minimal `TestingModule` with only the class under test and its direct providers
- Location: co-located with source in `apps/api/src/`

**E2E Tests (`*.e2e.spec.ts`):**
- Scope: full HTTP request/response cycle through the entire `AppModule`
- Approach: bootstrap a real `INestApplication`, send requests via `supertest`, assert HTTP status and body
- Teardown: `app.close()` in `afterEach`
- Location: `apps/api/test/`

**Frontend Tests:**
- Not applicable — `apps/web` has no test framework installed

## Common Patterns

**Async Testing:**
```typescript
// beforeEach must be async when compiling modules
beforeEach(async () => {
  const app: TestingModule = await Test.createTestingModule({ ... }).compile();
  appController = app.get<AppController>(AppController);
});

// E2E: return the supertest promise to let Bun await it
it("/ (GET)", () => {
  return request(app.getHttpServer()).get("/").expect(200);
});
```

**Instance retrieval from TestingModule:**
```typescript
// Use generic type parameter for type-safe retrieval
appController = app.get<AppController>(AppController);
```

**App lifecycle in E2E:**
```typescript
app = moduleFixture.createNestApplication();
await app.init();      // must init before sending requests
// ... tests ...
await app.close();     // must close to release port binding
```

**Error Testing:**
- No error path tests present at scaffold level
- Follow NestJS pattern: throw `HttpException` subclasses in services/controllers and assert HTTP status codes in E2E tests

---

*Testing analysis: 2026-03-24*
