---
name: nestjs-enterprise-pro
description: Enterprise backend architecture with NestJS, TypeScript, modular patterns, microservices (gRPC/RabbitMQ), guards, pipes, interceptors, Prisma/TypeORM, and test automation.
metadata:
  model: inherit
---

## Use this skill when

- Designing or refactoring enterprise backend services with NestJS and TypeScript.
- Structuring modular architectures (Core, Shared, Feature modules, Dynamic Config modules).
- Implementing authentication, authorization guards (JWT, OAuth2, RBAC, CASL/ABAC).
- Configuring global pipes (ValidationPipe with class-validator), interceptors, and exception filters.
- Orchestrating microservices via gRPC, RabbitMQ, Kafka, or Redis transport.
- Managing database persistence with Prisma or TypeORM with transactional integrity.
- Writing unit, integration, and E2E tests using `@nestjs/testing` and Supertest.

## Do not use this skill when

- Building simple lightweight scripts or serverless single-file lambdas where NestJS overhead is unnecessary.
- The project is pure Python, Go, or .NET without Node.js runtime.

## Instructions

- Enforce strict separation between Controllers (HTTP transport), Services (business logic), and Repositories (data access).
- Always use `ValidationPipe` with `whitelist: true` and `forbidNonWhitelisted: true` to prevent mass-assignment vulnerabilities.
- Implement domain errors and custom exception filters instead of throwing raw database errors.

---

## 1. Enterprise Modular Structure

Keep concerns separated with clear module boundaries:

```
src/
├── common/                  # Cross-cutting concerns
│   ├── decorators/          # @CurrentUser(), @Public(), @Roles()
│   ├── filters/             # AllExceptionsFilter
│   ├── guards/              # JwtAuthGuard, RolesGuard
│   ├── interceptors/        # LoggingInterceptor, TransformInterceptor
│   └── pipes/               # ValidationPipe config
├── config/                  # Validated environment configurations
├── core/                    # Singleton services loaded once (PrismaModule, LoggerModule)
├── modules/                 # Domain/Feature modules
│   ├── auth/
│   │   ├── dto/
│   │   ├── guards/
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   └── auth.module.ts
│   └── orders/
└── main.ts                  # Bootstrap entry point
```

---

## 2. Robust Bootstrap & Global Configuration

Configure security headers, CORS, validation, and filters at bootstrap:

```typescript
// src/main.ts
import { NestFactory } from '@nestjs/core'
import { ValidationPipe, VersioningType } from '@nestjs/common'
import { AppModule } from './app.module'
import helmet from 'helmet'

async function bootstrap() {
  const app = await NestFactory.create(AppModule)

  // Security Headers
  app.use(helmet())
  app.enableCors({ origin: process.env.ALLOWED_ORIGINS?.split(',') || false })

  // API Versioning
  app.enableVersioning({
    type: VersioningType.URI,
    defaultVersion: '1',
  })

  // Global Validation
  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      forbidNonWhitelisted: true,
      transform: true,
      transformOptions: { enableImplicitConversion: true },
    })
  )

  await app.listen(process.env.PORT ?? 3000)
}
bootstrap()
```

---

## 3. Custom Decorators & RBAC Guards

Implement role-based access control without controller boilerplate:

```typescript
// src/common/decorators/roles.decorator.ts
import { SetMetadata } from '@nestjs/common'
export enum Role { USER = 'USER', ADMIN = 'ADMIN' }
export const ROLES_KEY = 'roles'
export const Roles = (...roles: Role[]) => SetMetadata(ROLES_KEY, roles)

// src/common/guards/roles.guard.ts
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common'
import { Reflector } from '@nestjs/core'
import { ROLES_KEY, Role } from '../decorators/roles.decorator'

@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.getAllAndOverride<Role[]>(ROLES_KEY, [
      context.getHandler(),
      context.getClass(),
    ])
    if (!requiredRoles) return true

    const { user } = context.switchToHttp().getRequest()
    return requiredRoles.some((role) => user?.roles?.includes(role))
  }
}
```

---

## 4. Microservices Transport with Hybrid Application

NestJS enables listening on HTTP and message queues simultaneously:

```typescript
// Hybrid setup in main.ts
const app = await NestFactory.create(AppModule)

// Attach RabbitMQ transport
app.connectMicroservice({
  transport: Transport.RMQ,
  options: {
    urls: [process.env.RABBITMQ_URL],
    queue: 'orders_queue',
    queueOptions: { durable: true },
  },
})

await app.startAllMicroservices()
await app.listen(3000)
```

---

## 5. Testing Best Practices

```typescript
describe('OrdersService', () => {
  let service: OrdersService
  let prismaMock: DeepMockProxy<PrismaService>

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        OrdersService,
        { provide: PrismaService, useValue: mockDeep<PrismaService>() },
      ],
    }).compile()

    service = module.get<OrdersService>(OrdersService)
    prismaMock = module.get(PrismaService)
  })

  it('should calculate order total with discounts applied', async () => {
    // Isolated unit test without real database I/O
  })
})
```

---

## 6. Anti-Patterns to Avoid

- **Circular Module Dependencies**: Use `forwardRef()` only as an absolute last resort; redesign module boundaries or use events instead.
- **Leaking Database Entities in Controllers**: Always transform or map outputs to explicit Response DTOs.
- **Synchronous Blocking Code in Event Handlers**: Always handle queue events asynchronously with idempotency keys.
