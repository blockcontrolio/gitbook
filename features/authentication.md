# Authentication

BlockControl uses a **JWT-based authentication framework** to secure all API and UI access. In Milestone 02, this is extended with support for external authentication providers (Okta, Keycloak, Spring Boot Auth Server).

---

## Milestone 01: JWT Authentication

### How It Works

1. User submits credentials (email + password) via the login UI or API
2. The platform validates credentials and issues a signed **JWT access token**
3. The JWT is included in all subsequent API requests as a Bearer token
4. The platform validates the JWT on each request and enforces the user's role and permissions

### Login via API

```http
POST /api/v1/auth/login
Content-Type: application/json

{
  "email": "user@mybank.com",
  "password": "securepassword"
}
```

**Response:**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

### Using the Token

Include the access token in the `Authorization` header for all requests:

```http
GET /api/v1/accounts
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

### Token Refresh

```http
POST /api/v1/auth/refresh
Content-Type: application/json

{
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

---

## Milestone 02: Separated Auth Server

For production deployments requiring enterprise-grade identity management, BlockControl supports integration with an external or self-hosted auth server.

### Option A: External Auth Provider (Okta, Auth0, etc.)

Configure BlockControl to delegate authentication to your existing Okta or similar IdP:

- Users authenticate via Okta
- Okta issues a JWT / OIDC token
- BlockControl validates the token against Okta's JWKS endpoint
- User roles are mapped from Okta claims to BlockControl roles

### Option B: Self-Hosted Auth Server (Keycloak / Spring Boot)

Deploy a Keycloak or Spring Boot auth server alongside BlockControl:

- Provides full control over user identity and token issuance
- Supports SAML, OIDC, and OAuth 2.0
- Can integrate with your bank's existing LDAP/Active Directory

### Configuration

Auth server settings are configured in the Admin Panel under **Settings → Authentication**:

| Setting | Description |
|---|---|
| **Auth Provider** | Internal / Okta / Keycloak / Custom |
| **JWKS URL** | URL for public key retrieval (external providers) |
| **Token Issuer** | Expected `iss` claim in JWT |
| **Role Claim** | JWT claim field mapping to BlockControl user roles |
