# Create customer

Creates a new customer and provisions an on-chain client account on Arbitrum.

---

## POST `/api/v1/counterparty/{counterparty_guid}/customers`

### Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `counterparty_guid` | string (UUID) | ✅ | Your counterparty GUID |

### Request body

```json
{
  "email": "customer@example.com",
  "first_name": "Dmytro",
  "last_name": "Oliinyk",
  "external_id": "CUST-0042"
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `email` | string | ✅ | Customer's email. Must be unique within the counterparty. |
| `first_name` | string | ❌ | Customer's first name |
| `last_name` | string | ❌ | Customer's last name |
| `external_id` | string | ❌ | Your internal reference ID for this customer (e.g., CRM ID). Useful for reconciliation. |

### Responses

**200 OK**

```json
{
  "guid": "d5085e37-1a50-11ee-bd5f-3a6aa4436fee"
}
```

Store the `guid` — this is the customer's permanent identifier and is used as `receiver_id` or `sender_id` in payment requests.

**400 Bad Request**

```json
{
  "error": "CUSTOMER_ALREADY_EXISTS",
  "message": "Customer with this email already exists",
  "statusCode": 400
}
```

---

## Example

```http
POST /api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/customers
Authorization: Bearer ${session_token}
Content-Type: application/json

{
  "email": "customer@example.com",
  "first_name": "Dmytro",
  "last_name": "Oliinyk",
  "external_id": "CUST-0042"
}
```

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/customers' \
  --header 'Authorization: Bearer ${session_token}' \
  --header 'Content-Type: application/json' \
  --data '{
    "email": "customer@example.com",
    "first_name": "Dmytro",
    "last_name": "Oliinyk",
    "external_id": "CUST-0042"
  }'
```

---

## List customers per counterparty

## GET `/api/v1/counterparty/{counterparty_guid}/customers`

### Query parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `status` | enum | ❌ | `ACTIVE` or `DISABLED` |
| `external_id` | string | ❌ | Filter by your external reference ID |
| `limit` | integer | ❌ | Page size (default: 20) |
| `offset` | integer | ❌ | Pagination offset (default: 0) |

### Response — 200 OK

```json
{
  "customers": [
    {
      "guid": "d5085e37-1a50-11ee-bd5f-3a6aa4436fee",
      "email": "customer@example.com",
      "first_name": "Dmytro",
      "last_name": "Oliinyk",
      "external_id": "CUST-0042",
      "status": "ACTIVE",
      "created_at": "2025-03-01T10:00:00Z"
    }
  ],
  "total": 1
}
```

{% hint style="info" %}
After a customer is created, a **Client-type smart account** is automatically provisioned on Arbitrum. The account address is available in the full customer record returned by [Get customer](get-customer.md).
{% endhint %}
