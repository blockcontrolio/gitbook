# RBAC Authorization

BlockControl enforces **Role-Based Access Control (RBAC)** at every layer of the platform. All API requests are validated against the authenticated user's role and the requesting account's type and on-chain roles before any operation is executed.

---

## Two-Layer Model

See [Roles & Permissions](../core-concepts/roles-permissions.md) for a full breakdown of the two-layer model (user roles vs. account/on-chain roles).

---

## Enforcement Points

RBAC is enforced server-side. There is no way to bypass role checks via the API or UI. The following endpoints enforce RBAC:

| Endpoint | Required Role |
|---|---|
| `POST /accounts` | User or Admin |
| `POST /accounts/{id}/smart-account` | Admin |
| `POST /tokens` | Admin (with Admin-type account) |
| `POST /tokens/{id}/mint` | Account must have Minter role for this token |
| `POST /tokens/{id}/burn` | Account must have Burner role for this token |
| `POST /tokens/{id}/roles/grant` | Account must have Admin on-chain role |
| `POST /tokens/{id}/roles/revoke` | Account must have Admin on-chain role |
| `POST /transfers/*` | User or Admin + account type transfer rules |
| `POST /users` | Admin user |
| `PATCH /users/{id}` | Admin user |
| `POST /webhooks` | Admin user |

---

## Least Privilege by Default

New users are created with the minimum required role. When creating a user:
- Default role: **User** (operational access, no admin capabilities)
- Elevation to **Admin** must be done explicitly by an existing Admin

New accounts are created as specific types that constrain on-chain capabilities by design. No account type has broader permissions than necessary for its function.

---

## Handling Authorization Errors

When a request is denied due to insufficient permissions, the API returns:

```json
{
  "error": "FORBIDDEN",
  "message": "User does not have permission to perform this action.",
  "statusCode": 403
}
```

Or for on-chain role violations:

```json
{
  "error": "FORBIDDEN",
  "message": "Account acc_client_042 does not have the MINTER role for token tok_feur_001.",
  "statusCode": 403
}
```

---

## Audit Trail

All role assignments, revocations, and permission changes are logged:
- Who made the change (user ID)
- What was changed (role/permission)
- When it occurred (timestamp)
- Which account or user was affected

This audit log is available to Admin users and can be exported for compliance reporting.
