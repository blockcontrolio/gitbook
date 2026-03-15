# Bank authorization

This page describes how to configure the bank's own authorization endpoint so that BlockControl can validate bank-issued tokens via reverse request.

---

## Overview

When the bank uses its own JWT (rather than BlockControl-issued tokens), every inbound API request triggers a **reverse validation call** from BlockControl back to the bank's authorization endpoint.

```
Bank System → POST /api/v1/payments (with bank JWT)
                    ↓
BlockControl receives request
                    ↓
BlockControl → POST {bank_auth_endpoint} (validates token)
                    ↓
Bank auth server responds: valid / invalid
                    ↓
BlockControl proceeds or rejects the original request
```

The original request waits until the validation completes. If the bank's auth endpoint is unreachable or returns invalid, the original request is rejected.

---

## Bank authorization endpoint specification

The bank must implement an HTTP endpoint that BlockControl will call to validate tokens. The specification below must be implemented exactly as described.

### Request from BlockControl

```http
POST {bank_auth_endpoint}
Content-Type: application/json
X-BlockControl-Signature: {request_signature}

{
  "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002"
}
```

| Field | Description |
|---|---|
| `token` | The JWT provided by the bank system in the original request's `Authorization` header |
| `counterparty_guid` | The counterparty GUID associated with this token |
| `X-BlockControl-Signature` | RSA signature of the request body, signed by BlockControl's private key. Validate using BlockControl's public key. |

---

### Response from bank (success)

```http
HTTP 200 OK
Content-Type: application/json

{
  "valid": true,
  "employee_id": "emp_001",
  "role": "USER",
  "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002"
}
```

| Field | Description |
|---|---|
| `valid` | `true` if the token is valid and not expired |
| `employee_id` | The identifier of the employee associated with this token |
| `role` | Employee role: `ADMIN`, `USER`, or `READ_ONLY` |
| `counterparty_guid` | Must match the counterparty GUID in the request |

### Response from bank (failure)

```http
HTTP 200 OK
Content-Type: application/json

{
  "valid": false,
  "reason": "Token expired"
}
```

{% hint style="warning" %}
Always return HTTP `200` from your auth endpoint — even for invalid tokens. Use the `valid` boolean field to communicate the result. Non-200 responses are treated as infrastructure errors and will cause BlockControl to reject the original request.
{% endhint %}

---

## Registering the bank authorization endpoint

During onboarding (step 8 of the [Technical Integration Plan](../../general/integration-plan/integration-plan-technical-team.md)), provide the following to your BlockControl integration manager:

- The URL of your authorization endpoint (must be `https://`)
- The RSA public key for validating your token signatures (minimum 2048 bits)
- The token validity period (recommended: 1200 seconds)

BlockControl will store these and use them for all reverse validation calls from your counterparty.

---

## Security checklist

- [ ] Auth endpoint is accessible over `https://`
- [ ] Endpoint validates the `X-BlockControl-Signature` before processing
- [ ] Token expiry is checked and reflected in `valid: false` when expired
- [ ] Endpoint returns `200` with `valid: false` rather than `401` for invalid tokens
- [ ] Logs all validation requests for audit purposes
- [ ] Public key has been securely shared with BlockControl
