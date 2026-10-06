# Guest Users (B2B Collaboration)

[← Back to README](../README.md) · Lab 04

## What is it?
**B2B collaboration** lets **external users** (partners, consultants) access your resources **without becoming employees** or getting a new account in your tenant.

- The guest signs in with **their own identity**, authenticated in **their home tenant** (or with a Microsoft account, Google, or one-time passcode)
- You control **what** they can access in your tenant

## How a guest looks in your tenant

| Property | Value |
|---|---|
| **User type** | Guest |
| **UPN** | `sarah_abc.com#EXT#@contoso.onmicrosoft.com` |

The UPN is the guest's own email with `@` replaced by `_`, followed by `#EXT#` and your tenant domain.

## Inviting guests
1. Portal: **Users → New user → Invite external user**
2. The guest gets an email and **accepts the invitation** (redemption)
3. Many guests at once: **Bulk invite** with a CSV file

## External collaboration settings
Portal: **External Identities → External collaboration settings**. Controls:
- **Who can invite** guests (only admins, members, or also guests)
- What guests can see in the directory
- **Allowed or blocked domains** for invitations

## Exam tips
- Guests can be **added to groups** and **assigned RBAC roles** just like regular users
- Guests authenticate in their **home tenant** – you don't manage their passwords
- To limit who can invite guests → **External collaboration settings**

## Related
- [Entra Users](entra-users.md)
- [Access Reviews](access-reviews.md) – review guest access regularly
