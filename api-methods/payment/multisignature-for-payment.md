# Multisignature for payment

This page describes how the four-eyes approval flow works specifically for payment operations.

For general multisig architecture and setup, see [Multisignature](../../general/integration-plan/multisignature.md).

---

## When does multisig apply to payments?

When multisig is enabled for your counterparty, it applies to the **Confirm payment** step. The payment creation (`POST /payments`) proceeds normally, but the confirmation is held in `PENDING_APPROVAL` status until the CoSigner approves.

---

## Payment status lifecycle with multisig

```
CREATED
   ↓
(confirm called)
   ↓
PENDING_APPROVAL  ← Waiting for CoSigner
   ↓             ↓
APPROVED        REJECTED → Payment cancelled
   ↓
PENDING (queued for Arbitrum broadcast)
   ↓
SUBMITTED (broadcast to Arbitrum)
   ↓
CONFIRMED (on-chain confirmation received)
```

---

## Configuring multisig for payments

### Option A: Dfns CoSigner (recommended)

BlockControl integrates with **Dfns** as the default WaaS provider. Dfns provides a built-in co-signing module where you define approval policies:

- Minimum amount threshold requiring co-sign
- Account types requiring co-sign (e.g., inter-counterparty only)
- Designated co-signers (by employee GUID or role)

Configure this in the BlockControl Admin Panel under **Settings → MPC Provider → Approval Policy**.

### Option B: Custom CoSigner endpoint

If your institution requires co-signing through its own internal system, provide a callback URL to BlockControl during onboarding. BlockControl will call this URL for approval before processing the payment.

See [Multisignature](../../general/integration-plan/multisignature.md#api-approve-a-payment-cosigner-callback) for the callback specification.

---

## Checking approval status

Poll the payment endpoint to check if approval has been received:

```http
GET /api/v1/counterparty/{counterparty_guid}/payments/{payment_guid}
Authorization: Bearer ${session_token}
```

Look for `"status": "PENDING_APPROVAL"` to know approval is outstanding, or `"status": "APPROVED"` to know it has been approved and is queued for processing.

---

## Rejecting a payment (CoSigner)

If the CoSigner rejects a payment, the status changes to `REJECTED` and the payment is permanently cancelled. A new payment must be created if the transfer is still required.

```json
{
  "guid": "37f6b441-0906-46b6-9349-e742531f4eb7",
  "status": "REJECTED",
  "rejection_reason": "Exceeds daily limit",
  "updated_at": "2025-03-14T11:00:00Z"
}
```
