# Integration Plan - Business

The following outlines the business-level steps required for a successful integration between your institution and BlockControl.

---

## Phase 1: Pre-Integration (Days 1–5)

| # | Owner | Action | Outcome |
|---|---|---|---|
| 1 | Bank / EMI Legal | Review and sign the **Master Services Agreement** with BlockControl | Signed MSA |
| 2 | Bank / EMI Compliance | Confirm EMI license scope covers EMT issuance under MiCAR | Compliance sign-off |
| 3 | Bank / EMI Business | Define the token structure: currency, symbol, initial supply, distribution model | Token design document |
| 4 | BlockControl Business | Assign a dedicated integration manager | Named contact provided |
| 5 | Bank / EMI Business | Provide institution details for counterparty registration: name, alias, license number, jurisdiction | Onboarding form submitted |

---

## Phase 2: Technical Onboarding (Days 6–18)

| # | Owner | Action | Outcome |
|---|---|---|---|
| 6 | BlockControl Admin | Register counterparty in the BlockControl platform with `asset_code` | Counterparty visible in UI |
| 7 | Bank IT Team | Complete technical integration steps (see [Integration Plan - Technical Team](integration-plan-technical-team.md)) | Stage environment operational |
| 8 | Bank / EMI Business | Nominate first employee and provide email address to BlockControl Admin | First employee created |
| 9 | Bank officer | Complete [Basic use case](../basic-use-case.md) in Stage environment | First payment confirmed |

---

## Phase 3: UAT & Go-Live (Days 19–28)

| # | Owner | Action | Outcome |
|---|---|---|---|
| 10 | Bank IT Team | Execute full integration test scenario (all transfer types, error cases, reconciliation) | UAT sign-off |
| 11 | BlockControl DevOps | Provision production infrastructure for the counterparty | Production environment ready |
| 12 | Bank IT Team | Deploy integration to production | Live |
| 13 | Bank / EMI Business | Conduct operational readiness review | Go/No-Go decision |
| 14 | Both parties | Go-live | First production transaction |

{% hint style="info" %}
The whole integration process takes **14–28 days** (without signing a contract). Timeline varies based on bank IT team availability and internal approval processes.
{% endhint %}
