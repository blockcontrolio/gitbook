# Create employee

Creates a new employee (platform user) for the counterparty.

**Precondition:** The requesting user must have the `ADMIN` role.

---

## POST `/api/v1/counterparty/{counterparty_guid}/employees`

### Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `counterparty_guid` | string (UUID) | ✅ | Your counterparty GUID, received during onboarding |

### Request body

```json
{
  "email": "operator@mybank.com",
  "role": "USER",
  "first_name": "Anna",
  "last_name": "Kovalenko"
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `email` | string | ✅ | The employee's login email address. Must be unique within the counterparty. |
| `role` | enum | ✅ | `ADMIN`, `USER`, or `READ_ONLY` |
| `first_name` | string | ❌ | Employee's first name |
| `last_name` | string | ❌ | Employee's last name |

### Responses

**200 OK**

```json
{
  "guid": "e1a2b3c4-0000-0000-0000-000000000001"
}
```

Store the `guid` — this is the employee's permanent identifier.

**400 Bad Request**

```json
{
  "error": "EMPLOYEE_ALREADY_EXISTS",
  "message": "Employee with this email already exists",
  "statusCode": 400
}
```

---

## Example

```http
POST /api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/employees
Authorization: Bearer ${session_token}
Content-Type: application/json

{
  "email": "operator@mybank.com",
  "role": "USER",
  "first_name": "Anna",
  "last_name": "Kovalenko"
}
```

```bash
curl --location 'https://api.mybank.blockcontrol.io/api/v1/counterparty/ac75f1a6-0abe-11ee-be56-0242ac120002/employees' \
  --header 'Authorization: Bearer ${session_token}' \
  --header 'Content-Type: application/json' \
  --data '{
    "email": "operator@mybank.com",
    "role": "USER",
    "first_name": "Anna",
    "last_name": "Kovalenko"
  }'
```

---

{% hint style="info" %}
After creation, the employee receives an email prompting them to set their password. Until the password is set, the employee cannot log in.
{% endhint %}
