# Privileged Identity Management (PIM)

[← Back to README](../README.md) · Lab 11

## What is it?
**PIM** gives **just-in-time** admin access. Instead of being an admin all the time, users **activate** the role only when they need it, for a limited time.

**Requires Entra ID P2** (or Microsoft Entra ID Governance).

## Assignment types

| Type | Meaning |
|---|---|
| **Eligible** | *Can* activate the role when needed – no access until then |
| **Active** | *Has* the access right now |

## Activation requirements (can be combined)
- **MFA**
- **Approval** from a chosen approver
- **Justification** (a reason)
- **Ticket number**
- **Maximum duration**, for example 1–8 hours

## Two sections in PIM

| Section | Manages | Assigned at |
|---|---|---|
| **Microsoft Entra roles** | Directory roles, such as **Application Administrator** or Global Administrator | Directory (or administrative unit) |
| **Azure resources** | RBAC roles, such as Owner or Contributor | Management group, subscription, resource group, resource |

**Application Administrator is an Entra role, not an RBAC role.** It can't be assigned on a resource group – it's assigned in the **Microsoft Entra roles** section at directory level.

## Exam tips
- PIM = **P2**
- **Eligible** = can activate, **Active** = has access now
- Connects to [Entra Roles vs Azure RBAC](entra-roles-vs-rbac.md): PIM handles both, but separately

## Related
- [Entra Licenses](entra-licenses.md)
- [Access Reviews](access-reviews.md)
