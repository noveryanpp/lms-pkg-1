# API Contracts

API contract definitions for LMS-PKG. This file is the source of truth for all API endpoints. Both the backend and client applications must conform to these contracts.

Before implementing any endpoint — read the relevant section here. Before consuming any endpoint from Mobile or Web — read the relevant section here. Never invent an endpoint, request field, or response field that is not documented in this file.

---

## How to Use

### For Backend (NestJS)

1. Read the contract for the endpoint you are implementing
2. Implement the controller, DTOs, and service to match the contract exactly
3. If you need to change the contract — update this file first, then implement

### For Mobile (Flutter) and Web (Next.js)

1. Read the contract for the endpoint you are consuming
2. Build your request and parse the response according to the contract
3. If the contract doesn't cover what you need — stop and flag the gap, don't invent an API

### Contract Format

Each endpoint is documented with:

- **Method + Path** — HTTP method and URL
- **Auth** — authentication and role requirements
- **Request** — body, params, or query parameters
- **Response** — success and error response shapes
- **Status Codes** — expected HTTP status codes
- **Business Rules** — relevant rules that affect behavior

---

## Response Envelope

All API responses follow this envelope:

```typescript
// Success response
{
  success: true,
  data: T,
  meta?: {
    page: number,
    limit: number,
    total: number
  }
}

// Error response
{
  success: false,
  error: {
    code: string,
    message: string,
    details?: any[]
  }
}
```

---

## Auth Endpoints

### POST /api/auth/register

Register a new user account.

**Auth:** None (public)

**Request body:**
```json
{
  "email": "string (required, valid email, unique)",
  "password": "string (required, min 8 chars)",
  "fullName": "string (required)",
  "phone": "string (optional)"
}
```

**Success response (201):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "string",
      "fullName": "string",
      "role": "PESERTA"
    },
    "accessToken": "string",
    "refreshToken": "string"
  }
}
```

**Error responses:**
- `409 CONFLICT` — email already registered

**Business rules:**
- New users are always registered with role `PESERTA`
- Instruktur and Admin accounts are created by Admin Platform only
- Password is hashed before storage

---

### POST /api/auth/login

Authenticate an existing user.

**Auth:** None (public)

**Request body:**
```json
{
  "email": "string (required)",
  "password": "string (required)"
}
```

**Success response (200):**
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "string",
      "fullName": "string",
      "role": "string"
    },
    "accessToken": "string",
    "refreshToken": "string"
  }
}
```

**Error responses:**
- `401 UNAUTHORIZED` — invalid credentials

---

### POST /api/auth/refresh

Refresh an expired access token.

**Auth:** None (public, but requires valid refresh token)

**Request body:**
```json
{
  "refreshToken": "string (required)"
}
```

**Success response (200):**
```json
{
  "success": true,
  "data": {
    "accessToken": "string",
    "refreshToken": "string"
  }
}
```

**Error responses:**
- `401 UNAUTHORIZED` — invalid or expired refresh token

---

## User Endpoints

_Contracts will be added as features are designed and planned._

---

## Course Endpoints

_Contracts will be added as features are designed and planned._

---

## Assessment Endpoints

_Contracts will be added as features are designed and planned._

---

## Certification Endpoints

_Contracts will be added as features are designed and planned._

---

## Dashboard Endpoints

_Contracts will be added as features are designed and planned._

---

## Administration Endpoints

_Contracts will be added as features are designed and planned._

---

## Contract Changelog

| Date | Feature | Change | Ref |
|---|---|---|---|
| _Entries added as contracts are created or modified_ | | | |
