# EMT Management

BlockControl provides a complete lifecycle management system for Electronic Money Tokens (EMTs). From creation and issuance to burning and supply monitoring, all operations are handled through a unified interface and API.

---

## Creating an EMT

Token creation requires an **Admin** account to exist for the counterparty. The Admin account must be provisioned and confirmed before initiating token deployment.

### Steps

1. Navigate to **Assets → Tokens**
2. Click **Create Token**
3. Fill in the token details:
   - **Name** — Full token name (e.g., "Euro Stablecoin")
   - **Symbol** — Ticker (e.g., FEUR)
4. Select the **Paymaster** account to sponsor gas fees for token operations
5. Confirm — the token contract is deployed via the Token Factory on Arbitrum

{% hint style="info" %}
Token deployment is an asynchronous operation. The UI will show a "Deploying" status until the contract is confirmed on-chain. This typically takes a few seconds on Arbitrum.
{% endhint %}

---

## Assigning Token Roles After Deployment

Once deployed, on-chain roles must be assigned before the token can be used:

1. Go to the token detail page
2. Navigate to **Roles**
3. Assign the **Minter** role to an Issuer account
4. Assign the **Burner** role to an Issuer account
5. Optionally assign Custodian, Limiter, and Pauser to Operator accounts

---

## Minting (Issuance)

Minting increases the total supply of the token. Only accounts with the **Minter** role can mint.

**Rules:**
- Minted tokens can only be sent to a **Distributor** account
- The minting account must be of type **Issuer**
- Each mint is a blockchain transaction (asynchronous, gas-sponsored via Paymaster)

### Mint via Dashboard

1. Navigate to **Assets → Tokens → [Token Name]**
2. Click **Mint**
3. Enter the amount and select the destination Distributor account
4. Confirm

### Mint via API

```http
POST /api/v1/tokens/{tokenId}/mint
Content-Type: application/json
Authorization: Bearer {jwt_token}

{
  "amount": "10000.00",
  "destinationAccountId": "acc_distributor_123"
}
```

**Response:**
```json
{
  "transactionId": "txn_abc123",
  "status": "PENDING",
  "amount": "10000.00",
  "token": "FEUR",
  "destination": "0xDistributorAddress"
}
```

---

## Burning (Redemption)

Burning reduces the total supply. Only accounts with the **Burner** role can burn tokens, typically triggered by a customer redemption request.

**Rules:**
- The burning account must be of type **Issuer** with the Burner role
- Burned tokens are permanently destroyed

### Burn via Dashboard

1. Navigate to **Assets → Tokens → [Token Name]**
2. Click **Burn**
3. Enter the amount to burn
4. Confirm

### Burn via API

```http
POST /api/v1/tokens/{tokenId}/burn
Content-Type: application/json
Authorization: Bearer {jwt_token}

{
  "amount": "5000.00",
  "sourceAccountId": "acc_issuer_456"
}
```

---

## Balance Tracking

BlockControl maintains **real-time balance synchronization** with the blockchain. Balances displayed in the UI and returned by the API reflect the current on-chain state.

- Balances update automatically after each confirmed transaction
- The platform also **listens for external incoming transactions** — if tokens are sent to a platform wallet from an external address, the balance is automatically detected and updated

### Balance via API

```http
GET /api/v1/accounts/{accountId}/balances
Authorization: Bearer {jwt_token}
```

**Response:**
```json
{
  "accountId": "acc_123",
  "balances": [
    {
      "token": "FEUR",
      "tokenAddress": "0xTokenContract",
      "amount": "25000.00",
      "lastUpdated": "2025-03-11T10:30:00Z"
    }
  ]
}
```

---

## Token Detail Page

Each token has a dedicated detail page showing:
- Contract address (with explorer link)
- Total supply
- Assigned roles and their account holders
- Transaction history for this token
- Mint and burn actions (if user has permissions)
