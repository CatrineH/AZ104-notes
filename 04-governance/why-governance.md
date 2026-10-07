# Why Governance

[← Back to README](../README.md) · Governance 01

## What is governance?
**Azure governance** is the set of rules, structure, and controls that keep an Azure environment **secure, compliant, and affordable** as it grows – without checking every resource by hand.

The key question:
> **How do you control *this* for every subscription and resource, with the least possible admin effort?**
>
> **Answer: scopes.** Assign rules once, high up in the hierarchy, and let them flow down.

## Failures governance prevents

<!-- Add the ten failures from the course slide here -->

Typical examples:

| Without governance | Governance tool that stops it |
|---|---|
| Resources created in regions the company doesn't allow | **Azure Policy** (allowed locations) |
| Expensive VM sizes created by mistake | **Azure Policy** (allowed SKUs) + **budgets** |
| Nobody knows who owns a resource or which cost center pays | **Tags** + policy that requires tags |
| A production database deleted by accident | **Resource locks** |
| Too many people with Owner rights | **RBAC** + **PIM** |
| Storage accounts open to the internet | **Azure Policy** (deny public access) |
| Surprise bills at the end of the month | **Budgets** and **Cost Management** |
| Security settings different in every subscription | Policy and RBAC assigned at **management group** level |

## Microsoft cloud security benchmark (MCSB)
- A set of **security recommendations** from Microsoft for Azure
- Assigned automatically as a **built-in policy initiative** when you use **Microsoft Defender for Cloud**
- Gives a starting point for how companies structure their security policies

<!-- Follow up: how companies structure policy with MCSB -->

---

## The four levels of scope

```
Tenant root group (management group)
└── Management groups      ← group subscriptions, e.g. Platform, Landing zones, Sandbox
    └── Subscriptions      ← e.g. sub-prod, sub-dev
        └── Resource groups
            └── Resources
```

A **scope** defines an area where RBAC, policy, locks, and budgets are applied – and **inherited downwards**.

### Management groups
- Group **subscriptions** so you can manage access and policy for many at once
- The **tenant root group** is the top. Everything is placed under it
- Up to **6 levels** of management groups below the root
- Each management group or subscription has **exactly one parent**
- Typical groups: **Platform**, **Landing zones**, **Sandbox**

### Subscriptions
- Linked to a **billing account** (for example pay-as-you-go with a credit card, or an Enterprise or Microsoft Customer Agreement)
- Often split by environment or workload, for example `sub-prod`, `sub-dev`

### Resource groups
- Logical containers for resources that share a **lifecycle** – created, managed, and deleted together
- A resource belongs to **exactly one** resource group
- A resource group has a location, but its resources can be in **other regions**

### Resources
- The actual services: VMs, storage accounts, VNets, Key Vaults, and so on

---

## A subscription is five boundaries

| Boundary | What it means |
|---|---|
| **Billing** | Costs are invoiced and reported **per subscription** |
| **Limits and quotas** | For example **vCPU quotas per region** are set per subscription |
| **Trust** | A subscription trusts **exactly one** Entra tenant. A tenant can have **many** subscriptions |
| **Network** | A VNet lives in **one subscription and one region**. Peering **can cross** subscriptions |
| **Access and policy** | A common **scope** for RBAC and policy assignments |

> **Exam tip:** "Separate the bills for two departments" or "a team has run out of vCPU quota" → often solved with **separate subscriptions**.

---

## Landing zones

A **landing zone** is a **pre-planned management group hierarchy** with built-in rules and structure, so new workloads "land" in a place that is already governed. It comes from Microsoft's **Cloud Adoption Framework (CAF)**.

```
Tenant root group
└── Contoso (intermediate root)
    ├── Platform
    │   ├── Management     ← logging, monitoring
    │   ├── Connectivity   ← hub network, firewall, DNS
    │   └── Identity       ← domain controllers
    ├── Landing zones
    │   ├── Corp           ← internal apps, connected to the company network
    │   └── Online         ← internet-facing apps
    ├── Sandbox            ← testing, few restrictions, no connection to prod
    └── Decommissioned     ← subscriptions being shut down
```

- Policies and RBAC are assigned to these **management groups**, not to each subscription
- A new subscription placed under *Corp* **automatically gets** all of Corp's rules

## Related
- [Inheritance](inheritance.md)
- [Azure RBAC](../02-identity/azure-rbac.md)
- [Azure Project Skeleton](../00-guides/azure-project-skeleton.md)
