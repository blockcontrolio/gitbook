# Invoice System

BlockControl includes a built-in invoice and payment request system that allows counterparties to create structured payment requests and process them via deeplink. This enables structured, trackable payment flows between parties — both within and across counterparties.

---

## Invoice Lifecycle

```
Create Invoice → Share / Deeplink → Payer Selects Account → Payment Processed → Status Updated → Notification Sent
```

---

## Creating an Invoice

Any user with operational permissions can create a payment invoice.

### Required Fields

| Field | Description |
|---|---|
| **Amount** | Requested payment amount |
| **Asset** | Token to be paid (e.g., FEUR) |
| **Counterparty Payer** | The counterparty expected to make the payment |
| **Destination Account** | The account that will receive the funds |

### Validation

The platform validates the invoice on creation:
- Amount must be greater than zero
- Asset must be an active token accessible to both parties
- Destination account must be active and capable of receiving the asset

### Via Dashboard

1. Navigate to **Invoices → New Invoice**
2. Fill in the invoice fields
3. Confirm — the invoice is created with a **PENDING** status

### Via API

```http
POST /api/v1/invoices
Authorization: Bearer {jwt_token}

{
  "amount": "1500.00",
  "tokenId": "tok_feur_001",
  "payerCounterpartyId": "cpty_partner_bank",
  "destinationAccountId": "acc_distributor_010"
}
```

**Response:**
```json
{
  "invoiceId": "inv_001",
  "amount": "1500.00",
  "token": "FEUR",
  "status": "PENDING",
  "payerCounterparty": "Partner Bank",
  "destinationAccount": "acc_distributor_010",
  "deeplinkUrl": "https://app.blockcontrol.io/pay/inv_001",
  "createdAt": "2025-03-11T10:00:00Z"
}
```

---

## Processing a Payment (Payer Flow)

### Via Deeplink

The invoice creator shares the `deeplinkUrl` with the payer. Opening the deeplink:

1. Navigates the payer directly to the payment screen
2. Pre-fills the invoice details (amount, asset, destination)
3. Payer selects which of their accounts to pay from
4. Payer confirms the transfer

### Status Updates

After the payer confirms:
- Invoice status changes to **AWAITING** (transfer submitted, pending on-chain confirmation)
- Once the blockchain confirms the transaction, status changes to **COMPLETED**
- The invoice creator receives a notification when the invoice has been executed

---

## Invoice Statuses

| Status | Description |
|---|---|
| **PENDING** | Invoice created, not yet paid |
| **AWAITING** | Payment submitted, awaiting on-chain confirmation |
| **COMPLETED** | Payment confirmed on-chain |
| **CANCELLED** | Invoice manually cancelled |

---

## Invoice List

Navigate to **Invoices** in the sidebar to view all invoices for your counterparty.

### Columns

| Column | Description |
|---|---|
| **ID** | Invoice identifier |
| **Amount** | Requested amount |
| **Asset** | Token |
| **Status** | Current invoice status |
| **Payer** | Counterparty expected to pay |
| **Destination** | Receiving account |
| **Created** | Creation timestamp |

### Filtering

Invoices can be filtered by status, payer counterparty, asset, and date range.

---

## Notifications

When an invoice is executed (payment confirmed), a platform notification is sent to the invoice creator. If webhooks are configured, a `INVOICE_COMPLETED` webhook event is also dispatched to the registered URL.
