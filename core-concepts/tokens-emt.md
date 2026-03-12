# Tokens & EMT

An **Electronic Money Token (EMT)** is a type of crypto-asset that purports to maintain a stable value by referencing the value of an official currency. In BlockControl, EMTs are ERC-20-compatible smart contracts deployed on Arbitrum, issued by licensed EMIs under MiCAR (Markets in Crypto-Assets Regulation).

---

## What is an EMT?

Under MiCAR Article 3(1)(7), an EMT is defined as a crypto-asset that:
- References a single official currency (e.g., EUR)
- Is issued only by credit institutions or licensed electronic money institutions
- Grants a claim on the issuer for redemption at par value

BlockControl's EMT implementation ensures that every token deployed through the platform adheres to these requirements via smart contract design and access control enforcement.

---

## Token Lifecycle

```
Deploy → Mint → Distribute → Transfer → Burn
```

### 1. Deploy
An **Admin** account deploys a new token contract via the token factory. Token creation requires:
- Token name and symbol
- Associated counterparty (issuer)
- Paymaster assignment (for gas sponsorship)

### 2. Mint
An **Issuer** account (assigned the Minter role in the token) mints new tokens. Minted tokens can only be sent to a **Distributor** account — they cannot be minted directly to client accounts.

### 3. Distribute
A **Distributor** account moves tokens to clients or across counterparties. This step represents the commercial distribution of the EMT.

### 4. Transfer
**Client** and **Admin** accounts can transfer tokens between accounts within the platform, or withdraw to external addresses (subject to account type rules).

### 5. Burn
An **Issuer** account (assigned the Burner role) destroys tokens, reducing the total supply. Burning is typically triggered by redemption requests.

---

## On-Chain Token Roles

Each token has a set of on-chain roles that can be assigned to accounts. These roles are enforced at the smart contract level and cannot be bypassed at the application layer.

| Role | Description |
|---|---|
| **Admin** | Can deploy tokens, assign/revoke roles, transfer funds |
| **Minter** | Can create (mint) new token supply |
| **Burner** | Can destroy (burn) token supply |
| **Custodian** | Can freeze/unfreeze individual accounts |
| **Limiter** | Can set transfer limits on accounts |
| **Pauser** | Can pause all token transfers globally |

{% hint style="warning" %}
Accounts with the **Pauser** role can halt all transfers for a token. This is intended for emergency use only and should be assigned to Operator accounts with strict access controls.
{% endhint %}

---

## Partner Token Import

Counterparties can import tokens issued by partner counterparties. This enables inter-counterparty settlements using a shared token without the receiving counterparty needing to be the issuer.

**Import flow:**
1. A confirmed partnership must exist between the two counterparties
2. Navigate to the partner in the Partnerships section
3. Select the token to import
4. The token appears in the counterparty's token list and is available for cross-counterparty transfers

---

## Token Properties

| Field | Description |
|---|---|
| **Name** | Full token name (e.g., "Euro Stablecoin") |
| **Symbol** | Ticker symbol (e.g., FEUR) |
| **Contract Address** | On-chain Arbitrum address |
| **Total Supply** | Current circulating supply |
| **Issuer** | Counterparty that deployed the token |
| **Paymaster** | Assigned paymaster for gas sponsorship |
