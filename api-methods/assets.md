# Assets

Manage Electronic Money Tokens (EMTs) for your counterparty.

---

An **Asset** in BlockControl is an EMT smart contract deployed on Arbitrum. Assets have an `asset_code` (ticker symbol) and an `asset_issuer` (the on-chain contract address). Both fields are required to uniquely identify a token in payment requests.

---

## Asset object

```json
{
  "guid": "tok-feur-001",
  "asset_code": "FEUR",
  "asset_issuer": "0xTokenContractAddress",
  "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002",
  "name": "Euro Stablecoin",
  "total_supply": "1000000.00",
  "status": "ACTIVE",
  "explorer_url": "https://arbiscan.io/token/0xTokenContractAddress",
  "created_at": "2025-03-01T08:00:00Z"
}
```

---

## GET assets list

## GET `/api/v1/counterparty/{counterparty_guid}/assets`

### Response — 200 OK

```json
{
  "assets": [
    {
      "guid": "tok-feur-001",
      "asset_code": "FEUR",
      "asset_issuer": "0xTokenContractAddress",
      "name": "Euro Stablecoin",
      "total_supply": "1000000.00",
      "status": "ACTIVE"
    }
  ],
  "total": 1
}
```

### Example

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/assets' \
  --header 'Authorization: Bearer ${session_token}'
```

---

## Issue assets (mint)

Issue new token supply on the emission (Issuer) account. Only accounts with the `Minter` on-chain role can mint.

## POST `/api/v1/counterparty/{counterparty_guid}/assets/issue`

### Request body

```json
{
  "asset_code": "FEUR",
  "asset_issuer": "0xTokenContractAddress",
  "amount": "100000.00",
  "destination_account_guid": "8a1b2c3d-0000-0000-0000-000000000001"
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `asset_code` | string | ✅ | Token ticker |
| `asset_issuer` | string | ✅ | Token contract address on Arbitrum |
| `amount` | string | ✅ | Amount to mint (decimal string) |
| `destination_account_guid` | string (UUID) | ✅ | Must be an Issuer-type (emission) account |

### Response — 202 Accepted

```json
{
  "transfer_id": "txn-mint-001",
  "status": "PENDING",
  "amount": "100000.00",
  "asset_code": "FEUR"
}
```

{% hint style="info" %}
To start processing transfers, first issue assets to the emission account, then transfer from emission to the processing (distributor) account using the [Payment API](payment/README.md).
{% endhint %}

---

## Burn assets

Reduce token supply by burning from an Issuer account. Only accounts with the `Burner` on-chain role can burn.

## POST `/api/v1/counterparty/{counterparty_guid}/assets/burn`

### Request body

```json
{
  "asset_code": "FEUR",
  "asset_issuer": "0xTokenContractAddress",
  "amount": "5000.00",
  "source_account_guid": "8a1b2c3d-0000-0000-0000-000000000001"
}
```

### Response — 202 Accepted

```json
{
  "transfer_id": "txn-burn-001",
  "status": "PENDING",
  "amount": "5000.00",
  "asset_code": "FEUR"
}
```
