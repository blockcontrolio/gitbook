# Transfer System

BlockControl supports three types of token transfers, each with distinct routing rules and permission requirements. All transfers are processed asynchronously and confirmed on-chain via the Arbitrum network.

---

## Transfer Types

### Internal Transfer
A transfer between two accounts **within the same counterparty**.

- **Sender:** Admin or Client account
- **Recipient:** Any account within the same counterparty
- **Use case:** Moving tokens from distribution to client accounts, internal treasury management

### External Transfer
A transfer from a platform account to an **on-chain address outside the platform**.

- **Sender:** Admin or Client account (withdrawal)
- **Recipient:** Any valid Arbitrum wallet address
- **Use case:** Customer withdrawals to self-custodied wallets, on-chain settlement

{% hint style="warning" %}
Only **Admin** and **Client** accounts can send external transfers. Distributor accounts are not permitted to transfer to external addresses.
{% endhint %}

### Inter-Counterparty Transfer (Inter-Bank)
A transfer between accounts belonging to **two different counterparties** on the platform.

- **Sender:** Admin or Distributor account
- **Recipient:** Processing-type account of the partner counterparty
- **Prerequisite:** A confirmed partnership must exist between the two counterparties

**Use case:** Cross-bank settlements, correspondent banking flows, white-label distribution to partner institutions

---

## Initiating a Transfer

### Via Dashboard

1. Navigate to **Transfers → New Transfer**
2. Select transfer type: Internal / External / Inter-Counterparty
3. Fill in the required fields:
   - **From Account** — source account within your counterparty
   - **To Account / Address** — destination (account selector for internal/inter-bank, address input for external)
   - **Asset** — the token to transfer
   - **Amount**
4. Review and confirm

### Via API

**Internal Transfer:**
```http
POST /api/v1/transfers/internal
Authorization: Bearer {jwt_token}

{
  "fromAccountId": "acc_admin_001",
  "toAccountId": "acc_client_042",
  "tokenId": "tok_feur_001",
  "amount": "500.00"
}
```

**External Transfer:**
```http
POST /api/v1/transfers/external
Authorization: Bearer {jwt_token}

{
  "fromAccountId": "acc_client_042",
  "toAddress": "0xExternalWalletAddress",
  "tokenId": "tok_feur_001",
  "amount": "250.00"
}
```

**Inter-Counterparty Transfer:**
```http
POST /api/v1/transfers/inter-counterparty
Authorization: Bearer {jwt_token}

{
  "fromAccountId": "acc_distributor_010",
  "toCounterpartyId": "cpty_partner_bank",
  "toAccountId": "acc_processing_001",
  "tokenId": "tok_feur_001",
  "amount": "10000.00"
}
```

---

## Transfer Response

All transfer requests return immediately with a `PENDING` status. The actual on-chain execution is asynchronous.

```json
{
  "transferId": "txn_transfer_789",
  "type": "INTER_COUNTERPARTY",
  "status": "PENDING",
  "fromCounterparty": "My Bank",
  "fromAccount": "acc_distributor_010",
  "toCounterparty": "Partner Bank",
  "toAccount": "acc_processing_001",
  "token": "FEUR",
  "amount": "10000.00",
  "submittedAt": "2025-03-11T11:00:00Z",
  "transactionHash": null
}
```

Once confirmed on-chain, the status updates to `CONFIRMED` and `transactionHash` is populated. A webhook notification is sent if configured.

---

## Transfers List

The **Transfers** page provides a full paginated list of all transfers for the counterparty, with the following fields:

| Field | Description |
|---|---|
| **ID** | Unique transfer identifier |
| **From Counterparty** | Sending institution |
| **From Account** | Sending account |
| **To Counterparty** | Receiving institution |
| **To Account / Address** | Receiving account or external address |
| **Type** | Internal / External / Inter-Counterparty |
| **Asset** | Token transferred |
| **Amount** | Transfer amount |
| **Status** | Pending / Confirmed / Failed |
| **Hash** | Blockchain transaction hash (with explorer link) |
| **Datetime** | Timestamp of submission |

### Filtering

Transfers can be filtered by:
- Type (Internal, External, Inter-Counterparty)
- Status (Pending, Confirmed, Failed)
- Direction (Incoming, Outgoing)
- Date range
- Asset

---

## Incoming External Transactions

BlockControl listens to the blockchain for **external incoming transactions** — tokens sent to a platform wallet from an address outside the platform. These are automatically detected and recorded in the transfer history without any manual action required.
