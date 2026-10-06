# Access Reviews

[← Back to README](../README.md) · Lab 13

## What is it?
**Access reviews** check **regularly** whether people should **keep** their access – for example group memberships, app access, or roles.

**Requires Entra ID P2** (or Microsoft Entra ID Governance).

## Who can review?
- **Group owners**
- **Selected users**
- **Managers** of the users
- **The users themselves** (self-review)

## Important settings

| Setting | What it does |
|---|---|
| **Recurrence** | One-time, weekly, monthly, quarterly, yearly |
| **Duration** | How many days reviewers have to respond |
| **Auto apply results** | Yes, If on, a **denial actually removes** the access. If off, someone must apply the results manually |
| **If reviewers don't respond** | No change, remove access, approve access, or take recommendations |

## Why group-based access matters
If Azure RBAC roles are assigned to a **group**, removing a user from the group in an access review **also removes their Azure access** – automatically. This is a big advantage of giving access through groups instead of to individual users.

## Typical use cases
- Review **guest users** every quarter
- Review members of **admin groups**
- Review who has access to sensitive apps

## Exam tips
- Access reviews = **P2**
- **Auto apply results** decides whether denials are actually applied

## Related
- [Groups](groups.md)
- [Guest Users](guest-users.md)
- [PIM](pim.md)
