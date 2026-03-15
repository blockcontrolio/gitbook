# Integration Plan - Technical Team

## Steps to integrate — Stage environment

The interaction between the counterparty and BlockControl Platform requires the implementation of certain technical solutions on both sides.

---

| # | Who | What | Outcome |
|---|---|---|---|
| 1 | **Bank IT Team** | Implement an authorization endpoint to authenticate reverse-validation requests from BlockControl. Specification available in [Bank authorization](../../../api-methods/authentication/bank-authorization.md). | Authorization endpoint ready for deployment |
| 2 | **BlockControl DevOps** | Send RSA public key for JWT signature verification to the bank | Bank IT Team has the public key |
| 3 | **BlockControl Admin** | Register the counterparty in the BlockControl platform with `asset_code = 'FEUR'` | Counterparty visible in UI |
| 4 | **BlockControl Admin** | Request DevOps to provision Stage infrastructure components | Jira task created |
| 5 | **BlockControl DevOps** | Deploy Stage instances for Frontend application and authentication module. Creates separate hosts: `backoffice-{alias}-stage` and `api-{alias}-stage` | Hosts accessible from the internet |
| 6 | **Bank IT Team** | Deploy authorization service with the provided public key. Provide a mechanism to create secrets for the bank officer. Review optional [Multisignature](multisignature.md) approach for payment approval. | Bank officer has access to secrets |
| 7 | **Bank officer** | Register secrets for key keeper accounts (required for emission and processing accounts) | `GET /key` returns secrets for the 2 required users |
| 8 | **Bank officer** | Prepare onboarding data and send to BlockControl Admin: URLs for authorization endpoints, bank alias, email address for the first employee | Data sent to BlockControl Admin |
| 9 | **BlockControl Admin** | [UI] Update counterparty attributes with system URLs. Create the first employee for the counterparty. | Employee record created |
| 10 | **BlockControl Admin** | Create emission account (Issuer type) and processing account (Distributor type) for the counterparty on Arbitrum | Emission and processing accounts deployed on-chain |
| 11 | **Bank officer** | Set up password for the first employee | Employee can log in to the UI |
| 12 | **Bank System operator** | [API] Create a request to issue tokens on the emission account | Request sent to BlockControl |
| 13 | **BlockControl Admin** | [API] Confirm the issued amount and [transfer](../../../api-methods/payment/README.md#create-payment) it to the processing account of the bank | Assets available on processing account |
| 14 | **Bank officer** | Create secrets for customer accounts | Secrets ready |
| 15 | **Bank System operator** | [API] [Create customers](../../../api-methods/customer/create-customer.md) (with accounts) | Customers are created and listed |
| 16 | **Bank System operator** | [API] Make [transfers](../../../api-methods/payment/README.md#create-payment) and other checks to fulfil the integration test scenario | All checks passed |

{% hint style="info" %}
`asset_code = 'FEUR'` (or your agreed token symbol) is used throughout the Stage environment. The `asset_issuer` is the Arbitrum contract address of your token — provided by BlockControl after step 10.
{% endhint %}

---

## Environment URLs

| Environment | Frontend | API |
|---|---|---|
| **Stage** | `https://backoffice-{alias}-stage.blockcontrol.io` | `https://api-{alias}-stage.blockcontrol.io` |
| **Production** | `https://backoffice-{alias}.blockcontrol.io` | `https://api-{alias}.blockcontrol.io` |

Replace `{alias}` with your institution's alias, assigned during onboarding.
