# Transaction History

BlockControl maintains a complete, immutable record of all transactions initiated through or detected by the platform. Every transfer, minting event, burning event, and role assignment is logged with full detail.

---

## Transaction List

Navigate to **Transfers** in the sidebar to view the full transaction history for your counterparty.

### Available Columns

| Column | Description |
|---|---|
| **ID** | Unique platform transaction ID |
| **Type** | Internal / External / Inter-Counterparty / Mint / Burn |
| **From** | Sending counterparty and account |
| **To** | Receiving counterparty and account (or external address) |
| **Asset** | Token involved |
| **Amount** | Transfer amount |
| **Status** | Pending / Confirmed / Failed |
| **Hash** | On-chain transaction hash (explorer link) |
| **Datetime** | Timestamp of the operation |

---

## Filtering & Search

The transfer list supports filtering by:

- **Type** — Internal, External, Inter-Counterparty
- **Direction** — Incoming, Outgoing
- **Status** — Pending, Confirmed, Failed
- **Asset** — Filter by specific token
- **Date range** — Custom from/to date selection
- **Counterparty** — Filter by sender or recipient counterparty

---

## Transaction Detail Page

Clicking any transaction opens a dedicated detail page with the full context of that operation:

- **Transaction ID** and platform-level metadata
- **On-chain hash** with direct Arbiscan link
- **From / To** account details with account type labels
- **Token and amount**
- **Status timeline** — when it was submitted, when it reached the blockchain, when it was confirmed
- **Gas details** — sponsorship status via Paymaster

---

## Incoming External Transaction Detection

BlockControl actively listens to the Arbitrum blockchain for tokens sent to any platform wallet from external addresses. These transactions are automatically:

1. Detected via blockchain event listener
2. Attributed to the correct platform account
3. Added to the transaction history as an **Incoming External** transfer
4. Reflected in the account balance in real-time

No manual reconciliation is required.

---

## Querying Transaction History via API

```http
GET /api/v1/transfers?type=INTERNAL&status=CONFIRMED&direction=OUTGOING&from=2025-01-01
Authorization: Bearer {jwt_token}
```

**Response:**
```json
{
  "total": 42,
  "page": 1,
  "pageSize": 20,
  "transfers": [
    {
      "transferId": "txn_001",
      "type": "INTERNAL",
      "status": "CONFIRMED",
      "fromAccount": "acc_admin_001",
      "toAccount": "acc_client_042",
      "token": "FEUR",
      "amount": "500.00",
      "transactionHash": "0xabc...",
      "explorerUrl": "https://arbiscan.io/tx/0xabc...",
      "confirmedAt": "2025-03-11T09:15:00Z"
    }
  ]
}
```
