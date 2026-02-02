# NestJS - Quick Reference

A compact, easy-to-read guide that covers common conventions, useful commands, and reasons to choose NestJS over plain Express.

---

## HTTP status codes (recommended)
- `GET` - **200 OK** (returns resource)
- `POST` - **201 Created** (when a resource is created)
- `PUT` / `PATCH` / `DELETE` - **204 No Content** (request succeeded; no response body required)

Tip: Use **204** when the server successfully processed the request but doesn't need to return content.

---

## Responsibilities: Controller vs Service
- **Controller**
  - Handles incoming requests and outgoing responses.
  - Maps routes to service methods (no business logic here).
- **Service**
  - Implements business logic and data manipulation.

Keep controllers thin — delegate logic to services.

Example file naming conventions:
- `controllers/user.controller.ts`
- `services/user.service.ts`

---

## Constants and test data
- Keep constants (environment values, fixed strings, etc.) in separate files (e.g., `constants/*`).
- Use Faker for dummy/test data: https://fakerjs.dev/

---

## Why choose NestJS
### Against plain Express
- Requires a lot of boilerplate for structured apps.
- You often add many third-party libraries (CORS, body-parser, cookie-parser, etc.).
- Manual dependency wiring (more chance for mistakes).

### For NestJS
- Inversion of Control & Dependency Injection out of the box.
- Built-in support for testing (Jest + SuperTest examples).
- CLI generators scaffold modules, controllers, services, and wire them in modules.
- A consistent, opinionated structure helps teams scale projects.

---

## Getting started (useful commands)
Install package manager and Nest CLI (one-time):

```powershell
npm i -g pnpm
npm i -g @nestjs/cli
```

Create a new Nest project:

```powershell
nest new <project-directory>
```

Generate common artifacts (CLI helps wire modules automatically):

```powershell
nest g module <module_name>
nest g controller <controller_name>
nest g service <service_name>
# or create a resource (controller + module + service + DTOs for CRUD)
nest g resource <resource_name>
```

Notes:
- The CLI updates the appropriate `module.ts` files for you.
- Use `resource` when you want a full CRUD scaffold with DTOs.

---

## Basic structure and flow
1. A request hits a Controller route.
2. Controller calls a Service method.
3. Service contains business logic, data access, and returns results.
4. Controller formats the response and returns it to the client.

Example (pseudo-code):

```text
// controllers/todos.controller.ts
import { Controller, Get, Param } from '@nestjs/common';
import { TodosService } from '../services/todos.service';

@Controller('todos')
export class TodosController {
  constructor(private readonly todosService: TodosService) {}

  @Get(':id')
  async getOne(@Param('id') id: string) {
    return this.todosService.findOne(id); // service handles logic
  }
}

// services/todos.service.ts
export class TodosService {
  async findOne(id: string) {
    // fetch from DB, apply business rules, return entity or throw NotFound
    return { id };
  }
}
```

---

## Database integration
- Popular choice: PostgreSQL (use `@nestjs/typeorm` or `@nestjs/sequelize`, or `@nestjs/mongoose` for MongoDB).
- Example packages:
  - TypeORM: `@nestjs/typeorm` + `typeorm` + `pg`
  - Prisma: `@prisma/client` + Prisma CLI

Link: https://www.postgresql.org/

---

## Common topics to learn next
- Pipes (validation / transformation)
- Guards (authorization)
- Exception filters (centralized error handling)
- Interceptors (logging, transforming responses, caching)

---

## Small tips & conventions
- Favor DTOs for request validation and typing.
- Keep constants and configuration centralized (use `ConfigModule`).
- Use built-in decorators (`@Controller`, `@Injectable`, `@Get`, `@Post`, etc.).
- Prefer explicit HTTP status codes in responses when needed (e.g., use `@HttpCode(204)` or `res.status(204).send()`).

---

## Testing NestJS applications
Testing is a core part of NestJS developer workflow. Nest ships with Jest by default and provides good patterns for unit and integration tests.

### Common test types
- Unit tests: test services, pipes, guards, and small units in isolation (mock dependencies).
- Integration tests: spin up a test module and use SuperTest to exercise HTTP endpoints.

### Useful commands
```powershell
# Run tests once
npm test

# Run tests in watch mode
npm run test:watch

# Run e2e tests (created by Nest CLI under test/)
npm run test:e2e
```

### Example unit test (Jest)
```ts
// users.service.spec.ts
import { Test, TestingModule } from '@nestjs/testing';
import { UsersService } from './users.service';

describe('UsersService', () => {
  let service: UsersService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [UsersService],
    }).compile();

    service = module.get<UsersService>(UsersService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });
});
```

### Example e2e test (SuperTest + Nest)
```ts
// test/app.e2e-spec.ts
import { INestApplication } from '@nestjs/common';
import { Test, TestingModule } from '@nestjs/testing';
import * as request from 'supertest';
import { AppModule } from '../src/app.module';

describe('AppController (e2e)', () => {
  let app: INestApplication;

  beforeAll(async () => {
    const moduleFixture: TestingModule = await Test.createTestingModule({
      imports: [AppModule],
    }).compile();

    app = moduleFixture.createNestApplication();
    await app.init();
  });

  it('/ (GET)', () => {
    return request(app.getHttpServer()).get('/').expect(200).expect('Hello World!');
  });

  afterAll(async () => {
    await app.close();
  });
});
```

Notes
- Use an isolated test database for integration tests (set a separate DATABASE_URL in CI).
- Mock external services when possible to keep tests fast and deterministic.

---

## References / Further reading
- Official docs: https://docs.nestjs.com/
- Faker: https://fakerjs.dev/
- NestJS CLI: https://docs.nestjs.com/cli/overview