# API change log

This page tracks all breaking and non-breaking changes to the BlockControl REST API.

---

## v1.2.0 — 2025-03-01

### Added
- `POST /webhooks` — register a webhook endpoint for transaction status events
- `GET /transfers/{id}` — added `explorerUrl` field to response (direct Arbiscan link)
- `POST /transfers/inter-counterparty` — new endpoint for cross-counterparty transfers
- Async status values: `SUBMITTED` state added between `PENDING` and `CONFIRMED`

### Changed
- All transfer endpoints now return `202 Accepted` instead of `200 OK` when the operation is queued for on-chain processing

---

## v1.1.0 — 2025-02-01

### Added
- `POST /accounts/{id}/smart-account` — provision a smart account on Arbitrum for an existing account
- `POST /tokens/{id}/roles/grant` and `POST /tokens/{id}/roles/revoke` — on-chain role management
- `GET /accounts/{id}/balances` — real-time on-chain balance per token

### Changed
- Authentication endpoint moved from `/auth/session` to `/auth/login` (v1.0 endpoint deprecated, removed in v1.2)

---

## v1.0.0 — 2025-01-01

Initial public release. Covers:
- Authentication (JWT)
- Counterparty management
- Account management
- Token (EMT) lifecycle
- Internal and external transfers
- Invoice system
- User management (Employee)
