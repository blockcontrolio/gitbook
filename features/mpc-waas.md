# MPC / Wallet-as-a-Service

BlockControl integrates with **Dfns**, a Wallet-as-a-Service (WaaS) provider, to deliver MPC-based (Multi-Party Computation) key management for all on-chain operations. This eliminates the need for any party — including BlockControl — to ever hold a complete private key.

---

## What is MPC?

Multi-Party Computation splits a private key into cryptographic shares distributed across multiple independent parties. A transaction can only be signed when a threshold of parties cooperate — and the full private key is never reconstructed in any single location.

This provides:
- **No single point of compromise** — an attacker would need to compromise multiple independent parties simultaneously
- **HSM-grade security without HSM hardware** — institutional security delivered as API
- **Non-custodial architecture** — BlockControl does not control your keys

---

## Dfns Integration

### End-to-End Flow

All smart account operations in BlockControl — token creation, minting, burning, transfers, role assignments — are signed via Dfns:

```
BlockControl API
      ↓
Transaction constructed
      ↓
Sent to Dfns for MPC signing
      ↓
Signed transaction broadcast to Arbitrum
      ↓
On-chain confirmation returned
```

### Supported Operations

All blockchain operations are covered by the Dfns integration:

- Smart account creation
- Token deployment (via Token Factory)
- Token minting
- Token burning
- All transfer types (internal, external, inter-counterparty)
- On-chain role assignment and revocation
- Paymaster ownership transfer

---

## Fireblocks Support (Milestone 02)

In Milestone 02, BlockControl adds support for **Fireblocks** as an alternative MPC provider, with two roles:

| Role | Description |
|---|---|
| **Editor** | Can initiate and construct transactions |
| **CoSigner** | Must co-sign before broadcast — enforces four-eyes check |

This enables multisig approval workflows for high-value or sensitive operations. See [Multisig & Four-Eyes Check](../security/multisig.md) for details.

### Fireblocks Setup Parameters

When onboarding with Fireblocks, the following must be configured:

- Counterparty assignment
- Network configuration
- Credential setup and validation

These are configured via the Admin Panel under **Settings → MPC Provider**.

---

## Security Considerations

- Dfns credentials are stored server-side and never exposed to the frontend or API responses
- All MPC operations are logged for audit purposes
- Key shares are distributed and cannot be exported or reconstructed

{% hint style="warning" %}
Never share Dfns API credentials in plaintext. Use environment variables or secrets management (AWS Secrets Manager, Vault, etc.) for all credential storage.
{% endhint %}
