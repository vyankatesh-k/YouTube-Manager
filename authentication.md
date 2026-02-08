# Authentication & Authorization Design – AIM 2026

## Overview

AIM 2026 uses a **secure, scalable, token-based authentication system** designed to support current requirements (email/password login) while remaining extensible for future authentication methods such as OAuth (Google, Microsoft) and mobile clients.

The authentication system follows **industry-standard best practices** and avoids manual or insecure approaches.

---

## Design Goals

The authentication system is designed to:

- Provide a simple and reliable login experience
- Ensure strong security for user credentials
- Remain stateless for scalability
- Support logout and token revocation
- Allow seamless addition of SSO providers in the future
- Avoid frontend exposure to sensitive credentials

---

## Authentication Strategy

### Chosen Approach

**JWT-based authentication with Refresh Tokens**

This approach provides:
- Stateless request authentication
- Short-lived access tokens for security
- Long-lived refresh tokens for user convenience
- Server-side control over session revocation

---

## Token Types

### 1. Access Token
- Format: JWT
- Expiry: **15 minutes**
- Purpose: Authenticate API requests
- Transport: HttpOnly cookie or Authorization header
- Validation: Signature + expiration check

### 2. Refresh Token
- Format: Random secure token
- Expiry: **7–30 days**
- Purpose: Issue new access tokens
- Storage: **Hashed and stored in database**
- Revocation: Supported via database flag

---

## Credential Handling

### Password Storage
- Passwords are **never stored in plain text**
- Passwords are hashed using:
  - `bcrypt` or `argon2`
- Salting is applied automatically by the hashing algorithm

### Password Validation
- Minimum length enforcement
- Basic complexity requirements
- Validation performed server-side

---

## Database Design

### `users` Table

Stores user identity and authentication metadata.

| Column | Type | Description |
|------|------|------------|
| id | UUID (PK) | Unique user identifier |
| email | VARCHAR (UNIQUE) | Login email |
| username | VARCHAR (UNIQUE, optional) | Public username |
| hashed_password | VARCHAR | Secure password hash |
| is_active | BOOLEAN | Account status |
| is_verified | BOOLEAN | Email verification status |
| created_at | TIMESTAMP | Account creation time |

---

### `refresh_tokens` Table

Stores refresh tokens for session management and revocation.

| Column | Type | Description |
|------|------|------------|
| id | UUID (PK) | Token identifier |
| user_id | UUID (FK) | References users.id |
| token_hash | VARCHAR | Hashed refresh token |
| expires_at | TIMESTAMP | Token expiration |
| revoked_at | TIMESTAMP (nullable) | Revocation timestamp |
| created_at | TIMESTAMP | Issued time |
| user_agent | VARCHAR (optional) | Client info |
| ip_address | VARCHAR (optional) | Request origin |

> **Note:** Only hashed refresh tokens are stored. Raw tokens are never persisted.

---

## API Contracts

### Register User

**POST `/auth/register`**

Request:
```json
{
  "email": "user@example.com",
  "password": "StrongPassword@123",
  "username": "creator_name"
}
