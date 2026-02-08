Perfect 👍
Below is a **clean, formal, senior-engineer-grade authentication design document** written **exactly** the way it should live inside your GitHub repo as Markdown.

You can save this as:

```
docs/authentication.md
```

No fluff. No shortcuts. This follows **industry best practices** and is **future-proof**.

---

````md
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
````

Response:

```json
{
  "user_id": "uuid"
}
```

---

### Login

**POST `/auth/login`**

Request:

```json
{
  "email": "user@example.com",
  "password": "StrongPassword@123"
}
```

Response:

```json
{
  "access_token": "jwt-access-token",
  "refresh_token": "secure-refresh-token",
  "token_type": "bearer",
  "expires_in": 900
}
```

---

### Refresh Access Token

**POST `/auth/refresh`**

Request:

```json
{
  "refresh_token": "secure-refresh-token"
}
```

Response:

```json
{
  "access_token": "new-jwt-access-token",
  "expires_in": 900
}
```

---

### Logout

**POST `/auth/logout`**

Request:

```json
{
  "refresh_token": "secure-refresh-token"
}
```

Behavior:

* Refresh token is marked as revoked
* Access tokens naturally expire

---

## Token Storage Strategy (Frontend)

### Preferred (Web Applications)

* Tokens stored in **HttpOnly, Secure cookies**
* Protects against XSS attacks
* Frontend never accesses raw tokens

### Alternative (Development / API Clients)

* Access token passed via `Authorization: Bearer`
* Tokens stored in memory only
* Refresh token handled securely

---

## Authorization Model

* All protected endpoints require a valid access token
* User identity is derived from token claims
* Authorization logic is based on:

  * `user_id`
  * role flags (future enhancement)

---

## Security Considerations

* Short-lived access tokens reduce exposure risk
* Refresh token hashing prevents misuse if database is compromised
* Token revocation allows secure logout
* No sensitive data stored in JWT payload
* HTTPS enforced for all authentication endpoints

---

## Extensibility & Future Enhancements

The system is designed to support:

* OAuth providers (Google, Microsoft)
* Email verification workflows
* Password reset flows
* Multi-device session tracking
* Role-based access control (RBAC)

These can be added **without changing the core authentication flow**.

---

## Summary

The AIM 2026 authentication system is:

* Secure
* Scalable
* Maintainable
* Industry-aligned
* Ready for future growth

It prioritizes correctness and long-term reliability over short-term convenience.

```
