# ❗ Error messages

Consult this table if you encounter an error response from the BlockControl API.

---

## Standard HTTP error format

All errors are returned in a consistent JSON structure:

```json
{
  "error": "ERROR_CODE",
  "message": "Human-readable description of the error.",
  "statusCode": 400,
  "timestamp": "2025-03-14T10:30:00Z"
}
```

---

## Error reference table

| HTTP Status | Error Code | Message | Description |
|---|---|---|---|
| `401` | `UNAUTHORIZED` | Access credentials are invalid | You are not authenticated. Go to [Authentication](../api-methods/authentication/README.md) and obtain a valid session token. |
| `403` | `FORBIDDEN` | Insufficient permissions to perform this action | Your user role or account type does not permit this operation. Check [Roles & Permissions](https://docs.blockcontrol.io/core-concepts/roles-permissions). |
| `400` | `EMPLOYEE_ALREADY_EXISTS` | Employee with this email already exists | The email address is already registered for this counterparty. Use a different email. |
| `400` | `CUSTOMER_ALREADY_EXISTS` | Customer with this login/email already exists | A customer account with this identifier already exists. |
| `400` | `INVALID_ACCOUNT_TYPE` | Invalid account kind: bad request | You attempted to create an account with an unrecognized `account_kind`. Valid values: `admin`, `issuer`, `operator`, `distributor`, `client`. |
| `400` | `MISSING_RECEIVER_ID` | Missing `receiver_id` in payment request | The `receiver_id` field is required when creating a payment. |
| `400` | `MISSING_SENDER_ID` | Missing `sender_id` in payment request | The `sender_id` field is required when creating a payment. |
| `400` | `INSUFFICIENT_BALANCE` | Account has insufficient balance for this transfer | The sending account does not hold enough tokens to cover the requested amount. |
| `400` | `PARTNERSHIP_REQUIRED` | No confirmed partnership exists with the target counterparty | Inter-counterparty transfers require a confirmed partnership. Contact your BlockControl integration manager. |
| `400` | `TOKEN_PAUSED` | Token transfers are currently paused | The token's Pauser role has halted all transfers. Contact the token admin for your counterparty. |
| `400` | `INVALID_UUID` | Invalid UUID format | A GUID field contains an invalid or empty value. Check that all `_id` and `_guid` fields are valid UUID strings. |
| `404` | `NOT_FOUND` | Resource not found | The requested entity (account, payment, token, etc.) does not exist for this counterparty. |
| `409` | `CONFLICT` | Resource already exists | A resource with this identifier already exists. |
| `422` | `VALIDATION_ERROR` | Request body failed validation | One or more required fields are missing or have invalid values. Check the `details` field in the response for specifics. |
| `202` | `ASYNC_PENDING` | Operation accepted and queued for processing | Not an error — the operation has been accepted and will be processed asynchronously. Use the `transferId` to track status. |

---

{% hint style="info" %}
Contact the BlockControl integration team at **support@blockcontrol.io** if you receive an error code not listed here, or if an error persists after following the guidance above.
{% endhint %}
