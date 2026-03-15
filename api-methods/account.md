# Account

Manage on-chain smart accounts for your counterparty.

---

Accounts are the on-chain wallets that hold token balances and participate in transfers. Each account has a **kind** (type) that determines its role in the token flow.

---

## Account kinds

| Kind | Description |
|---|---|
| `admin` | Can deploy tokens, assign roles, transfer freely |
| `issuer` | Mints and burns tokens (emission account) |
| `operator` | Controls compliance functions (pause, limit, freeze) — holds no balance |
| `distributor` | Distributes tokens between issuer and clients, or across counterparties (processing account) |
| `client` | End-user account — holds balance, can transfer |

---

## Account object

```json
{
  "guid": "7f971dc7-0fd2-4c5c-926d-75a6d6088535",
  "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002",
  "name": "Main Processing Account",
  "account_kind": "distributor",
  "address": "0xProcessingWalletAddress",
  "status": "ACTIVE",
  "balances": [
    {
      "asset_code": "FEUR",
      "asset_issuer": "0xTokenContractAddress",
      "amount": "50000.00"
    }
  ],
  "created_at": "2025-03-01T08:00:00Z"
}
```

---

## GET accounts list

## GET `/api/v1/counterparty/{counterparty_guid}/accounts`

### Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `counterparty_guid` | string (UUID) | ✅ | Your counterparty GUID |

### Query parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `account_kind` | enum | ❌ | Filter by kind: `admin`, `issuer`, `operator`, `distributor`, `client` |
| `status` | enum | ❌ | `ACTIVE` or `DISABLED` |
| `limit` | integer | ❌ | Page size (default: 20) |
| `offset` | integer | ❌ | Pagination offset (default: 0) |

### Response — 200 OK

```json
{
  "accounts": [
    {
      "guid": "7f971dc7-0fd2-4c5c-926d-75a6d6088535",
      "name": "Main Processing Account",
      "account_kind": "distributor",
      "address": "0xProcessingWalletAddress",
      "status": "ACTIVE"
    },
    {
      "guid": "8a1b2c3d-0000-0000-0000-000000000001",
      "name": "Emission Account",
      "account_kind": "issuer",
      "address": "0xEmissionWalletAddress",
      "status": "ACTIVE"
    }
  ],
  "total": 2
}
```

### Example

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/accounts?account_kind=distributor' \
  --header 'Authorization: Bearer ${session_token}'
```

---

## GET account balances

## GET `/api/v1/counterparty/{counterparty_guid}/accounts/{account_guid}/balances`

Returns real-time on-chain token balances for an account.

### Response — 200 OK

```json
{
  "account_guid": "7f971dc7-0fd2-4c5c-926d-75a6d6088535",
  "balances": [
    {
      "asset_code": "FEUR",
      "asset_issuer": "0xTokenContractAddress",
      "amount": "50000.00",
      "last_updated": "2025-03-14T10:30:00Z"
    }
  ]
}
```

{% hint style="info" %}
Balances are synced in real-time from Arbitrum. BlockControl also detects **external incoming transactions** automatically — if tokens are sent to an account address from outside the platform, the balance updates without any manual action.
{% endhint %}
