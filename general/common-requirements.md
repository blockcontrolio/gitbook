# 🌐 Common requirements

For API users the following requirements and recommendations are applicable:

1. Each API request contains a `counterparty_guid` in the URL, which you received during the [onboarding process](integration-plan/README.md).

2. A `session token` must be sent for all API requests in the HTTP `Authorization` header (see [Authentication](../api-methods/authentication/README.md) for details):
   ```
   Authorization: Bearer ${session-token}
   ```

3. Time is represented in **ISO 8601 format** with timezone, for example:
   ```
   2025-03-14T10:30:00+00:00
   ```

4. All amounts are represented as **strings** to preserve decimal precision, for example:
   ```
   "amount": "1500.00"
   ```

5. All blockchain operations are **asynchronous**. A `202 Accepted` response means the operation has been queued — not that it has been confirmed on-chain. Use the `transferId` from the response to poll status or configure a [webhook](../api-methods/payment/callbacks.md) for push notification.

6. Identifiers used for testing in the Stage (Pre-prod) environment:

   ```
   asset_code: FEUR
   asset_issuer: 0xYourTokenContractAddress
   network: Arbitrum Sepolia (testnet)
   explorer: https://sepolia.arbiscan.io
   ```

{% hint style="info" %}
Your `counterparty_guid`, Stage environment URL, and test asset details are provided by the BlockControl team during the onboarding kickoff. Contact your integration manager if you have not received these.
{% endhint %}
