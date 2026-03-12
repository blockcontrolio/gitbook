# Architecture

BlockControl follows a layered architecture that separates the off-chain platform logic from on-chain smart contract operations, connected through a MPC-secured wallet layer.

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────┐
│                  BlockControl UI                │
│          (Dashboard / Management Portal)        │
└────────────────────┬────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────┐
│              BlockControl API                   │
│   (REST / Async / Webhooks / Auth / RBAC)       │
└──────┬─────────────┬───────────────┬────────────┘
       │             │               │
┌──────▼──────┐ ┌────▼──────┐ ┌─────▼──────────┐
│  Auth Server│ │  Database │ │  Event / Queue  │
│ (JWT/Okta/  │ │ (Off-chain│ │  (Async ops,    │
│  Keycloak)  │ │  state)   │ │   webhooks)     │
└─────────────┘ └───────────┘ └────────────────┘
                     │
┌────────────────────▼────────────────────────────┐
│           MPC Wallet Layer (Dfns / WaaS)        │
│     (Key management, transaction signing)       │
└────────────────────┬────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────┐
│            Arbitrum Blockchain                  │
│   (EMT Smart Contracts, Token Factories,        │
│    Account Factories, Paymaster)                │
└─────────────────────────────────────────────────┘
```

---

## Key Architectural Decisions

### Smart Accounts (ERC-4337)
BlockControl uses **smart accounts** rather than standard EOA (Externally Owned Accounts). Smart accounts enable:
- On-chain role assignment (Minter, Burner, Pauser, etc.)
- Transaction sponsorship via Paymaster (gas abstraction)
- Programmable access control at the contract level

### Account Factory & Token Factory
New accounts and tokens are deployed via factory contracts. This enables:
- Standardized deployment across all counterparties
- Predictable contract addresses
- Admin-controlled provisioning without manual deployments

### Off-Chain vs On-Chain Roles
BlockControl separates concerns clearly:

| Layer | Role Type | Examples |
|---|---|---|
| **On-chain** | Token-level roles | Minter, Burner, Custodian, Limiter, Pauser, Admin |
| **Off-chain** | Platform-level roles | User, Admin, Read-Only |
| **Account type** | Structural roles | Issuer, Distributor, Operator, Client |

### MPC / Non-Custodial Key Management
All transaction signing goes through **Dfns**, a Wallet-as-a-Service provider using MPC (Multi-Party Computation). No single point holds a complete private key, ensuring institutional-grade security without HSM infrastructure.

### Async Transaction Processing
All blockchain operations (token creation, minting, transfers) are **asynchronous**. The API accepts the request, queues the operation, and notifies via webhooks when the transaction is confirmed on-chain.

---

## Data Flow: Token Transfer

```
1. API Request (REST)
      ↓
2. RBAC Check (user + account permissions)
      ↓
3. Business Logic Validation (balance, limits, counterparty rules)
      ↓
4. Transaction queued (Async)
      ↓
5. MPC Signing (Dfns)
      ↓
6. Broadcast to Arbitrum
      ↓
7. On-chain confirmation
      ↓
8. Webhook notification → Customer system
      ↓
9. UI / Transaction history updated
```
