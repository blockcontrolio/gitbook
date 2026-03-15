# Reconciliation

BlockControl provides reconciliation endpoints to allow banks and EMIs to independently verify their on-chain balances and transaction history against the platform's internal records.

---

## Why reconciliation matters

All EMT operations are recorded both in BlockControl's database (off-chain) and on the Arbitrum blockchain (on-chain). For regulatory compliance under MiCAR and internal audit requirements, institutions should periodically reconcile these two sources.

BlockControl also provides **Arbiscan** as an independent third-party source for on-chain verification.

---

## GET reconciliation report

### GET `/api/v1/counterparty/{counterparty_guid}/reconciliation`

Returns a summary of all account balances and transaction totals for the counterparty, with cross-references to on-chain data.

#### Query parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `date` | ISO 8601 (date) | ❌ | Reconciliation date. Defaults to today. Format: `2025-03-14` |
| `asset_code` | string | ❌ | Filter by token ticker |

#### Response — 200 OK

```json
{
  "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002",
  "reconciliation_date": "2025-03-14",
  "generated_at": "2025-03-14T23:59:00Z",
  "assets": [
    {
      "asset_code": "FEUR",
      "asset_issuer": "0xTokenContractAddress",
      "total_issued": "1000000.00",
      "total_burned": "50000.00",
      "net_supply": "950000.00",
      "platform_balance": "950000.00",
      "onchain_balance": "950000.00",
      "discrepancy": "0.00",
      "status": "RECONCILED"
    }
  ],
  "transactions": {
    "total_count": 1542,
    "confirmed_count": 1538,
    "pending_count": 4,
    "failed_count": 0
  }
}
```

| Field | Description |
|---|---|
| `platform_balance` | Balance recorded in BlockControl's database |
| `onchain_balance` | Balance read directly from Arbitrum smart contract |
| `discrepancy` | Difference between platform and on-chain balances. Should always be `"0.00"`. |
| `status` | `RECONCILED` (no discrepancy) or `DISCREPANCY_DETECTED` |

{% hint style="warning" %}
If `status` is `DISCREPANCY_DETECTED`, contact BlockControl support immediately at **support@blockcontrol.io** and do not initiate new transfers until the discrepancy is resolved.
{% endhint %}

---

## GET transaction reconciliation

### GET `/api/v1/counterparty/{counterparty_guid}/reconciliation/transactions`

Returns a full list of transactions with their on-chain hashes for independent verification on Arbiscan.

#### Query parameters

| Parameter | Type | Description |
|---|---|---|
| `from_date` | ISO 8601 | Start of reconciliation period |
| `to_date` | ISO 8601 | End of reconciliation period |
| `asset_code` | string | Filter by token |
| `status` | enum | `CONFIRMED`, `FAILED`, `PENDING` |
| `limit` | integer | Page size (default: 100) |
| `offset` | integer | Pagination offset |

#### Response — 200 OK

```json
{
  "transactions": [
    {
      "payment_guid": "37f6b441-0906-46b6-9349-e742531f4eb7",
      "asset_code": "FEUR",
      "amount": "500.00",
      "sender_id": "7f971dc7-0fd2-4c5c-926d-75a6d6088535",
      "receiver_id": "d5085e37-1a50-11ee-bd5f-3a6aa4436fee",
      "status": "CONFIRMED",
      "arbitrum_hash": "0xe2302182308fad32018e57095537d7850a085ae8034df07224f8d5da79c1662d",
      "explorer_url": "https://arbiscan.io/tx/0xe2302182308fad32018e57095537d7850a085ae8034df07224f8d5da79c1662d",
      "confirmed_at": "2025-03-14T10:30:45Z"
    }
  ],
  "total": 1
}
```

---

## Independent on-chain verification

Every confirmed transaction can be independently verified on Arbiscan without relying on BlockControl's API:

1. Take the `arbitrum_hash` from any payment record
2. Visit: `https://arbiscan.io/tx/{arbitrum_hash}`
3. Verify sender address, receiver address, token, and amount match the platform record

This provides a fully auditable, trustless verification path for all settlements.
