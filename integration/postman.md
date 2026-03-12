# Postman Collections

BlockControl provides a ready-to-use Postman collection covering all API endpoints. Use it to explore the API, test integrations, and prototype workflows without writing code.

---

## Importing the Collection

1. Download the collection file:
   ```
   https://api.blockcontrol.io/postman/blockcontrol-collection.json
   ```
2. Open Postman → **Import**
3. Select the downloaded file or paste the URL directly
4. The collection appears in your Postman workspace

---

## Environments

The collection uses Postman **environment variables** to manage configuration. Two environments are provided:

| Environment | Base URL |
|---|---|
| **Sandbox** | `https://sandbox.api.blockcontrol.io/api/v1` |
| **Production** | `https://api.blockcontrol.io/api/v1` |

### Setting Up an Environment

1. Import the environment file:
   ```
   https://api.blockcontrol.io/postman/blockcontrol-env-sandbox.json
   ```
2. In Postman, select the **Sandbox** environment from the top-right dropdown
3. Set your credentials:
   - `email` — your user email
   - `password` — your user password

The `accessToken` variable is automatically populated by the **Login** request using a Postman test script.

---

## Collection Structure

```
BlockControl API
├── Auth
│   ├── Login              ← Run this first to get your token
│   └── Refresh Token
├── Counterparties
│   ├── List Counterparties
│   └── Get Counterparty
├── Accounts
│   ├── List Accounts
│   ├── Create Account
│   ├── Get Account
│   └── Get Balances
├── Tokens
│   ├── List Tokens
│   ├── Create Token
│   ├── Mint Token
│   ├── Burn Token
│   └── Grant Role
├── Transfers
│   ├── List Transfers
│   ├── Internal Transfer
│   ├── External Transfer
│   └── Inter-Counterparty Transfer
├── Invoices
│   ├── List Invoices
│   ├── Create Invoice
│   └── Pay Invoice
└── Webhooks
    ├── List Webhooks
    ├── Register Webhook
    └── Delete Webhook
```

---

## Auto-Authentication

The **Login** request includes a Postman test script that automatically saves the `accessToken` to your environment after a successful login. All other requests in the collection use `{{accessToken}}` in their Authorization header, so you only need to log in once per session.

---

## Running the Full Integration Flow

Use Postman's **Collection Runner** to execute the full integration flow end-to-end:

1. Login
2. Create Account (Admin type)
3. Create Token
4. Grant Minter role to Issuer account
5. Mint tokens to Distributor account
6. Internal Transfer to Client account
7. Create Invoice
8. Pay Invoice

This flow validates that your environment is correctly configured and all operations work as expected.
