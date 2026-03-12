# Transaction Sponsorship & Paymaster

BlockControl implements **ERC-4337 Paymaster** support, enabling gas fee sponsorship for all smart account operations. This means your users and accounts never need to hold ETH to pay for blockchain transactions — the institution sponsors all gas costs transparently.

---

## What is a Paymaster?

In the ERC-4337 (Account Abstraction) standard, a Paymaster is a smart contract that pays gas fees on behalf of other accounts. BlockControl deploys and manages Paymaster contracts so that:

- End users never see gas fees
- Institutions control and budget gas costs centrally
- Token operations work seamlessly without requiring ETH in every wallet

---

## Paymaster Assignment

Each EMT token must have a Paymaster assigned at the time of creation. The Paymaster covers gas for all operations related to that token.

### Assigning a Paymaster

1. When creating a new token, select a Paymaster from the dropdown
2. The Paymaster must be pre-deployed and funded with ETH

If no Paymaster is assigned, token operations requiring on-chain execution will fail.

---

## Paymaster Management

### Transferring Paymaster Ownership

Paymaster ownership can be transferred to another account (e.g., when rotating admin accounts or transferring control to a counterparty):

1. Navigate to **Settings → Paymasters**
2. Select the Paymaster to transfer
3. Click **Transfer Ownership**
4. Enter the new owner's account address
5. Confirm — ownership is transferred via an on-chain transaction

{% hint style="warning" %}
Transferring Paymaster ownership is irreversible from the old owner's perspective. Ensure the new owner address is correct before confirming.
{% endhint %}

---

## Paymaster Funding

Paymasters must maintain an ETH balance to sponsor transactions. BlockControl provides visibility into Paymaster ETH balances in the Admin Panel.

If a Paymaster balance falls below a threshold:
- Operations linked to that Paymaster will fail
- An alert is shown in the dashboard
- Top up the Paymaster by sending ETH to its contract address

---

## Token Creation and Paymaster

Token creation itself is also a gas-sponsored operation. The Paymaster assigned to the Admin account covers the gas for the token factory deployment transaction.

---

## API: Paymaster Operations

**Get Paymaster details:**
```http
GET /api/v1/paymasters/{paymasterId}
Authorization: Bearer {jwt_token}
```

**Transfer ownership:**
```http
POST /api/v1/paymasters/{paymasterId}/transfer-ownership
Authorization: Bearer {jwt_token}

{
  "newOwnerAddress": "0xNewOwnerAddress"
}
```
