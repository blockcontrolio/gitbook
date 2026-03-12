# Counterparties

A **counterparty** is the top-level organizational entity in BlockControl. Every account, token, and user belongs to a counterparty. Counterparties represent real-world institutions — typically banks, EMIs, or licensed service providers.

---

## Counterparty Types

BlockControl distinguishes between counterparty types based on their regulatory license and operational role.

### EMI (Electronic Money Institution)
An EMI holds a regulatory license that authorizes it to issue electronic money.

**Capabilities:**
- Can issue (create and deploy) EMT tokens
- Can create accounts with on-chain roles (e.g., Minter, Burner)
- Can participate in inter-counterparty transfers as both sender and recipient
- Can establish partnerships with other counterparties

### LSP (Licensed Service Provider)
An LSP operates within the platform but does not hold an EMI license.

**Capabilities:**
- Can create accounts with **off-chain roles only**
- Cannot deploy or issue tokens
- Can participate in transfers as a distributor or client

{% hint style="warning" %}
Token issuance is only available to counterparties that have been verified as holding an appropriate EMI license. Attempting to deploy a token without the correct counterparty type will return an authorization error.
{% endhint %}

---

## Counterparty Lifecycle

```
1. Created by platform Admin
2. Type assigned (EMI or LSP)
3. License status verified
4. Accounts provisioned
5. Partnership agreements established (for inter-counterparty transfers)
6. Operational
```

---

## Partnerships Between Counterparties

For **inter-counterparty (inter-bank) transfers** to occur, two counterparties must establish a confirmed partnership. Only confirmed partners can:
- Transfer funds to each other's processing accounts
- Import tokens owned by the partner counterparty

### Importing a Partner Token
When a counterparty wants to operate with a token issued by a partner:
1. Navigate to **Partnerships → [Partner Name]**
2. Select **Import Token**
3. Choose the partner (must be a confirmed partner)
4. Choose the token to import

Once imported, the token appears in the counterparty's token list and can be used for transfers within the platform's transfer rules.

---

## Counterparty Management (Admin)

Platform administrators can manage all counterparties from the Admin Panel:
- Create new counterparties
- Assign and update counterparty type
- View associated accounts and users
- Manage network assignments
- Enable or disable a counterparty

{% hint style="info" %}
Counterparty management via the Admin Panel is a Milestone 02 feature. In Milestone 01, counterparties are provisioned directly by the BlockControl team.
{% endhint %}
