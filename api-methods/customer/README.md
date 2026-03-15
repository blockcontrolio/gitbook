# Customer

How to manage customer accounts under your counterparty.

---

A **Customer** represents an end-user or business client who holds a token balance on the BlockControl platform. Each customer is associated with one or more **Client-type accounts** within your counterparty.

API provides the following ways to work with customers:

- [Create customer](create-customer.md)
- [Get customer](get-customer.md)

---

## Customer object

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
      "address": "0xClientWalletAddress"
    }
  ],
  "created_at": "2025-03-01T10:00:00Z",
  "updated_at": "2025-03-01T10:00:00Z"
}
```

| Field | Type | Description |
|---|---|---|
| `guid` | string (UUID) | Unique customer identifier — used as `receiver_id` or `sender_id` in payments |
| `counterparty_guid` | string (UUID) | The counterparty this customer belongs to |
| `external_id` | string | Optional external reference ID (e.g., your CRM ID) |
| `email` | string | Customer email address |
| `first_name` | string | Customer first name |
| `last_name` | string | Customer last name |
| `status` | enum | `ACTIVE` or `DISABLED` |
| `accounts` | array | On-chain accounts associated with this customer |
| `accounts[].account_kind` | enum | Always `client` for customer accounts |
| `accounts[].address` | string | Arbitrum wallet address for this account |
