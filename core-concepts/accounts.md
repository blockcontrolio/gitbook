# Accounts & Wallets

Accounts are on-chain smart wallets that belong to a counterparty. Each account has a **type** that defines its structural role in the token flow, and can hold **on-chain roles** within specific tokens depending on that type.

---

## Account Types

### ADMIN
The highest-privilege account type within a counterparty.

**Capabilities:**
- Deploy new EMT tokens
- Deploy account factories and token factories
- Assign and revoke on-chain roles (e.g., Minter, Burner) to other accounts in any token
- Transfer funds internally and to other counterparties

### ISSUER
Represents the issuance function of an EMI.

**Capabilities:**
- Can be assigned on-chain roles: **Minter** and **Burner** only
- Can only mint funds to a **Distributor** account
- Cannot transfer freely to arbitrary addresses

### OPERATOR
Represents a control or compliance function.

**Capabilities:**
- Can be assigned on-chain roles: **Limiter**, **Custodian**, **Pauser**
- Cannot hold any token balance
- Cannot send or receive transfers

### DISTRIBUTOR
Represents an account that moves tokens within or across counterparties.

**Capabilities:**
- Cannot transfer to external (non-platform) addresses
- Can transfer to: **Issuer**, **Client**, or another **Distributor** (cross-counterparty)
- Does not hold on-chain roles in tokens

### CLIENT
Represents an end-user or customer account.

**Capabilities:**
- Can transfer assets
- Cannot hold on-chain roles in any token
- Cannot interact directly with token lifecycle operations (minting, burning)

---

## Account Type Summary

| Account Type | On-chain Roles | Can Hold Balance | Can Transfer Externally | Can Deploy Tokens |
|---|---|---|---|---|
| ADMIN | ✅ Full | ✅ | ✅ | ✅ |
| ISSUER | ✅ Minter, Burner | ✅ | ❌ | ❌ |
| OPERATOR | ✅ Limiter, Custodian, Pauser | ❌ | ❌ | ❌ |
| DISTRIBUTOR | ❌ | ✅ | ❌ | ❌ |
| CLIENT | ❌ | ✅ | ✅ (withdraw only) | ❌ |

---

## Account Properties

| Field | Description |
|---|---|
| **Name** | Unique display name within the counterparty |
| **Reference (Ref)** | Optional reference identifier |
| **Type** | Account type (not editable after creation) |
| **On-chain Address** | Smart account address on Arbitrum |
| **Status** | Active or Disabled |

{% hint style="warning" %}
Account **Type** is set at creation and cannot be changed afterward. Plan your account structure carefully before provisioning.
{% endhint %}

---

## Creating an Account

1. Navigate to **Accounts** in the sidebar
2. Click **New Account**
3. Enter a unique account name (Reference is optional)
4. Select the account type
5. The platform provisions a smart account on-chain via the account factory

{% hint style="info" %}
Token creation is disabled if no Admin account exists for the counterparty. Ensure an Admin account is created before attempting to deploy tokens.
{% endhint %}

---

## Enabling and Disabling Accounts

Accounts can be disabled to suspend all operations without deleting history. Disabled accounts:
- Cannot send or receive transfers
- Retain their transaction history
- Can be re-enabled by a platform admin

A history of account status changes is maintained for audit purposes.
