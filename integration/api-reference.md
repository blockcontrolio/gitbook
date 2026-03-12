# API Reference

BlockControl exposes a RESTful API for all platform operations. The full specification is available as a Swagger/OpenAPI document. This page covers authentication, base URL conventions, and key endpoint categories.

---

## Base URL

```
https://api.blockcontrol.io/api/v1
```

---

## Authentication

All API requests require a valid JWT Bearer token. See [Authentication](../features/authentication.md) for how to obtain a token.

```http
Authorization: Bearer {your_jwt_token}
```

---

## Request & Response Format

- All request bodies are `application/json`
- All responses are `application/json`
- Dates are ISO 8601 format: `2025-03-11T10:00:00Z`
- Amounts are strings to preserve decimal precision: `"1500.00"`

---

## Async Operations

All blockchain operations are **asynchronous**. When you submit a request that triggers an on-chain transaction (mint, burn, transfer, role assignment, token creation), the API returns immediately with a `PENDING` status and a `transactionId`.

To track completion:
1. **Poll** `GET /transfers/{transactionId}` for status
2. Or configure a **webhook** to receive a push notification on status change (recommended)

See [Async API](async-api.md) and [Webhooks](webhooks.md) for details.

---

## Endpoint Categories

### Authentication
| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/auth/login` | Obtain JWT access token |
| `POST` | `/auth/refresh` | Refresh access token |
| `POST` | `/auth/logout` | Invalidate token |

### Counterparties
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/counterparties` | List counterparties |
| `GET` | `/counterparties/{id}` | Get counterparty detail |
| `POST` | `/counterparties/{id}/partnerships` | Create partnership |
| `GET` | `/counterparties/{id}/partnerships` | List confirmed partners |

### Accounts
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/accounts` | List accounts for counterparty |
| `POST` | `/accounts` | Create a new account |
| `GET` | `/accounts/{id}` | Get account detail |
| `POST` | `/accounts/{id}/smart-account` | Provision smart account on-chain |
| `GET` | `/accounts/{id}/balances` | Get token balances |
| `PATCH` | `/accounts/{id}` | Update account (name, status) |

### Tokens
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/tokens` | List tokens |
| `POST` | `/tokens` | Create (deploy) a new EMT |
| `GET` | `/tokens/{id}` | Get token detail |
| `POST` | `/tokens/{id}/mint` | Mint new supply |
| `POST` | `/tokens/{id}/burn` | Burn supply |
| `POST` | `/tokens/{id}/roles/grant` | Grant on-chain role to account |
| `POST` | `/tokens/{id}/roles/revoke` | Revoke on-chain role |

### Transfers
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/transfers` | List transfers (filterable) |
| `POST` | `/transfers/internal` | Initiate internal transfer |
| `POST` | `/transfers/external` | Initiate external transfer |
| `POST` | `/transfers/inter-counterparty` | Initiate inter-counterparty transfer |
| `GET` | `/transfers/{id}` | Get transfer detail and status |

### Invoices
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/invoices` | List invoices |
| `POST` | `/invoices` | Create invoice |
| `GET` | `/invoices/{id}` | Get invoice detail |
| `POST` | `/invoices/{id}/pay` | Process payment for invoice |
| `DELETE` | `/invoices/{id}` | Cancel invoice |

### Users
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/users` | List users for counterparty |
| `POST` | `/users` | Create a new user |
| `PATCH` | `/users/{id}` | Update user role or permissions |

### Webhooks
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/webhooks` | List registered webhooks |
| `POST` | `/webhooks` | Register a new webhook |
| `DELETE` | `/webhooks/{id}` | Delete webhook |

### Paymasters
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/paymasters` | List paymasters |
| `GET` | `/paymasters/{id}` | Get paymaster detail |
| `POST` | `/paymasters/{id}/transfer-ownership` | Transfer paymaster ownership |

---

## Swagger / OpenAPI Spec

The full OpenAPI specification is available at:

```
https://api.blockcontrol.io/api/v1/swagger-ui
```

Or download the raw spec:

```
GET https://api.blockcontrol.io/api/v1/api-docs
```

The Swagger spec includes:
- Full request/response schemas for every endpoint
- Field descriptions and validation rules
- Example requests and responses
- Authentication requirements per endpoint

---

## Error Responses

All errors follow a consistent format:

```json
{
  "error": "INSUFFICIENT_BALANCE",
  "message": "Account acc_client_042 has insufficient FEUR balance for this transfer.",
  "statusCode": 400,
  "timestamp": "2025-03-11T10:00:00Z"
}
```

### Common Error Codes

| Code | HTTP Status | Description |
|---|---|---|
| `UNAUTHORIZED` | 401 | Missing or invalid JWT token |
| `FORBIDDEN` | 403 | Insufficient role/permission for this action |
| `NOT_FOUND` | 404 | Resource does not exist |
| `INSUFFICIENT_BALANCE` | 400 | Not enough token balance |
| `INVALID_ACCOUNT_TYPE` | 400 | Account type not permitted for this operation |
| `PARTNERSHIP_REQUIRED` | 400 | No confirmed partnership exists |
| `TOKEN_PAUSED` | 400 | Token transfers are currently paused |
| `ASYNC_PENDING` | 202 | Operation accepted and queued for processing |
