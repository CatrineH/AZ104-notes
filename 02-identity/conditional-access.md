# Conditional Access

[← Back to README](../README.md) · Lab 14

## What is it?
**Conditional Access** is Entra ID's **"if–then" policy engine**:

**IF** a user, app, and condition match → **THEN** block, or allow with requirements.

**Requires Entra ID P1.**

## Policy structure

| Part | Contains | Examples |
|---|---|---|
| **Assignments – users** | Include/exclude users, groups, roles | All users, except break-glass |
| **Target resources** | Cloud apps | Microsoft Azure Management, Office 365 |
| **Conditions** | When the policy applies | Location, device platform, sign-in risk, client app |
| **Access controls – Grant** | Block, or grant with requirements | Require MFA, compliant device |
| **Access controls – Session** | Limits during the session | Sign-in frequency |

## Rules for how policies work
- **Exclusions win over inclusions**
- If **several policies apply**, **all** of them must be satisfied
- **Block always wins**
- **Report-only** mode logs what *would* happen, without enforcing anything
- The **What If** tool simulates the result for a user without a real sign-in

## Microsoft Azure Management
- Covers the **Azure portal, Azure CLI, Azure PowerShell, and ARM**
- **Be careful when blocking it – you can **lock yourself out**

## Break-glass account
- An **emergency admin account** that is **excluded** from Conditional Access policies
- Used if a policy locks out all other admins
- Long, strong password, stored safely, and its sign-ins are monitored

## Good habits
1. Create the policy in **Report-only** first
2. Test with **What If**
3. Always **exclude a break-glass account**
4. Then turn the policy **On**

## Exam tips
- Conditional Access = **P1**
- Exclude beats include, Block beats everything
- "Test a policy without affecting users" → **Report-only** or **What If**

## Related
- [Entra Licenses](entra-licenses.md)
- [Entra Roles vs Azure RBAC](entra-roles-vs-rbac.md)
