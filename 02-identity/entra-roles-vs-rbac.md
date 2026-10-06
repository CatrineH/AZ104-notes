# Entra Roles vs Azure RBAC Roles

[← Back to README](../README.md) · Lab 09

**Arguably the most important concept for the identity part of the exam.** The two role systems are **completely separate**.

| | Entra roles | Azure RBAC roles |
|---|---|---|
| Manage | The **directory**: users, groups, passwords, apps, licenses | **Azure resources**: VMs, storage, networks |
| Examples | Global Administrator, User Administrator, Helpdesk Administrator | Owner, Contributor, Reader |
| Scope | Tenant (or administrative unit) | Management group, subscription, resource group, resource |
| Where in the portal | Microsoft Entra ID → Roles and administrators | Resource → Access control (IAM) |

## Lab example

| Person | Role | Can | Can't |
|---|---|---|---|
| **Sarah** | User Administrator (Entra) | Create users, reset passwords | Touch VMs |
| **Adam** | Contributor (RBAC) | Create and manage resources | Create users |

## Global Administrator and Azure
- A **Global Administrator is not automatically an Owner** of Azure subscriptions
- But a Global Administrator can turn on **Elevate access** (Entra ID → Properties → *Access management for Azure resources*)
- This gives them **User Access Administrator at the root scope (`/`)** – all subscriptions and management groups
- From there, they can give themselves any Azure role
- Turn it off again when finished

## Exam tips
- "Needs to create users" → **Entra role** (User Administrator)
- "Needs to manage VMs" → **RBAC role** (Contributor / Virtual Machine Contributor)
- "Global admin can't see the subscription" → **Elevate access**

## Related
- [Azure RBAC](azure-rbac.md)
- [PIM](pim.md) – handles both role types in separate sections
