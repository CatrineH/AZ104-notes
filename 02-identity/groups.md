# Groups and Membership

[← Back to README](../README.md) · Lab 02

## Group types

| Type | Used for |
|---|---|
| **Security group** | Giving access to resources (RBAC roles, apps, licenses) |
| **Microsoft 365 group** | Collaboration – shared mailbox, calendar, Teams, SharePoint |

## Membership types

| Type | How members are added | License |
|---|---|---|
| **Assigned** | Manually, by an admin or group owner | Free |
| **Dynamic user** | Automatically, by a rule on user properties | **Entra ID P1** |
| **Dynamic device** | Automatically, by a rule on device properties | **Entra ID P1** |

### Dynamic group rule example
```
user.department -eq "Development"
```
Everyone with *Development* as department becomes a member automatically – and is removed if their department changes.

- You **can't add or remove members manually** in a dynamic group
- One group is either user-based or device-based, not both

## Group owners
- A **group owner** can manage the group's members **without being an administrator**
- Useful for letting team leads manage their own team's access

## Why use groups for access?
Assign RBAC roles and licenses to **groups**, not individual users. When someone joins or leaves the group, their access follows automatically. This also makes [access reviews](access-reviews.md) work well.

## Exam tips
- **Dynamic groups require Entra ID P1**
- Deleted **Microsoft 365 groups** can be restored for **30 days**. Deleted security groups can't
- Group owners manage members, not the group's role assignments

## Related
- [Entra Users](entra-users.md)
- [Entra Licenses](entra-licenses.md)
- [Azure RBAC](azure-rbac.md)
