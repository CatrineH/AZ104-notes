# Self-Service Password Reset (SSPR)

[← Back to README](../README.md) · Lab 05

## What is it?
**SSPR** lets users **reset their own password** without calling the helpdesk.

## Settings

| Setting | Options |
|---|---|
| **Enabled for** | **None**, **Selected** (a chosen group), or **All** |
| **Number of methods required** | **1 or 2** |
| **Methods** | Authenticator app, SMS, phone call, email, security questions, and more |
| **Registration** | Require users to register methods at next sign-in |

## Administrators have stricter rules
- Admins **always** need **two methods** to reset their password, whatever the setting says
- Admins **can't use security questions**
- This applies even if SSPR is set to *None* for users

## Password writeback
- Sends the new password back to **on-premises Active Directory** (hybrid environments with Entra Connect)
- Requires **Entra ID P1**

## Exam tips
- Enable for a **test group first** with *Selected*, then roll out to *All*
- **Admins: always two methods, no security questions**
- Writeback to on-premises AD → **P1**

## Related
- [Entra Licenses](entra-licenses.md)
- [Entra Users](entra-users.md)
