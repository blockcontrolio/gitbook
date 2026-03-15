# Employee

How to manage platform users (employees) for your counterparty.

---

An **Employee** is a human operator who accesses the BlockControl platform on behalf of a counterparty. Employees log in via the UI or authenticate via API and perform operational tasks according to their role.

API provides the following ways to work with employees:

- [Create employee](create-employee.md)
- [Get employee](get-employee.md)

---

## Employee roles

| Role | Description |
|---|---|
| `ADMIN` | Full access: can create users, manage accounts, and perform all operations |
| `USER` | Operational access: can initiate transfers, manage invoices, view accounts |
| `READ_ONLY` | View-only: cannot perform any write operations |

---

## Employee object

```json
{
  "guid": "e1a2b3c4-0000-0000-0000-000000000001",
  "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002",
  "email": "operator@mybank.com",
  "role": "USER",
  "status": "ACTIVE",
  "created_at": "2025-03-01T09:00:00Z",
  "updated_at": "2025-03-01T09:00:00Z"
}
```

| Field | Type | Description |
|---|---|---|
| `guid` | string (UUID) | Unique employee identifier |
| `counterparty_guid` | string (UUID) | The counterparty this employee belongs to |
| `email` | string | Login email address |
| `role` | enum | `ADMIN`, `USER`, or `READ_ONLY` |
| `status` | enum | `ACTIVE` or `DISABLED` |
| `created_at` | ISO 8601 | Creation timestamp |
| `updated_at` | ISO 8601 | Last update timestamp |
