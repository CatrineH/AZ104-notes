# Custom Roles

[← Back to README](../README.md) · Lab 08

## Why?
**Least privilege.** Built-in roles often give too much. For example, *Virtual Machine Contributor* can also change the VM's configuration – but an operator may only need to **restart** VMs.

## The JSON definition

```json
{
  "Name": "VM Power Operator",
  "Description": "Can start, restart, and stop VMs",
  "Actions": [
    "Microsoft.Compute/virtualMachines/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/deallocate/action"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/<subscription-id>/resourceGroups/rg-dev"
  ]
}
```

| Property | Meaning |
|---|---|
| **Actions** | Allowed **control plane** operations |
| **NotActions** | Operations **removed** from Actions (for example `*` minus delete) |
| **DataActions** | Allowed **data plane** operations (for example reading blobs) |
| **NotDataActions** | Data operations removed from DataActions |
| **AssignableScopes** | **Where** the role can be assigned |

**NotActions is not a deny.** If another role assignment gives the same permission, the user still has it.

## Minimum permissions for a VM restart
- `Microsoft.Compute/virtualMachines/read`
- `Microsoft.Compute/virtualMachines/restart/action`

## Create the role

```powershell
New-AzRoleDefinition -InputFile ".\vm-power-operator.json"
```
```bash
az role definition create --role-definition vm-power-operator.json
```

## Exam tips
- Creating custom roles requires `Microsoft.Authorization/roleDefinitions/write` → you must be **Owner** or **User Access Administrator**
- Know the difference between **Actions** (control plane) and **DataActions** (data plane)
- A good starting point: copy a built-in role (**Clone a role** in the portal) and remove what isn't needed

## Related
- [Azure RBAC](../04-governance/azure-rbac.md)
