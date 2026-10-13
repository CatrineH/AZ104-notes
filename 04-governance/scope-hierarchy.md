# Scope Hierarchy

[← Back to README](../README.md) · Governance

## The four levels of scope

```
Tenant root group
└── Management groups       e.g. Platform, Landing zones, Sandbox
    └── Subscriptions       e.g. sub-prod, sub-dev
        └── Resource groups e.g. rg-webshop-prod
            └── Resources   e.g. VMs, storage accounts, VNets
```

A **scope** is an area where **RBAC, policy, locks, and budgets** are applied – and **inherited downwards** (see [Inheritance](inheritance.md)).

> ***Exam:*** "Do this with the **least administrative effort**" → assign it **once**, at the **highest scope** that covers everything needed.

---

## Management groups
Group **subscriptions** so you can manage access and policy for many at once.

- The **tenant root group** is created automatically. Its ID is the **tenant ID**. It can't be moved or deleted
- Up to **6 levels** below the root (not counting the root or subscriptions)
- Up to **10 000** management groups per tenant
- Each management group and subscription has **exactly one parent**
- New subscriptions are placed under the **root** by default – this can be changed in **hierarchy settings**
- A **Global Administrator** must **elevate access** to manage the root group the first time

### Moving a subscription to another management group
You need:
- **Owner** (or write access) on the **subscription**
- **Management Group Contributor** on **both** the current and the new parent

Policies and roles from the **new** parent apply right away.

---

## Subscriptions
A subscription is **five boundaries**:

| Boundary | What it means |
|---|---|
| **Billing** | Costs are invoiced and reported **per subscription** |
| **Limits and quotas** | For example **vCPU quotas per region** are set per subscription |
| **Trust** | A subscription trusts **exactly one** Entra tenant. A tenant can have **many** subscriptions |
| **Network** | A VNet lives in **one subscription and one region**. Peering **can cross** subscriptions |
| **Access and policy** | A common **scope** for RBAC and policy assignments |

- Linked to a **billing account** – pay-as-you-go (credit card), Enterprise Agreement, or Microsoft Customer Agreement
- *** Moving a subscription to **another tenant** **removes all RBAC assignments** – they must be recreated

---

## Resource groups
- Logical containers for resources that share a **lifecycle**
- A resource belongs to **exactly one** resource group
- Resource groups **can't be nested**
- The resource group's **location** only stores its **metadata** – the resources inside can be in other regions
- **Deleting a resource group deletes everything in it**

---

## Moving resources

| Move | Possible? | Notes |
|---|---|---|
| To another **resource group** | YES, Most resources | Same subscription |
| To another **subscription** | YES, Most resources | Both subscriptions in the **same tenant** |
| To another **region** | *Not with a normal move* | Use **Azure Resource Mover**, or redeploy |

During a move:
- **Both resource groups are locked** for writes and deletes – the resources keep running
- The **resource ID changes** – update scripts and references
- The **location doesn't change**
- Dependent resources must be moved **together**, e.g. a VM with its disks and NIC
- Check first: `az resource invoke-action --action validateMoveResources` or the portal's validation step

```bash
az resource move --destination-group rg-new \
  --ids /subscriptions/<id>/resourceGroups/rg-old/providers/Microsoft.Storage/storageAccounts/az104catrinesta
```

---

## Landing zones
A **landing zone** is a **pre-planned management group hierarchy** with built-in rules, from Microsoft's **Cloud Adoption Framework (CAF)**.

```
Tenant root group
└── Contoso
    ├── Platform
    │   ├── Management     ← logging, monitoring
    │   ├── Connectivity   ← hub network, firewall, DNS
    │   └── Identity
    ├── Landing zones
    │   ├── Corp           ← internal apps
    │   └── Online         ← internet-facing apps
    ├── Sandbox            ← testing, few restrictions
    └── Decommissioned
```

A new subscription placed under *Corp* **automatically gets** all of Corp's policies and roles.

---

## Azure Lighthouse
Manage **other tenants'** resources from **your own** tenant.

- Built for **service providers (MSPs)** – for example a company like Orange Business managing customers' Azure
- The customer **delegates** subscriptions or resource groups to the provider's users or groups, with specific **RBAC roles**
- The provider's staff sign in to **their own tenant** – no guest accounts in each customer tenant
- Customers can see and **remove** the delegation at any time

## Exam tips
- **Least administrative effort** → the **highest scope** that covers what's needed
- Management groups: **6 levels**, **one parent**, root can't be deleted
- A subscription trusts **one tenant**
- Moving resources: the **resource ID changes**, the **location doesn't**
- Manage customers' tenants without guest accounts → **Azure Lighthouse**

## Related
- [Why Governance](why-governance.md)
- [Inheritance](inheritance.md)
- [Azure Policy](azure-policy.md)
- [Azure RBAC](../02-identity/azure-rbac.md)

