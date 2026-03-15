# Get employee

Retrieve a single employee record or list all employees for a counterparty.

---

## GET `/api/v1/counterparty/{counterparty_guid}/employees/{employee_guid}`

### Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `counterparty_guid` | string (UUID) | ✅ | Your counterparty GUID |
| `employee_guid` | string (UUID) | ✅ | The employee's GUID (returned at creation) |

### Response — 200 OK

```json
{
  "guid": "e1a2b3c4-0000-0000-0000-000000000001",
  "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002",
  "email": "operator@mybank.com",
  "role": "USER",
  "first_name": "Anna",
  "last_name": "Kovalenko",
  "status": "ACTIVE",
  "created_at": "2025-03-01T09:00:00Z",
  "updated_at": "2025-03-01T09:00:00Z"
}
```

### Example

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/employees/e1a2b3c4-0000-0000-0000-000000000001' \
  --header 'Authorization: Bearer ${session_token}'
```

---

## GET `/api/v1/counterparty/{counterparty_guid}/employees`

Returns a list of all employees for the counterparty.

### Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `counterparty_guid` | string (UUID) | ✅ | Your counterparty GUID |

### Query parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `role` | enum | ❌ | Filter by role: `ADMIN`, `USER`, `READ_ONLY` |
| `status` | enum | ❌ | Filter by status: `ACTIVE`, `DISABLED` |
| `limit` | integer | ❌ | Number of results per page (default: 20) |
| `offset` | integer | ❌ | Pagination offset (default: 0) |

### Response — 200 OK

```json
{
  "employees": [
    {
      "guid": "e1a2b3c4-0000-0000-0000-000000000001",
      "email": "operator@mybank.com",
      "role": "USER",
      "status": "ACTIVE",
      "created_at": "2025-03-01T09:00:00Z"
    },
    {
      "guid": "e1a2b3c4-0000-0000-0000-000000000002",
      "email": "admin@mybank.com",
      "role": "ADMIN",
      "status": "ACTIVE",
      "created_at": "2025-02-15T08:00:00Z"
    }
  ],
  "total": 2
}
```

### Example

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/employees?role=USER&limit=20&offset=0' \
  --header 'Authorization: Bearer ${session_token}'
```
