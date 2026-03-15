# 👉 Basic use case

End-to-end process to make your first payment

---

Please follow these steps to make your first payment in the Pre-prod (Stage) environment.

1. Complete all integration steps for the [Stage environment](integration-plan/integration-plan-technical-team.md).

2. Request a BlockControl integration manager to create test token assets for the emission and processing accounts.

3. [Create a customer](../api-methods/customer/create-customer.md): store the returned `guid` as `customer_guid`.

4. Get [the list of accounts](../api-methods/account.md#get-accounts-list) and store the processing account's `guid` as `processing_guid`.

5. [Create a payment](../api-methods/payment/README.md#create-payment) request with:

   ```
   sender_id = processing_guid
   receiver_id = customer_guid
   amount = {your_value}
   asset_code = FEUR
   ```

   Store the returned payment `guid` as `payment_guid`.

6. [Confirm](../api-methods/payment/README.md#confirm-payment) the payment using `payment_guid`.

7. Check the [payment status](../api-methods/payment/README.md#get-payment-information) — wait for `status: CONFIRMED`.

8. Verify the transaction on Arbiscan using the `transfer_hash` returned in the payment response:
   ```
   https://sepolia.arbiscan.io/tx/{transfer_hash}
   ```

9. Check [payment history](../api-methods/payment/README.md#get-payment-list) to confirm the transfer appears correctly.

---

### 🎉 Congrats! Your first payment is done!

{% hint style="success" %}
If you reached this point successfully, your integration with BlockControl is working end-to-end. You are ready to proceed to the production environment setup.
{% endhint %}
