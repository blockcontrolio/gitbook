# Async API

All blockchain operations in BlockControl are **asynchronous**. This is by design — blockchain transactions are not instantaneous, and a synchronous API would result in long-hanging HTTP requests that are impractical for production banking systems.

---

## How It Works

When you submit any operation that results in an on-chain transaction, the API:

1. Validates the request (RBAC, business rules, balance checks)
2. Queues the operation for processing
3. Returns immediately with HTTP `202 Accepted` and a `transactionId`
4. Processes the transaction in the background via the MPC layer
5. Broadcasts to Arbitrum and awaits confirmation
6. Updates the transaction status
7. Fires a webhook (if configured)

---

## Async Operations List

The following operations are asynchronous:

| Operation | API Endpoint |
|---|---|
| Create token | `POST /tokens` |
| Mint token | `POST /tokens/{id}/mint` |
| Internal transfer | `POST /transfers/internal` |
| External transfer | `POST /transfers/external` |
| Inter-counterparty transfer | `POST /transfers/inter-counterparty` |
| Grant on-chain role | `POST /tokens/{id}/roles/grant` |
| Revoke on-chain role | `POST /tokens/{id}/roles/revoke` |
| Partnership creation | `POST /counterparties/{id}/partnerships` |
| Cross-counterparty transfer | `POST /transfers/inter-counterparty` |

---

## Polling for Status

If you prefer not to use webhooks, poll the transfer or operation endpoint:

```http
GET /api/v1/transfers/{transactionId}
Authorization: Bearer {jwt_token}
```

Poll with reasonable intervals (e.g., every 2–5 seconds). Arbitrum typically confirms transactions within 1–3 seconds, but allow up to 30 seconds for network congestion.

### Status Values

| Status | Description |
|---|---|
| `PENDING` | Queued, not yet submitted to blockchain |
| `SUBMITTED` | Broadcast to Arbitrum, awaiting confirmation |
| `CONFIRMED` | On-chain confirmation received |
| `FAILED` | Transaction reverted or processing error |

---

## Recommended Integration Pattern

For production systems, the recommended pattern is:

```
1. Submit operation → receive transactionId
2. Store transactionId in your system
3. Receive webhook on status change
4. Update your system state accordingly
```

This is more reliable than polling and ensures your system stays in sync with BlockControl without unnecessary API load.

---

## Integration Test Coverage

The following flows have integration test coverage in Milestone 02:

- Create token → CONFIRMED
- Mint token → CONFIRMED
- Internal transfer → CONFIRMED
- External transfer → CONFIRMED
- Inter-counterparty transfer → CONFIRMED
- Partnership creation → CONFIRMED
