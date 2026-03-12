# Admin Panel

The BlockControl Admin Panel is a separate management interface for platform administrators. Unlike the standard counterparty dashboard, the Admin Panel provides global visibility and control across all counterparties, networks, and system configuration.

---

## Access

The Admin Panel is accessible to users with the **Platform Admin** role — a super-admin role distinct from the counterparty-level Admin role. Platform Admins are provisioned by the BlockControl team during onboarding.

```
https://admin.blockcontrol.io
```

---

## Counterparty Management

View and manage all counterparties registered on the platform.

### Actions Available
- **Create Counterparty** — Register a new bank or EMI on the platform
- **View Counterparty** — See all accounts, users, tokens, and transactions for any counterparty
- **Edit Counterparty** — Update counterparty type, name, or configuration
- **Disable/Enable Counterparty** — Suspend all operations for a counterparty
- **View All Users** — See users across all counterparties (with filtering)

### Counterparty Detail View

For each counterparty, admins can see:
- Type (EMI / LSP)
- License status
- Associated accounts and their types
- Active tokens
- Transaction volume
- User list

---

## Network Management

Configure the blockchain networks available on the platform.

### Actions Available
- **Add Network** — Register a new network (e.g., Arbitrum mainnet, testnet, L2)
- **Edit Network** — Update RPC URL, chain ID, explorer URL
- **Enable/Disable Network** — Toggle network availability for counterparties

### Network Configuration Fields

| Field | Description |
|---|---|
| **Name** | Display name (e.g., "Arbitrum One") |
| **Chain ID** | EVM chain identifier |
| **RPC URL** | JSON-RPC endpoint |
| **Explorer URL** | Block explorer base URL |
| **Status** | Active / Inactive |

---

## User Management (Global)

Platform admins can view and manage users across all counterparties:

- View all users with their counterparty, role, and last active timestamp
- Update user roles if needed
- Disable users across the platform

---

## MPC Provider Configuration

Configure and validate MPC provider settings per counterparty or globally.

### Fireblocks Configuration

| Field | Description |
|---|---|
| **API Key** | Fireblocks Editor API key |
| **CoSigner URL** | CoSigner service callback URL |
| **Network** | Fireblocks network configuration |
| **Counterparty** | Fireblocks workspace counterparty |

A **validation step** is available to test connectivity to Fireblocks before saving configuration.

---

## Deployment

The Admin Panel is deployed on AWS with the rest of the BlockControl infrastructure. Access is restricted to authorized IP ranges and requires MFA.

{% hint style="info" %}
The Admin Panel is a Milestone 02 deliverable. In Milestone 01, platform administration is performed directly by the BlockControl team.
{% endhint %}
