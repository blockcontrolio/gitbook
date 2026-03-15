# Payment

{% hint style="info" %}
To start using transfers, first [issue assets](../assets.md#issue-assets) to your emission account, then distribute them to the processing account. Once the processing account holds a balance, transfers to customers can begin.
{% endhint %}

API provides the following ways to work with payments:

- [Get payment information](#get-payment-information)
- [Create payment](#create-payment)
- [Confirm payment](#confirm-payment)
- [Get payment list](#get-payment-list)

---

As best practice, rely on the following transfer routes **inside 1 Counterparty**:

- From emission account to processing account
- From processing account to customer account
- From customer account to customer account
- From processing account back to emission account
- From customer account to processing account

For **external transfers (2 counterparties)** the following transfer routes are advised:

- From your processing account to the processing account of another Counterparty
- From your customer account to a customer of another Counterparty

---

## Get payment information

### GET `/api/v1/counterparty/{counterparty_guid}/payments/{payment_guid}`

#### Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `counterparty_guid` | string (UUID) | ✅ | Your counterparty GUID |
| `payment_guid` | string (UUID) | ✅ | The payment GUID |

#### Query parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `with_arbitrum_info` | boolean | ❌ | Include on-chain Arbitrum transaction details |

#### Response — 200 OK

```json
{
  "guid": "37f6b441-0906-46b6-9349-e742531f4eb7",
  "asset_issuer": "0xTokenContractAddress",
  "asset_code": "FEUR",
  "sender_id": "7f971dc7-0fd2-4c5c-926d-75a6d6088535",
  "sender_counterparty": "ac75f1a6-0abe-11ee-be56-0242ac120002",
  "sender_account_account_kind": "distributor",
  "receiver_id": "d5085e37-1a50-11ee-bd5f-3a6aa4436fee",
  "receiver_counterparty": "ac75f1a6-0abe-11ee-be56-0242ac120002",
  "receiver_account_account_kind": "client",
  "amount": "500.00",
  "status": "CONFIRMED",
  "fee": "0.00",
  "arbitrum_info": {
    "hash": "0xe2302182308fad32018e57095537d7850a085ae8034df07224f8d5da79c1662d",
    "explorer_url": "https://arbiscan.io/tx/0xe2302182308fad32018e57095537d7850a085ae8034df07224f8d5da79c1662d"
  },
  "created_at": "2025-03-14T10:30:00Z",
  "updated_at": "2025-03-14T10:30:45Z",
  "deleted_at": null
}
```

{% hint style="success" %}
Using the `arbitrum_info.hash` field, you can verify this transaction independently on [Arbiscan](https://arbiscan.io).
{% endhint %}

| Field | Type | Description |
|---|---|---|
| `guid` | string | Payment GUID |
| `asset_code` | string | Token ticker (e.g., `FEUR`) |
| `asset_issuer` | string | Token contract address on Arbitrum |
| `sender_id` | string (UUID) | Sending account GUID |
| `sender_counterparty` | string (UUID) | Sending counterparty GUID |
| `sender_account_account_kind` | enum | `admin`, `issuer`, `distributor`, `client` |
| `receiver_id` | string (UUID) | Receiving account GUID |
| `receiver_counterparty` | string (UUID) | Receiving counterparty GUID |
| `receiver_account_account_kind` | enum | `admin`, `issuer`, `distributor`, `client` |
| `amount` | string | Transfer amount |
| `status` | enum | `CREATED`, `PENDING`, `SUBMITTED`, `CONFIRMED`, `FAILED` |
| `fee` | string | Transaction fee (typically `"0.00"` — sponsored by Paymaster) |
| `arbitrum_info.hash` | string | On-chain transaction hash |
| `arbitrum_info.explorer_url` | string | Direct Arbiscan link |

#### Example

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/payments/37f6b441-0906-46b6-9349-e742531f4eb7?with_arbitrum_info=true' \
  --header 'Authorization: Bearer ${session_token}'
```

---

## Create transfer

A transfer is completed in two steps:
1. **Create payment** — registers a draft payment record
2. **Confirm payment** — submits the payment for on-chain processing

### Create payment

{% hint style="warning" %}
**Precondition**: The sender's account must hold a sufficient balance before creating a transfer.
{% endhint %}

### POST `/api/v1/counterparty/{counterparty_guid}/payments`

#### Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `counterparty_guid` | string (UUID) | ✅ | Your counterparty GUID |

#### Request body

```json
{
  "amount": "500.00",
  "asset_code": "FEUR",
  "asset_issuer": "0xTokenContractAddress",
  "sender_id": "7f971dc7-0fd2-4c5c-926d-75a6d6088535",
  "receiver_id": "d5085e37-1a50-11ee-bd5f-3a6aa4436fee",
  "purpose": "Monthly salary disbursement"
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `amount` | string | ✅ | Amount of tokens to transfer |
| `asset_code` | string | ✅ | Token ticker (e.g., `FEUR`) |
| `asset_issuer` | string | ✅ | Token contract address on Arbitrum |
| `sender_id` | string (UUID) | ✅ | GUID of the sending account |
| `receiver_id` | string (UUID) | ✅ | GUID of the receiving account |
| `receiver_iban` | string | ❌ | IBAN of the receiver account (if applicable) |
| `sender_iban` | string | ❌ | IBAN of the sender account (if applicable) |
| `purpose` | string | ❌ | Optional payment description / reference |
| `target_balance` | string | ❌ | Expected account balance after the operation |

#### Response — 200 OK

```json
{
  "guid": "37f6b441-0906-46b6-9349-e742531f4eb7"
}
```

Store the `guid` as `payment_guid` — required for the confirmation step.

#### Example

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/payments' \
  --header 'Authorization: Bearer ${session_token}' \
  --header 'Content-Type: application/json' \
  --data '{
    "amount": "500.00",
    "asset_code": "FEUR",
    "asset_issuer": "0xTokenContractAddress",
    "sender_id": "7f971dc7-0fd2-4c5c-926d-75a6d6088535",
    "receiver_id": "d5085e37-1a50-11ee-bd5f-3a6aa4436fee",
    "purpose": "Monthly salary disbursement"
  }'
```

---

### Confirm payment

After creating a payment, you must confirm it to submit the transaction for on-chain processing. Before confirming, ensure the payment has been created and you have the correct `payment_guid`.

`bank_guid` (receiving counterparty GUID) is **mandatory only for inter-counterparty (inter-bank) payments**.

### POST `/api/v1/counterparty/{counterparty_guid}/payments/confirm`

#### Request body

```json
{
  "guid": "37f6b441-0906-46b6-9349-e742531f4eb7",
  "bank_guid": "130a613a-d5ba-42fd-9915-ae289f392def"
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `guid` | string (UUID) | ✅ | Payment GUID from the create step |
| `bank_guid` | string (UUID) | ❌ | Receiving counterparty GUID. Required for inter-counterparty transfers only. If omitted, the payment is treated as internal. |

#### Response — 202 Accepted

```json
{
  "guid": "37f6b441-0906-46b6-9349-e742531f4eb7",
  "status": "PENDING"
}
```

The payment is now queued for on-chain processing. Poll [Get payment information](#get-payment-information) or wait for a [webhook callback](callbacks.md) for status updates.

#### Example

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/payments/confirm' \
  --header 'Authorization: Bearer ${session_token}' \
  --header 'Content-Type: application/json' \
  --data '{
    "guid": "37f6b441-0906-46b6-9349-e742531f4eb7",
    "bank_guid": "130a613a-d5ba-42fd-9915-ae289f392def"
  }'
```

---

## Get payment list

Returns a paginated, filterable list of all payments for the counterparty.

### GET `/api/v1/counterparty/{counterparty_guid}/payments`

#### Query parameters

| Parameter | Type | Description |
|---|---|---|
| `receiver_counterparty` | string (UUID) | Filter by receiving counterparty GUID |
| `sender_counterparty` | string (UUID) | Filter by sending counterparty GUID |
| `guids` | string[] | Filter by specific payment GUIDs |
| `sender_ids` | string[] | Filter by sender account GUIDs |
| `receiver_ids` | string[] | Filter by receiver account GUIDs |
| `statuses` | string[] | Filter by status: `CREATED`, `PENDING`, `SUBMITTED`, `CONFIRMED`, `FAILED` |
| `asset_codes` | string[] | Filter by token ticker (e.g., `FEUR`) |
| `asset_issuers` | string[] | Filter by token contract address |
| `min_amount` | number | Minimum transfer amount |
| `max_amount` | number | Maximum transfer amount |
| `min_created_at` | ISO 8601 | Earliest creation date |
| `max_created_at` | ISO 8601 | Latest creation date |
| `order` | enum | Sort order: `created_at` |
| `limit` | integer | Page size (default: 20) |
| `offset` | integer | Pagination offset (default: 0) |

#### Response — 200 OK

```json
{
  "payments": [
    {
      "guid": "37f6b441-0906-46b6-9349-e742531f4eb7",
      "asset_code": "FEUR",
      "asset_issuer": "0xTokenContractAddress",
      "sender_id": "7f971dc7-0fd2-4c5c-926d-75a6d6088535",
      "sender_counterparty": "ac75f1a6-0abe-11ee-be56-0242ac120002",
      "sender_account_account_kind": "distributor",
      "receiver_id": "d5085e37-1a50-11ee-bd5f-3a6aa4436fee",
      "receiver_counterparty": "ac75f1a6-0abe-11ee-be56-0242ac120002",
      "receiver_account_account_kind": "client",
      "amount": "500.00",
      "status": "CONFIRMED",
      "fee": "0.00",
      "arbitrum_info": {
        "hash": "0xe2302182308fad32018e57095537d7850a085ae8034df07224f8d5da79c1662d"
      },
      "created_at": "2025-03-14T10:30:00Z",
      "updated_at": "2025-03-14T10:30:45Z"
    }
  ],
  "total": 1
}
```

#### Example

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/payments?statuses[]=CONFIRMED&asset_codes[]=FEUR&limit=20&offset=0' \
  --header 'Authorization: Bearer ${session_token}'
```
