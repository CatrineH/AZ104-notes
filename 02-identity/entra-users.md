# Entra ID and Users

[← Back to README](../README.md) · Lab 01

## User lifecycle

| Action | What happens |
|---|---|
| **Create** | One user at a time, or **bulk create** with a CSV file |
| **Block sign-in** | The user still exists but can't sign in. Good when someone leaves or is on leave |
| **Delete** | The user moves to **Deleted users** |
| **Restore** | Possible for **30 days** after deletion |
| **Permanently delete** | Happens automatically after 30 days, or manually from Deleted users |

## Bulk operations
- **Bulk create**, **bulk invite** (guests), and **bulk delete** use a **CSV template downloaded from the portal**
- Portal: **Users → Bulk operations → Download** the template, fill it in, then upload it

## User types

| Type | Who |
|---|---|
| **Member** | Employees in your own tenant |
| **Guest** | External users invited through B2B (see [guest-users.md](guest-users.md)) |

## Exam tips
- **Usage location must be set before a license can be assigned.** Licensing rules differ between countries, so Azure needs to know where the user is
- Deleted users can be restored for **30 days** – after that, they're gone
- Blocking sign-in is reversible and keeps the user's group memberships and roles

## Related
- [Groups](groups.md)
- [Entra Licenses](entra-licenses.md)
