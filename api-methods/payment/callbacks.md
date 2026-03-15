# [optional] Callbacks

BlockControl can notify your system of payment status changes via **HTTP callbacks (webhooks)**. This is the recommended alternative to polling for production integrations.

---

## Overview

When a payment status changes (e.g., from `PENDING` to `CONFIRMED`), BlockControl sends an HTTP `POST` request to your registered callback URL with the updated payment details.

This eliminates the need to poll `GET /payments/{guid}` repeatedly and allows your system to react to on-chain events in real time.

---

## Registering a callback URL

Provide your callback URL to the BlockControl integration manager during onboarding, or register it via the Admin Panel under **Settings → Webhooks**.

Requirements:
- Must use `https://`
- Must be publicly reachable from BlockControl's infrastructure
- Must return HTTP `200` within 10 seconds

---

## Callback events

| Event | Trigger |
|---|---|
| `PAYMENT_STATUS_UPDATED` | Fired on every status change: `PENDING` → `SUBMITTED` → `CONFIRMED` or `FAILED` |
| `PAYMENT_APPROVAL_REQUIRED` | Fired when a payment enters `PENDING_APPROVAL` status (multisig only) |
| `INVOICE_COMPLETED` | Fired when an invoice payment is confirmed on-chain |

---

## Callback payload

### PAYMENT_STATUS_UPDATED

```http
POST {your_callback_url}
Content-Type: application/json
X-BlockControl-Signature: {signature}

{
  "event": "PAYMENT_STATUS_UPDATED",
  "timestamp": "2025-03-14T10:30:45Z",
  "data": {
    "guid": "37f6b441-0906-46b6-9349-e742531f4eb7",
    "status": "CONFIRMED",
    "previous_status": "SUBMITTED",
    "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002",
    "asset_code": "FEUR",
    "amount": "500.00",
    "arbitrum_info": {
      "hash": "0xe2302182308fad32018e57095537d7850a085ae8034df07224f8d5da79c1662d",
      "explorer_url": "https://arbiscan.io/tx/0xe2302182308fad32018e57095537d7850a085ae8034df07224f8d5da79c1662d"
    }
  }
}
```

### INVOICE_COMPLETED

```http
POST {your_callback_url}
Content-Type: application/json
X-BlockControl-Signature: {signature}

{
  "event": "INVOICE_COMPLETED",
  "timestamp": "2025-03-14T11:05:00Z",
  "data": {
    "invoice_guid": "inv-001",
    "payment_guid": "37f6b441-0906-46b6-9349-e742531f4eb7",
    "status": "COMPLETED",
    "amount": "1500.00",
    "asset_code": "FEUR"
  }
}
```

---

## Signature validation

All callback requests include an `X-BlockControl-Signature` header. This is an RSA-SHA256 signature of the request body, signed with BlockControl's private key. Validate it using [BlockControl's public key](../authentication/authorization-via-jwt.md#obtaining-blockcontrols-public-key) before processing the callback.

```java
// Java example — verify signature
byte[] bodyBytes = request.getBody().getBytes(StandardCharsets.UTF_8);
byte[] signatureBytes = Base64.getDecoder().decode(request.getHeader("X-BlockControl-Signature"));

Signature sig = Signature.getInstance("SHA256withRSA");
sig.initVerify(blockcontrolPublicKey);
sig.update(bodyBytes);
boolean isValid = sig.verify(signatureBytes);
```

---

## Handling callbacks

Your callback endpoint should:

1. **Respond HTTP `200` immediately** — do not wait for processing
2. **Process asynchronously** — handle business logic after acknowledgement
3. **Be idempotent** — use `guid` to deduplicate; the same event may arrive more than once
4. **Validate the signature** — reject any callback without a valid `X-BlockControl-Signature`

```javascript
// Node.js / Express example
app.post('/callbacks/blockcontrol', (req, res) => {
  res.status(200).send('OK'); // Acknowledge immediately

  const { event, data } = req.body;

  if (event === 'PAYMENT_STATUS_UPDATED' && data.status === 'CONFIRMED') {
    processConfirmedPayment(data.guid); // Handle async
  }
});
```

---

## Retry policy

If your endpoint returns a non-200 response or times out, BlockControl retries the callback with exponential backoff:

| Attempt | Delay |
|---|---|
| 1st retry | 30 seconds |
| 2nd retry | 5 minutes |
| 3rd retry | 30 minutes |
| 4th retry | 2 hours |

After 4 failed retries, the callback is marked as undelivered and no further attempts are made. You can retrieve the missed payment status at any time via [Get payment information](README.md#get-payment-information).
