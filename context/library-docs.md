# Library Docs

Project-specific usage patterns for third party libraries in this project. This file only covers how we use each library in this specific project — rules, patterns, and constraints specific to LMS-PKG.

Read the relevant section before implementing any feature that touches these libraries.

---

## Before Using Any Library

Before implementing any feature that uses a third party library:

1. **Check AGENTS.md** at the project root — it lists every skill installed for this project and how to use them. Skills contain up-to-date API documentation, usage patterns, and best practices specific to this codebase.

2. **Check if an MCP server is configured** for that library. Some tools have MCP servers that give the AI agent direct access to documentation, logs, and debugging tools. If an MCP server is available — use it before falling back to general knowledge.

3. **Read this file** for project-specific patterns that override general library knowledge.

The order of authority is:

```
MCP server (real-time docs) → Skills via AGENTS.md → This file (project rules) → General training knowledge
```

Never rely on general training knowledge alone for library APIs — they change frequently and training data may be outdated.

---

## Prisma

**Check first:** Check AGENTS.md for an installed Prisma skill. If a Prisma MCP server is configured — use it.

### Schema Location

```
apps/api/prisma/schema.prisma
```

### Client Usage

```typescript
// apps/api/src/prisma/prisma.service.ts
import { Injectable, OnModuleInit, OnModuleDestroy } from '@nestjs/common';
import { PrismaClient } from '@prisma/client';

@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit, OnModuleDestroy {
  async onModuleInit() {
    await this.$connect();
  }

  async onModuleDestroy() {
    await this.$disconnect();
  }
}
```

### Query Patterns

```typescript
// Read with pagination
const [data, total] = await Promise.all([
  this.prisma.course.findMany({
    where: { status: CourseStatus.PUBLISHED },
    skip: (page - 1) * limit,
    take: limit,
    orderBy: { createdAt: 'desc' },
    include: { instructor: { select: { id: true, fullName: true } } },
  }),
  this.prisma.course.count({ where: { status: CourseStatus.PUBLISHED } }),
]);

// Create
const course = await this.prisma.course.create({
  data: {
    title: dto.title,
    description: dto.description,
    instructorId: userId,
    status: CourseStatus.DRAFT,
  },
});

// Update
const course = await this.prisma.course.update({
  where: { id: courseId },
  data: { title: dto.title },
});

// Transaction
const [enrollment, _] = await this.prisma.$transaction([
  this.prisma.enrollment.create({ data: { userId, courseId } }),
  this.prisma.course.update({
    where: { id: courseId },
    data: { enrollmentCount: { increment: 1 } },
  }),
]);
```

### Migration Commands

```bash
# Create a new migration
npx prisma migrate dev --name add_enrollment_table

# Apply migrations (production)
npx prisma migrate deploy

# Reset database (development only)
npx prisma migrate reset

# Generate Prisma Client after schema change
npx prisma generate
```

**Rules:**

- PrismaService is a NestJS injectable — always inject, never instantiate directly
- Always use transactions for multi-table writes that must be atomic
- Always handle Prisma errors — especially unique constraint violations
- Use `include` sparingly — only include relations when needed
- Use `select` to limit returned fields when full objects are not needed
- Never use raw SQL unless Prisma cannot express the query
- Every schema change requires a migration with a descriptive name

---

## NestJS Authentication

**Check first:** Check AGENTS.md for NestJS or Passport skills.

### JWT Strategy

```typescript
// apps/api/src/modules/auth/strategies/jwt.strategy.ts
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { PassportStrategy } from '@nestjs/passport';
import { ExtractJwt, Strategy } from 'passport-jwt';

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(private readonly configService: ConfigService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthBearerToken(),
      secretOrKey: configService.get<string>('JWT_SECRET'),
    });
  }

  async validate(payload: JwtPayload) {
    // payload contains: sub, email, role
    return { id: payload.sub, email: payload.email, role: payload.role };
  }
}
```

### Auth Guard

```typescript
// Applied globally or per controller
@UseGuards(AuthGuard('jwt'))
```

### Roles Guard

```typescript
// Custom decorator
export const Roles = (...roles: Role[]) => SetMetadata('roles', roles);

// Guard
@Injectable()
export class RolesGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.get<Role[]>('roles', context.getHandler());
    if (!requiredRoles) return true;
    const { user } = context.switchToHttp().getRequest();
    return requiredRoles.includes(user.role);
  }
}

// Usage
@Post()
@Roles(Role.INSTRUKTUR, Role.ADMIN_PLATFORM)
async createCourse(@Body() dto: CreateCourseDto) { ... }
```

**Rules:**

- AuthGuard is applied globally or at controller level — never skip authentication on protected routes
- RolesGuard checks role metadata — always use @Roles() decorator for role-restricted endpoints
- JWT payload must only contain: sub (user ID), email, role — never include sensitive data
- Token generation and validation use the same secret from environment variables

---

## class-validator

**Check first:** Check AGENTS.md for class-validator documentation.

### DTO Validation

```typescript
import {
  IsString,
  IsNotEmpty,
  IsEmail,
  MinLength,
  IsOptional,
  IsEnum,
  IsInt,
  Min,
  Max,
  IsUUID,
} from 'class-validator';

export class CreateCourseDto {
  @IsString()
  @IsNotEmpty()
  @MinLength(3)
  title: string;

  @IsString()
  @IsOptional()
  description?: string;
}

export class PaginationDto {
  @IsInt()
  @Min(1)
  @IsOptional()
  page?: number = 1;

  @IsInt()
  @Min(1)
  @Max(100)
  @IsOptional()
  limit?: number = 20;
}
```

### Global Validation Pipe

```typescript
// main.ts
app.useGlobalPipes(new ValidationPipe({
  whitelist: true,           // strip non-decorated properties
  forbidNonWhitelisted: true, // throw on extra properties
  transform: true,           // auto-transform payloads to DTO instances
}));
```

**Rules:**

- Every request body must have a corresponding DTO with validation decorators
- `whitelist: true` — never accept fields not defined in the DTO
- `forbidNonWhitelisted: true` — reject requests with extra fields
- `transform: true` — automatically transform plain objects to class instances
- Always use specific validators — never rely on generic `@IsNotEmpty()` alone

---

## bcrypt

### Password Hashing

```typescript
import * as bcrypt from 'bcrypt';

const SALT_ROUNDS = 10;

// Hash password before saving
const hashedPassword = await bcrypt.hash(plainPassword, SALT_ROUNDS);

// Verify password during login
const isValid = await bcrypt.compare(plainPassword, hashedPassword);
```

**Rules:**

- Salt rounds: always 10 — never lower
- Never log or return plain passwords
- Never store plain passwords in the database
- Always use `bcrypt.compare()` — never compare hashes directly

---

## S3 Storage (AWS SDK)

**Check first:** Check AGENTS.md for AWS or S3 skills.

### Client Setup

```typescript
import { S3Client, PutObjectCommand, GetObjectCommand } from '@aws-sdk/client-s3';
import { getSignedUrl } from '@aws-sdk/s3-request-presigner';

const s3 = new S3Client({
  endpoint: process.env.S3_ENDPOINT,
  region: 'auto',
  credentials: {
    accessKeyId: process.env.S3_ACCESS_KEY!,
    secretAccessKey: process.env.S3_SECRET_KEY!,
  },
  forcePathStyle: true, // needed for MinIO and most S3-compatible services
});
```

### File Upload

```typescript
await s3.send(new PutObjectCommand({
  Bucket: process.env.S3_BUCKET!,
  Key: `courses/${courseId}/materials/${materialId}/${filename}`,
  Body: buffer,
  ContentType: mimeType,
}));
```

### Pre-signed URL

```typescript
const url = await getSignedUrl(s3, new GetObjectCommand({
  Bucket: process.env.S3_BUCKET!,
  Key: filePath,
}), { expiresIn: 3600 }); // 1 hour
```

**Rules:**

- All file uploads go through the NestJS API — clients never access S3 directly
- File paths follow the conventions defined in architecture.md
- Always use `forcePathStyle: true` for S3-compatible services
- Pre-signed URLs expire — use appropriate expiry (1 hour for downloads)
- Never store S3 credentials in client applications

---

## Next.js (Dashboard)

**Check first:** Check AGENTS.md for Next.js skills.

### App Router

- App Router only — no Pages Router
- All components are Server Components by default
- Only add `'use client'` when the component requires state, effects, or browser APIs
- Data fetching happens through the API client — never direct database access
- Protected routes enforced via middleware or layout

### API Client

```typescript
// lib/api-client.ts
const API_BASE = process.env.NEXT_PUBLIC_API_URL;

async function apiFetch<T>(path: string, options?: RequestInit): Promise<ApiResponse<T>> {
  const token = getAccessToken(); // from cookies or storage
  const response = await fetch(`${API_BASE}${path}`, {
    ...options,
    headers: {
      'Content-Type': 'application/json',
      ...(token ? { Authorization: `Bearer ${token}` } : {}),
      ...options?.headers,
    },
  });

  if (response.status === 401) {
    // attempt token refresh
    // retry original request
  }

  return response.json();
}
```

**Rules:**

- Dashboard consumes the NestJS API — no direct database access
- All data flows through the API client
- Token refresh is handled automatically by the API client
- Never implement business logic in the dashboard — it's a presentation layer

---

## Flutter / Dart

**Check first:** Check AGENTS.md for Flutter skills.

### HTTP Client

```dart
// lib/core/api_client.dart
import 'package:dio/dio.dart';

class ApiClient {
  final Dio _dio;

  ApiClient({required String baseUrl}) : _dio = Dio(BaseOptions(
    baseUrl: baseUrl,
    contentType: 'application/json',
  )) {
    _dio.interceptors.add(AuthInterceptor()); // handles JWT attachment + refresh
  }

  Future<ApiResponse<T>> get<T>(String path, {Map<String, dynamic>? queryParams}) async {
    final response = await _dio.get(path, queryParameters: queryParams);
    return ApiResponse.fromJson(response.data);
  }

  Future<ApiResponse<T>> post<T>(String path, {dynamic body}) async {
    final response = await _dio.post(path, data: body);
    return ApiResponse.fromJson(response.data);
  }
}
```

### Secure Token Storage

```dart
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

class TokenStorage {
  final _storage = const FlutterSecureStorage();

  Future<void> saveTokens(String accessToken, String refreshToken) async {
    await _storage.write(key: 'access_token', value: accessToken);
    await _storage.write(key: 'refresh_token', value: refreshToken);
  }

  Future<String?> getAccessToken() => _storage.read(key: 'access_token');
  Future<String?> getRefreshToken() => _storage.read(key: 'refresh_token');
  Future<void> clearTokens() => _storage.deleteAll();
}
```

**Rules:**

- Mobile app consumes the NestJS API — no direct database access
- Tokens stored securely using `flutter_secure_storage` — never in shared preferences
- API client handles token refresh automatically via Dio interceptors
- Models match API response contracts exactly — do not invent fields
- Never implement business logic in the mobile app — it's a presentation layer
- State management approach: TBD (to be decided and recorded in decisions.md)
