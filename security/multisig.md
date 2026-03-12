# Multisig & Four-Eyes Check

BlockControl supports a **four-eyes principle** for high-value or sensitive operations. Using Fireblocks as the MPC provider, a second authorized party must co-sign a transaction before it is broadcast to the blockchain — preventing any single user from unilaterally executing critical operations.

---

## What is the Four-Eyes Principle?

The four-eyes principle requires that any significant action must be reviewed and approved by at least two independent people before execution. In financial services, this is a standard control for:

- High-value transfers
- Token minting above a threshold
- Role assignments (e.g., granting Minter or Admin roles)
- Paymaster ownership transfer

---

## Implementation: Fireblocks Editor + CoSigner

BlockControl implements the four-eyes check using two Fireblocks roles:

| Role | Responsibility |
|---|---|
| **Editor** | Constructs and initiates the transaction |
| **CoSigner** | Reviews and approves (co-signs) the transaction |

A transaction cannot be broadcast until both the Editor has initiated it **and** the CoSigner has approved it.

---

## Flow

```
1. Editor initiates operation (e.g., large mint) via BlockControl
      ↓
2. Transaction constructed and sent to Fireblocks in PENDING_APPROVAL state
      ↓
3. CoSigner receives notification in Fireblocks (app or API)
      ↓
4. CoSigner reviews transaction details
      ↓
5a. CoSigner APPROVES → transaction proceeds to MPC signing → broadcast
5b. CoSigner REJECTS → transaction cancelled, initiator notified
```

---

## Configuring Multisig

Fireblocks multisig configuration is managed in the Admin Panel under **Settings → MPC Provider → Fireblocks**.

Required configuration:

| Parameter | Description |
|---|---|
| **Editor API Key** | Fireblocks API key for the Editor role |
| **CoSigner Callback URL** | URL of your Fireblocks CoSigner service |
| **Network** | Arbitrum network configuration in Fireblocks |
| **Counterparty** | Fireblocks workspace counterparty mapping |

{% hint style="warning" %}
The Fireblocks CoSigner must be deployed and running before enabling multisig. Transactions will remain in PENDING_APPROVAL state indefinitely if the CoSigner is unreachable.
{% endhint %}

---

## Async Encryption

In addition to multisig, BlockControl supports **asymmetric encryption** (public/private key) for securing sensitive payloads in API communication.

- Public keys are exchanged during onboarding
- Sensitive request payloads can be encrypted with the recipient's public key
- Only the intended recipient (holding the private key) can decrypt the payload

This is particularly relevant for:
- Credential exchange during initial setup
- Sensitive configuration parameters
- Key material exchange in automated workflows
