# Entra ID Licenses

[← Back to README](../README.md) · Lab 03

Many exam questions are really about **which license a feature needs**.

| License | Includes, among other things |
|---|---|
| **Free** | Basic user and group management, bulk operations, B2B guests |
| **P1** | **Conditional Access**, **dynamic groups**, **group-based licensing**, **SSPR with password writeback** to on-premises AD |
| **P2** | Everything in P1 + **PIM**, **Access Reviews**, **Identity Protection** (risk-based policies) |

## How to remember
- **P1 = automate and control access** (dynamic groups, Conditional Access)
- **P2 = govern and protect privileged access** (PIM, reviews, risk detection)

## Assigning licenses
- To a **user**, or to a **group** (group-based licensing – needs P1)
- The user's **usage location** must be set first

## Exam tips
- "The company wants to use dynamic groups / Conditional Access" → **P1**
- "The company wants just-in-time admin access / access reviews / risky sign-in policies" → **P2**

## Related
- [Groups](groups.md)
- [SSPR](sspr.md)
- [PIM](pim.md)
- [Conditional Access](conditional-access.md)
