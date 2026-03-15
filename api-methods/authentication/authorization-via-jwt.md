# Authorization via JWT

BlockControl supports two JWT authorization modes. This page describes authorization using a **BlockControl-issued JWT**.

---

## JWT by BlockControl

When you log in via the `/auth/login` endpoint, BlockControl issues a signed JWT. This token:

- Is signed using BlockControl's RSA private key
- Can be validated by any party holding BlockControl's public key
- Contains claims identifying the employee, counterparty, and role

### Token flow

```
1. Employee logs in → POST /auth/login
      ↓
2. BlockControl validates credentials
      ↓
3. BlockControl issues signed JWT (session_token)
      ↓
4. Client includes JWT in Authorization header of all requests
      ↓
5. BlockControl validates JWT on each request
```

### Using the token

```http
GET /api/v1/counterparty/{counterparty_guid}/payments
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

---

## JWT by Bank

If your institution has its own Identity Provider (IdP) — such as Okta, Keycloak, or an internal auth server — you can configure BlockControl to accept **bank-issued JWTs** directly.

In this mode:
- The bank generates and signs the JWT using its own private key
- BlockControl validates the token using the bank's public key (shared during onboarding)
- No login call to BlockControl is required

See [Bank authorization](bank-authorization.md) for full setup instructions.

{% hint style="info" %}
Bank-issued JWT authorization is the **recommended approach** for automated system-to-system integrations where storing employee credentials is not appropriate.
{% endhint %}

---

## Obtaining BlockControl's public key

To validate BlockControl-issued JWTs on your side (e.g., for CoSigner signature verification):

```http
GET /api/v1/auth/jwks
```

**Response:**

```json
{
  "keys": [
    {
      "kty": "RSA",
      "use": "sig",
      "kid": "bc-key-2025-01",
      "alg": "RS256",
      "n": "...",
      "e": "AQAB"
    }
  ]
}
```

This JWKS endpoint is also used by external auth servers (Okta, Keycloak) for automatic key discovery.
