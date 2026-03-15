# Multisignature

BlockControl supports a **four-eyes approval mechanism** for sensitive or high-value payment operations. This requires two independent parties to co-sign a transaction before it is broadcast to the Arbitrum network — preventing any single operator from unilaterally executing critical operations.

---

## How it works

When multisig is enabled for a counterparty, all payment operations go through a two-step approval flow:

```
Step 1: Initiator creates and submits the payment
              ↓
Step 2: Approver reviews and co-signs the payment
              ↓
Step 3: BlockControl broadcasts the signed transaction to Arbitrum
              ↓
Step 4: On-chain confirmation → status: CONFIRMED
```

If the approver **rejects** the payment, it is cancelled and the initiator is notified. The transaction is never broadcast.

---

## Roles

| Role | Description |
|---|---|
| **Initiator (Editor)** | Creates the payment request via API or UI. Cannot finalize without co-signer approval. |
| **Approver (CoSigner)** | Reviews the payment details and either approves or rejects. Operates via Dfns / Fireblocks CoSigner module. |

{% hint style="warning" %}
The Approver role must be configured and the CoSigner service must be running before enabling multisig. Payments will remain in `PENDING_APPROVAL` status indefinitely if the CoSigner is unreachable.
{% endhint %}

---

## Enabling multisig for your counterparty

Multisig is configured per counterparty during onboarding. To enable it:

1. Inform your BlockControl integration manager during the [Integration Plan - Business](integration-plan-business.md) phase
2. BlockControl Admin configures the CoSigner callback URL and role mapping for your counterparty
3. Deploy your CoSigner service and provide the callback URL to BlockControl

---

## Payment status with multisig enabled

When multisig is active, the payment status lifecycle includes an additional state:

| Status | Description |
|---|---|
| `CREATED` | Payment draft created, awaiting co-signer approval |
| `PENDING_APPROVAL` | Submitted to CoSigner, awaiting approval |
| `APPROVED` | Co-signed; queued for on-chain broadcast |
| `SUBMITTED` | Broadcast to Arbitrum, awaiting confirmation |
| `CONFIRMED` | Confirmed on-chain |
| `REJECTED` | Rejected by co-signer — payment cancelled |
| `FAILED` | On-chain transaction reverted |

---

## API: Approve a payment (CoSigner callback)

BlockControl calls the following endpoint on your CoSigner service when a payment requires approval:

```http
POST {your_cosigner_callback_url}
Content-Type: application/json

{
  "payment_guid": "37f6b441-0906-46b6-9349-e742531f4eb7",
  "counterparty_guid": "ac75f1a6-0abe-11ee-be56-0242ac120002",
  "amount": "10000.00",
  "asset_code": "FEUR",
  "sender_id": "7f971dc7-0fd2-4c5c-926d-75a6d6088535",
  "receiver_id": "d5085e37-1a50-11ee-bd5f-3a6aa4436fee",
  "initiated_by": "employee@mybank.com",
  "initiated_at": "2025-03-14T10:30:00Z"
}
```

Your CoSigner responds with:

```json
{ "decision": "APPROVED" }
```
or
```json
{ "decision": "REJECTED", "reason": "Exceeds daily limit" }
```

BlockControl proceeds or cancels based on this response.

---

## Security considerations

- The CoSigner callback URL must use `https://`
- BlockControl signs all outbound CoSigner requests with its private key — validate the signature using BlockControl's public key before acting on the request
- Log all approval and rejection decisions for audit purposes
