# [optional] Admin Panel

The BlockControl Admin Panel is a separate web interface for platform-level administration. It is available to institutions who require self-service management of counterparties, networks, and users — typically larger EMIs managing multiple sub-counterparties or distribution channels.

---

## Access

The Admin Panel is accessible at a dedicated URL provisioned during onboarding:

```
https://admin.{alias}.blockcontrol.io
```

Access is restricted to users with the **Platform Admin** role, provisioned by the BlockControl team.

---

## Features available in the Admin Panel

### Counterparty management
- Register new counterparties (sub-institutions, distribution partners)
- Update counterparty type (EMI / LSP)
- Enable or disable counterparties
- View all accounts, users, and transaction volume per counterparty

### Network management
- View configured blockchain networks
- Update RPC URLs if required by your infrastructure
- Toggle network availability for specific counterparties

### User management
- View all employees across all counterparties
- Update employee roles
- Disable employee accounts globally

### MPC provider configuration
- Configure Dfns or Fireblocks credentials
- Set up approval policies for multisig payments
- Validate MPC connectivity

### Webhook management
- Register, view, and delete webhook endpoints
- View delivery history and retry status

---

{% hint style="info" %}
The Admin Panel is provided as an optional capability for institutions managing complex multi-counterparty structures. For standard single-counterparty integrations, all operations can be performed via the main platform UI and API without requiring Admin Panel access.
{% endhint %}

---

## Admin Panel API

All Admin Panel operations are also available via a dedicated Admin API:

```
https://api.{alias}.blockcontrol.io/api/v1/admin/
```

Contact your BlockControl integration manager for the Admin API specification if required.
