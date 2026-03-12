# Auth Server

For production deployments, BlockControl supports a **separated authentication server** decoupled from the main API. This enables enterprise identity management, SSO integration, and compliance with organizational security policies.

See [Authentication](../features/authentication.md) for JWT basics and the full auth configuration reference.

---

## Why a Separated Auth Server?

In regulated financial environments, identity and access management (IAM) is often centrally governed. A separated auth server allows BlockControl to:

- Integrate with your bank's existing Identity Provider (IdP)
- Support Single Sign-On (SSO) across multiple systems
- Apply MFA (Multi-Factor Authentication) policies centrally
- Maintain user identity outside of BlockControl's database

---

## Supported Providers

| Provider | Type | Notes |
|---|---|---|
| **Okta** | External SaaS IdP | OIDC + OAuth 2.0; enterprise SSO |
| **Keycloak** | Self-hosted | Open-source; full control; LDAP/AD integration |
| **Spring Boot Auth Server** | Self-hosted | Custom implementation for specific requirements |

---

## Keycloak Setup Guide

### 1. Deploy Keycloak

```bash
docker run -p 8080:8080 \
  -e KEYCLOAK_ADMIN=admin \
  -e KEYCLOAK_ADMIN_PASSWORD=admin \
  quay.io/keycloak/keycloak:latest start-dev
```

### 2. Create a Realm
- Open Keycloak admin at `http://localhost:8080`
- Create a new realm: `blockcontrol`

### 3. Create a Client
- Client ID: `blockcontrol-api`
- Client Protocol: `openid-connect`
- Access Type: `confidential`
- Valid Redirect URIs: `https://app.blockcontrol.io/*`

### 4. Map Roles
Create roles in Keycloak matching BlockControl's user roles:
- `blockcontrol-admin`
- `blockcontrol-user`
- `blockcontrol-readonly`

### 5. Configure BlockControl

In Admin Panel → **Settings → Authentication**:

```json
{
  "provider": "KEYCLOAK",
  "jwksUrl": "http://keycloak:8080/realms/blockcontrol/protocol/openid-connect/certs",
  "tokenIssuer": "http://keycloak:8080/realms/blockcontrol",
  "roleClaim": "realm_access.roles"
}
```

---

## Okta Setup Guide

### 1. Create an Okta Application
- Sign in to Okta Admin
- Create a new **API Services** application
- Note the Client ID and Issuer URL

### 2. Configure Authorization Server
- Create a custom authorization server or use the default
- Add a custom claim for BlockControl roles: `blockcontrol_role`

### 3. Configure BlockControl

```json
{
  "provider": "OKTA",
  "jwksUrl": "https://your-domain.okta.com/oauth2/default/v1/keys",
  "tokenIssuer": "https://your-domain.okta.com/oauth2/default",
  "roleClaim": "blockcontrol_role"
}
```

---

## Token Validation

Regardless of provider, BlockControl validates every incoming JWT by:
1. Fetching the provider's public keys from the JWKS URL
2. Verifying the token signature
3. Checking the `iss` (issuer) claim matches the configured issuer
4. Checking the `exp` (expiration) claim
5. Extracting the role claim and mapping to a BlockControl user role
