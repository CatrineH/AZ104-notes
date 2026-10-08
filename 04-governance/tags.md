# Azure Tags

[← Back to README](../README.md)

## What is it?
**Tags Are Lables That Don't Inherit** Name:value pairs on resource, RG and subscriptions. Most - not all - resources types support them.

A **role assignment** has three parts:

| **Not Inherited** | **Limits** | **Plain text** | **Tag Contributor** | **Cost analysis** | **Enforced by Policy** |
|---|---|---|---|---|---|
| A tag on an RG does not appear on the resources inside it | 50 tags per item. Name 512 chars (128) for storage accounts), value 256. | Never put secrets, passwords or personal data tags. | Manage tags without access to the resources themselves. | Group and filter costs by tag - the financial payoff. | Unenforced tags quickly become unreliable. |


## Enforce Tags WIth Policy
Pick the effect by the goal: block, add, copy or fix.

| **Goal** | **Policy** | **Effect** |
|---|---|---|
| Block resources without an owner tag | Require a tag on resources | **Deny** |
| Add environment=dev if missing | Add or replace a tag | **Modify** |
| Copy costCenter from the RG | Inherit a tag from the resource group if missing | **Modify** |
| Fix existing untagged resources | Modify + remediation task | **Modify** |



## Related
- [Custom Roles](../02-identity/custom-roles.md)
- [Entra Roles vs Azure RBAC](../02-identity/entra-roles-vs-rbac.md)
- [Managed Identities](../02-identity/managed-identities.md)
