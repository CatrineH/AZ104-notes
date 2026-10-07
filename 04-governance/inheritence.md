# Inheritance

[← Back to README](../README.md) · Governance 02

## Control flows downward
Settings assigned at a scope apply to **everything below it**:

```
Management group  ──►  Subscription  ──►  Resource group  ──►  Resource
```

But each governance tool behaves a little differently.

## How each tool inherits

| Tool | Inherited downwards? | Can a lower scope weaken it? |
|---|---|---|
| **RBAC** | YES | NO. Lower assignments can only **add** permissions |
| **Azure Policy** | YES | NO. Only **exclusions** or **exemptions**, set at the assignment scope |
| **Resource locks** | YES | NO. The **most restrictive** lock in the chain wins |
| **Tags** | **NO** | – Use a **Modify policy** to inherit or copy tags from the resource group or subscription |
| **Budgets** | NO | – They **roll up**: a subscription budget measures **everything below it** |

### Examples
- **RBAC:** Reader on the subscription + Contributor on `rg-dev` → Contributor in `rg-dev`, Reader everywhere else
- **Policy:** "Allowed locations: Norway East" on a management group → no subscription below it can create resources in West Europe
- **Locks:** CanNotDelete on a resource group + ReadOnly on one VM inside it → the VM is **ReadOnly**
- **Tags:** `environment=prod` on a resource group → resources inside it **don't** get the tag automatically
- **Budgets:** a 10 000 kr budget on `sub-dev` counts the costs of **all** resource groups in it

> **Exam tip:** tags are the classic trap – they are **not inherited**. The answer is usually the built-in policy **"Inherit a tag from the resource group"** (Modify effect).

---

## Control plane vs data plane

| | Control plane | Data plane |
|---|---|---|
| Manages | The **resource** – e.g. the storage account | The **data** – e.g. files and blobs in a container |
| Roles | Owner, Contributor, Reader | Storage Blob Data Owner/Contributor/Reader |
| Deleting a single file | NO Needs a data role | YES |

A control plane **Owner or Contributor can delete the whole storage account** – and all the data with it. That's why production storage often gets a **CanNotDelete lock**.

---

## Govern from above

| Assign at | Good for |
|---|---|
| **Root / management groups** | Rules for the whole company: allowed regions, security baseline, required tags |
| **Subscriptions and resource groups** | Rules for one environment or workload |

### Assign once, high up
- **Consistent** – every subscription below follows the same rules
- **Low effort** – one assignment instead of one per subscription
- **Out of the owner's reach** – a **subscription Owner can't remove** a policy or role assigned at a management group, because they have no rights at that scope

### But not too high
- **Big blast radius** – a bad **Deny policy at the root breaks every subscription**
- Test new policies with the **Audit** effect, or on a **Sandbox** management group, before assigning them high up

## Related
- [Why Governance](why-governance.md)
- [Azure RBAC](../02-identity/azure-rbac.md)
- [Azure Policy](azure-policy.md)
- [Resource Locks](resource-locks.md)
- [Tags](tags.md)

