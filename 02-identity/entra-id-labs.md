# Entra ID Labs – Overview

[← Back to README](../README.md)

All 14 labs belong to the exam domain **"Manage Azure identities and governance"** (about **20–25%** of AZ-104). The labs build on each other.

## Part 1: Identities – *who are the users?*

| Lab | Topic | Notes |
|---|---|---|
| 01 | Entra ID and users | [entra-users.md](entra-users.md) |
| 02 | Groups and membership | [groups.md](groups.md) |
| 03 | Entra licenses | [entra-licenses.md](entra-licenses.md) |
| 04 | Guest users | [guest-users.md](guest-users.md) |
| 05 | Self-service password reset (SSPR) | [sspr.md](sspr.md) |

## Part 2: Access to Azure resources – *what are they allowed to do?*

| Lab | Topic | Notes |
|---|---|---|
| 06 | Azure RBAC | [azure-rbac.md](azure-rbac.md) |
| 07 | RBAC scope and inheritance | [azure-rbac.md](azure-rbac.md#scope-and-inheritance) |
| 08 | Custom roles | [custom-roles.md](custom-roles.md) |
| 09 | Entra roles vs Azure RBAC roles | [entra-roles-vs-rbac.md](entra-roles-vs-rbac.md) |
| 10 | Managed identity | [managed-identities.md](managed-identities.md) |

## Part 3: Access governance and security

| Lab | Topic | Notes |
|---|---|---|
| 11 | Privileged Identity Management (PIM) | [pim.md](pim.md) |
| 12 | App registrations and enterprise applications | [app-registrations.md](app-registrations.md) |
| 13 | Access reviews | [access-reviews.md](access-reviews.md) |
| 14 | Conditional Access | [conditional-access.md](conditional-access.md) |

## Three themes that come back again and again

| Theme | Labs | Remember |
|---|---|---|
| **Entra roles vs Azure RBAC roles** | 9, 11 | Two separate systems: directory vs resources |
| **Control plane vs data plane** | 6, 7, 10 | Managing a resource ≠ accessing its data |
| **Required licenses** | 3, 5, 11, 13, 14 | P1 for Conditional Access and dynamic groups, P2 for PIM, access reviews, and Identity Protection |
