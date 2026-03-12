# Platform Overview

BlockControl is a B2B SaaS platform that provides white-label stablecoin infrastructure for European banks and EMIs. It abstracts the complexity of blockchain deployment, smart contract management, and regulatory compliance into a unified API and dashboard.

---

## Core Value Proposition

Traditional banks and EMIs face a choice when entering the digital asset space: build blockchain infrastructure from scratch (expensive, slow, high risk) or rely on third-party issuers (loss of brand, margin, and control). BlockControl provides a third path — **infrastructure-as-a-service for stablecoin issuance under your own license and brand**.

---

## Platform Components

### 1. Smart Contract Layer
BlockControl deploys and manages ERC-20-compatible Electronic Money Token (EMT) smart contracts on Arbitrum. Each token is fully customizable and bound to the issuing institution's compliance policies.

### 2. Account & Counterparty Management
A structured hierarchy of counterparties (issuers, distributors, clients) and accounts (with on-chain and off-chain roles) governs who can do what within the system.

### 3. Transfer Engine
Supports three transfer types:
- **Internal** — within the same counterparty
- **External** — to an on-chain address outside the platform
- **Inter-Counterparty (Inter-Bank)** — between two counterparties on the platform

### 4. RBAC & Compliance Controls
Role-Based Access Control operates at two levels:
- **User roles** — govern what platform users can do (Admin, User, Read-Only)
- **Account/token roles** — govern what smart accounts can do on-chain (Minter, Burner, Custodian, Limiter, Pauser, Admin)

### 5. MPC Wallet Infrastructure
BlockControl integrates with **Dfns** (Wallet-as-a-Service provider) for MPC-based key management. This ensures non-custodial, institutionally secure wallet operations without managing private keys directly.

### 6. Integration Layer
REST API with full Swagger documentation, async event processing, webhooks, and Java SDK for integration with existing banking core systems.

---

## Milestone Roadmap

| Milestone | Focus | Status |
|---|---|---|
| **M01** | Core platform: EMT lifecycle, transfer engine, RBAC, dashboard, MPC integration | In Progress |
| **M02** | Integration: API framework, webhooks, async API, SDK, developer portal, admin panel | Planned |
