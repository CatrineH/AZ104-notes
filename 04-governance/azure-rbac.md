# Azure RBAC

[← Back to README](../README.md) · Labs 06–07

## What is it?
**Azure role-based access control (RBAC)** decides **what someone can do with Azure resources**.

A **role assignment** has three parts:

| Part | Question | Example |
|---|---|---|
| **Security principal** | Who? | User, group, service principal, managed identity |
| **Role definition** | What can they do? | Contributor |
| **Scope** | Where? | Resource group `rg-dev` |

## The basic roles

| Role | Create/modify resources | Assign access to others |
|---|---|---|
| **Reader** | NO | NO |
| **Contributor** | YES | NO |
| **Owner** | YES | YES |
| **User Access Administrator** | NO | YES (only manages access) |
| **Role Based Access Control Administrator** | NO | YES (can be limited with conditions) |

## Control plane vs data plane

| | Control plane (management) | Data plane (the data) |
|---|---|---|
| Example | Create a storage account, change settings | Read or upload blobs |
| Roles | Owner, Contributor, Reader | **Storage Blob Data Owner/Contributor/Reader** |

 **Even an Owner can't necessarily read blobs** with Entra authentication. A **data role** such as *Storage Blob Data Contributor* is needed. (An Owner or Contributor can list the account **keys**, though, and the keys give full access.)

---

## Scope and inheritance

Access is **inherited downwards**:

```
Management group
└── Subscription
    └── Resource group
        └── Resource
```

- A **Contributor on the subscription** can change and delete **everything** in it – including a storage account in the Dev resource group
- Assign roles at the **lowest scope** that does the job (least privilege)

## Resource locks
Locks protect resources **even from Owners**. They're also inherited downwards.

| Lock | Can read | Can modify | Can delete |
|---|---|---|---|
| **CanNotDelete** | YES | YES | NO |
| **ReadOnly** | YES | NO | NO |

- To delete a locked resource, **remove the lock first** (needs Owner or User Access Administrator)
- **ReadOnly** also blocks actions that are technically POST requests, such as **listing storage account keys**

## Key Vault – same idea as storage

When the Key Vault uses the **RBAC permission model**:

| Task | Role needed |
|---|---|
| Create the Key Vault | Contributor |
| Create and manage secrets | **Key Vault Secrets Officer** |
| Only read secrets (minimum privilege) | **Key Vault Secrets User** |

A Contributor can **create** the vault but **can't create secrets** in it – control plane vs data plane again.

## Sign in to a VM with Entra ID

| Role | Gives |
|---|---|
| **Virtual Machine User Login** | Sign in as a regular user |
| **Virtual Machine Administrator Login** | Sign in as administrator |

The role alone is not enough. You also need:
- The **Entra ID login extension** on the VM
- **Network access**: an NSG rule for RDP **3389** (or SSH 22) and a public IP, Bastion, or a jump server

## Exam tips
- **Contributor can't assign roles** – Owner or User Access Administrator can
- Inheritance goes **down**, never up
- Locks beat RBAC – even an Owner can't delete a resource with a CanNotDelete lock
- RBAC changes can take **a few minutes** to apply

## Related
- [Custom Roles](../02-identity/custom-roles.md)
- [Entra Roles vs Azure RBAC](..02-identity/entra-roles-vs-rbac.md)
- [Managed Identities](..02-identity/managed-identities.md)
