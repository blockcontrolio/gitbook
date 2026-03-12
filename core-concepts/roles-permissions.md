# Roles & Permissions

BlockControl implements a **two-layer Role-Based Access Control (RBAC)** model. The first layer governs what platform **users** can do. The second layer governs what **accounts** can do on-chain. These layers are independent but complementary.

---

## Layer 1: User Roles

User roles control access to platform operations — creating accounts, initiating transfers, managing users, and so on.

### ADMIN (User)
The platform superuser for a counterparty.

**Can:**
- Perform all operations available to a regular User
- Create new users under the counterparty
- Assign and change roles for other users
- Grant permissions to other users
- Manage accounts and platform settings

### USER
The standard operational role.

**Can:**
- Perform all operational tasks (initiate transfers, view accounts, manage invoices)
- Interact with any account under the counterparty

**Cannot:**
- Create or manage other users
- Change user roles or permissions

### READ-ONLY USER
A view-only role for auditors, compliance officers, or observers.

**Can:**
- View all data: accounts, transactions, tokens, invoices

**Cannot:**
- Perform any write or modify operation

---

## Layer 2: Account / On-Chain Roles

Account roles are assigned at the **token level** and enforced on-chain. An account's role within a token defines what it can do with that specific token.

| Role | Assigned To | Capabilities |
|---|---|---|
| **Admin** | Admin accounts | Deploy tokens, assign/revoke roles, transfer |
| **Minter** | Issuer accounts | Mint new token supply |
| **Burner** | Issuer accounts | Burn (destroy) token supply |
| **Custodian** | Operator accounts | Freeze/unfreeze individual balances |
| **Limiter** | Operator accounts | Set transaction limits |
| **Pauser** | Operator accounts | Pause all token transfers |

{% hint style="info" %}
On-chain roles are **per token** — an Issuer account can be a Minter for Token A but not Token B unless explicitly assigned.
{% endhint %}

---

## Permission Model: Least Privilege

BlockControl follows a **least privilege** design. The permission matrix follows the pattern:

```
User → Action → Resource
User → Action → Account → Action (on-chain)
```

This means:
- A User can only act on resources they have been granted access to
- An Account can only perform on-chain operations matching its assigned token roles
- Permissions are never inherited implicitly — they must be explicitly assigned

---

## Transfer Permissions by Account Type

Not all account types can send or receive all transfer types. The table below summarizes transfer capabilities:

| Account Type | Internal Transfer | External Transfer | Inter-Counterparty |
|---|---|---|---|
| ADMIN | ✅ | ✅ | ✅ |
| ISSUER | ✅ (to Distributor only) | ❌ | ❌ |
| OPERATOR | ❌ | ❌ | ❌ |
| DISTRIBUTOR | ✅ | ❌ | ✅ (to processing accounts) |
| CLIENT | ✅ | ✅ (withdraw only) | ❌ |

---

## Role Assignment Flow

### Assigning an On-Chain Role to an Account

1. Navigate to **Accounts** and select the target account
2. Click **Manage Roles**
3. Select the token for which you want to assign the role
4. Choose the role (e.g., Minter)
5. Confirm — the role assignment is submitted as a blockchain transaction

{% hint style="warning" %}
Role assignment and revocation are on-chain operations. They require gas and are processed asynchronously. The account will show a "Pending" state until the transaction is confirmed.
{% endhint %}

### Revoking an On-Chain Role

The same flow as assignment — select the existing role and click **Revoke**. This triggers an on-chain revocation transaction.

---

## Policy Enforcement Points

RBAC policies are enforced at the following API endpoints:

- `POST /accounts` — Create account
- `POST /accounts/{id}/smart-account` — Create smart account
- `POST /tokens/{id}/roles/grant` — Grant on-chain role
- `POST /tokens/{id}/roles/revoke` — Revoke on-chain role
- `POST /transfers/internal` — Internal transfer
- `POST /transfers/external` — External transfer
- `POST /transfers/inter-counterparty` — Inter-counterparty transfer
- All other token operations (mint, burn, pause, etc.)
