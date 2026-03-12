# Webhooks

BlockControl supports webhooks to notify your systems of important platform events — most critically, transaction status updates. Rather than polling the API, configure a webhook endpoint and receive real-time push notifications when events occur.

---

## Supported Events

| Event Type | Description |
|---|---|
| `TRANSACTION_STATUS_UPDATED` | Fired when a transfer or on-chain operation changes status (e.g., PENDING → CONFIRMED) |
| `INVOICE_COMPLETED` | Fired when an invoice payment is confirmed on-chain |

Additional events will be added in future releases.

---

## Registering a Webhook

Only users with the **Admin** role can register webhooks.

### Via Dashboard

1. Navigate to **Settings → Webhooks**
2. Click **New Webhook**
3. Select the **Event Type** (e.g., `TRANSACTION_STATUS_UPDATED`)
4. Enter your **Endpoint URL** (must be a valid `https://` URL)
5. Save

### Via API

```http
POST /api/v1/webhooks
Authorization: Bearer {jwt_token}

{
  "eventType": "TRANSACTION_STATUS_UPDATED",
  "url": "https://yourbank.com/webhooks/blockcontrol"
}
```

**Response:**
```json
{
  "webhookId": "wh_001",
  "eventType": "TRANSACTION_STATUS_UPDATED",
  "url": "https://yourbank.com/webhooks/blockcontrol",
  "status": "ACTIVE",
  "createdAt": "2025-03-11T10:00:00Z"
}
```

---

## Webhook Payload

When an event fires, BlockControl sends an HTTP `POST` request to your registered URL with a JSON body.

### `TRANSACTION_STATUS_UPDATED` Payload

```json
{
  "event": "TRANSACTION_STATUS_UPDATED",
  "timestamp": "2025-03-11T11:05:00Z",
  "data": {
    "transactionId": "txn_transfer_789",
    "status": "CONFIRMED",
    "previousStatus": "PENDING",
    "transactionHash": "0xabc123...",
    "explorerUrl": "https://arbiscan.io/tx/0xabc123..."
  }
}
```

### `INVOICE_COMPLETED` Payload

```json
{
  "event": "INVOICE_COMPLETED",
  "timestamp": "2025-03-11T11:10:00Z",
  "data": {
    "invoiceId": "inv_001",
    "transactionId": "txn_transfer_790",
    "status": "COMPLETED",
    "amount": "1500.00",
    "token": "FEUR"
  }
}
```

---

## Handling Webhooks on Your Server

Your webhook endpoint should:

1. **Return HTTP 200** immediately upon receipt — BlockControl considers any non-200 response a delivery failure
2. **Process asynchronously** — do not block the response while processing the event
3. **Be idempotent** — the same event may be delivered more than once in edge cases; use `transactionId` or `invoiceId` to deduplicate

### Example (Node.js / Express)

```javascript
app.post('/webhooks/blockcontrol', (req, res) => {
  // Acknowledge receipt immediately
  res.status(200).send('OK');

  // Process asynchronously
  const { event, data } = req.body;
  
  if (event === 'TRANSACTION_STATUS_UPDATED' && data.status === 'CONFIRMED') {
    handleConfirmedTransaction(data.transactionId);
  }
});
```

---

## URL Validation

BlockControl validates that the registered webhook URL:
- Is a valid, well-formed URL
- Uses `https://` (plain `http://` is not accepted)

{% hint style="warning" %}
Ensure your webhook endpoint is publicly reachable from BlockControl's servers. Endpoints behind a private network or VPN will not receive events unless explicitly whitelisted.
{% endhint %}

---

## Managing Webhooks

```http
# List all webhooks
GET /api/v1/webhooks

# Delete a webhook
DELETE /api/v1/webhooks/{webhookId}
```
