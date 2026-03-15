# Limits

BlockControl supports configurable transfer limits at the account and counterparty level. Limits are enforced on-chain via the **Limiter** role and off-chain via platform-level business rules.

---

## Limit types

| Type | Scope | Enforcement |
|---|---|---|
| **Per-transaction limit** | Account | Maximum amount per single transfer |
| **Daily limit** | Account | Maximum cumulative transfer amount per 24-hour period |
| **Monthly limit** | Account | Maximum cumulative transfer amount per calendar month |
| **Counterparty limit** | Counterparty | Maximum cumulative inter-counterparty transfer volume |

---

## GET limits

### GET `/api/v1/counterparty/{counterparty_guid}/accounts/{account_guid}/limits`

Returns all configured limits for an account.

#### Response — 200 OK

```json
{
  "account_guid": "d5085e37-1a50-11ee-bd5f-3a6aa4436fee",
  "limits": [
    {
      "guid": "lim-001",
      "type": "PER_TRANSACTION",
      "asset_code": "FEUR",
      "max_amount": "10000.00",
      "status": "ACTIVE"
    },
    {
      "guid": "lim-002",
      "type": "DAILY",
      "asset_code": "FEUR",
      "max_amount": "50000.00",
      "current_usage": "12500.00",
      "resets_at": "2025-03-15T00:00:00Z",
      "status": "ACTIVE"
    }
  ]
}
```

---

## SET limit

### POST `/api/v1/counterparty/{counterparty_guid}/accounts/{account_guid}/limits`

{% hint style="warning" %}
Setting limits is an on-chain operation performed via the **Limiter** role. The account performing this action must have the `Limiter` on-chain role assigned for the relevant token. This is an asynchronous operation — a `transfer_id` is returned and the limit becomes active once confirmed on Arbitrum.
{% endhint %}

#### Request body

```json
{
  "type": "DAILY",
  "asset_code": "FEUR",
  "asset_issuer": "0xTokenContractAddress",
  "max_amount": "50000.00"
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `type` | enum | ✅ | `PER_TRANSACTION`, `DAILY`, or `MONTHLY` |
| `asset_code` | string | ✅ | Token ticker |
| `asset_issuer` | string | ✅ | Token contract address |
| `max_amount` | string | ✅ | Maximum allowed amount |

#### Response — 202 Accepted

```json
{
  "transfer_id": "txn-limit-001",
  "status": "PENDING",
  "message": "Limit update queued for on-chain processing"
}
```

---

## Limit enforcement

When a payment is created that would exceed a configured limit, the API returns:

```json
{
  "error": "LIMIT_EXCEEDED",
  "message": "This transfer would exceed the daily limit of 50000.00 FEUR for account d5085e37.",
  "statusCode": 400,
  "details": {
    "limit_type": "DAILY",
    "limit_amount": "50000.00",
    "current_usage": "47000.00",
    "requested_amount": "5000.00"
  }
}
```
