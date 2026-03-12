# Blockchain Explorer Integration

BlockControl integrates with the Arbitrum blockchain explorer (Arbiscan) to provide direct on-chain verification of all transactions, wallets, and token contracts — without leaving the platform.

---

## What You Can Look Up

| Object | Explorer Link Location |
|---|---|
| Transaction hash | Transfer detail page → **Hash** field |
| Wallet / Account address | Account detail page → **On-chain Address** |
| Token contract | Token detail page → **Contract Address** |

Every hash or address shown in the platform is a **clickable link** that opens the corresponding Arbiscan page in a new tab.

---

## Why It Matters

Explorer integration provides:

- **Independent verification** — Anyone can verify a transaction on-chain without relying solely on the platform's records
- **Audit trail** — Compliance teams can cross-reference platform data with public blockchain data
- **Transparency** — Clients and counterparties can verify settlement without needing platform access

---

## Explorer Links in the API

Transaction responses include a direct explorer URL:

```json
{
  "transferId": "txn_abc123",
  "status": "CONFIRMED",
  "transactionHash": "0xabc123...",
  "explorerUrl": "https://arbiscan.io/tx/0xabc123..."
}
```
