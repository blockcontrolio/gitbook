# Get customer

Retrieve a full customer record including associated on-chain accounts.

---

## GET `/api/v1/counterparty/{counterparty_guid}/customers/{customer_guid}`

### Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `counterparty_guid` | string (UUID) | ✅ | Your counterparty GUID |
| `customer_guid` | string (UUID) | ✅ | The customer's GUID (returned at creation) |

### Response — 200 OK

```json
{
  "guid": "d5085e37-1a50-11ee-bd5f-3a6aa4436fee",
  "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002",
  "external_id": "CUST-0042",
  "email": "customer@example.com",
  "first_name": "Dmytro",
  "last_name": "Oliinyk",
  "status": "ACTIVE",
  "accounts": [
    {
      "guid": "acc-client-001",
      "account_kind": "client",
      "address": "0xClientWalletAddress",
      "status": "ACTIVE"
    }
  ],
  "created_at": "2025-03-01T10:00:00Z",
  "updated_at": "2025-03-01T10:00:00Z"
}
```

### Example

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/customers/d5085e37-1a50-11ee-bd5f-3a6aa4436fee' \
  --header 'Authorization: Bearer ${session_token}'
```

{% hint style="info" %}
Use `accounts[].address` to verify on-chain account identity on Arbiscan: `https://arbiscan.io/address/{address}`
{% endhint %}
