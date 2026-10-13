# Resource Locks

[← Back to README](../README.md) · Governance

## What is it?
A **resource lock** protects resources from being **deleted or changed by accident** – what must **not** be deleted or changed?

- Applies to **everyone**, including **Owners**
- Can be set on a **subscription**, **resource group**, or **resource**
- **Inherited downwards** – a lock on a resource group protects every resource in it

## Two lock types

| Lock | Read | Modify | Delete |
|---|---|---|---|
| **CanNotDelete** (Delete in the portal) | YES | YES | NO |
| **ReadOnly** (Read-only in the portal) | YES | NO | NO |

- When several locks apply, the **most restrictive lock wins**
- To delete or change a locked resource, **remove the lock first**

## Who can create and remove locks?
You need `Microsoft.Authorization/locks/*`:
- YES **Owner**
- YES **User Access Administrator**
- NO **Contributor** – can't create or remove locks

---

## ReadOnly has side effects
Some operations **look like reads** but are technically **POST requests** – and ReadOnly **blocks them**.

| Resource | What ReadOnly breaks |
|---|---|
| **Storage accounts** | **Listing account keys** is blocked – so Storage Explorer, the portal's data view with keys, and scripts using keys stop working |
| **VMs** | **Start, stop, and restart** are blocked |
| **App Service** | Scaling and some management tools stop working, for example browsing files from Visual Studio |
| **Resource groups** | ARM can't clean up old deployment history |

> **Use CanNotDelete for production. Use ReadOnly only when you truly want it frozen – and test first.**

## Locks protect the control plane, not the data
- A lock on a storage account stops the **account** from being deleted
- It does **not** stop someone with a data role from **deleting blobs or files** inside it
- Protect data with **soft delete**, **versioning**, or **immutable storage**

---

## Commands

**Azure CLI**
```bash
# Lock a resource group
az lock create --name lock-no-delete --lock-type CanNotDelete \
  --resource-group AZ104-catrine-rg

# Lock one resource
az lock create --name lock-readonly --lock-type ReadOnly \
  --resource-group AZ104-catrine-rg \
  --resource-name az104catrinesta \
  --resource-type Microsoft.Storage/storageAccounts

# List and delete
az lock list --resource-group AZ104-catrine-rg -o table
az lock delete --name lock-no-delete --resource-group AZ104-catrine-rg
```

**PowerShell**
```powershell
New-AzResourceLock -LockName "lock-no-delete" -LockLevel CanNotDelete `
  -ResourceGroupName AZ104-catrine-rg

Get-AzResourceLock -ResourceGroupName AZ104-catrine-rg
Remove-AzResourceLock -LockName "lock-no-delete" -ResourceGroupName AZ104-catrine-rg
```

Portal: **Resource → Settings → Locks → + Add**

---

## Locks vs RBAC vs Policy

| Tool | Protects against |
|---|---|
| **RBAC** | The **wrong people** making changes |
| **Locks** | **Accidental** delete or change – even by the right people |
| **Policy (DenyAction)** | Delete actions across **many resources**, set centrally |

## Exam tips
- Locks apply **even to Owners**
- **Most restrictive** lock wins
- Contributor **can't** remove a lock
- ReadOnly on a storage account **blocks listing keys**
- ReadOnly on a VM **blocks start/restart**

## Related
- [Inheritance](inheritance.md)
- [Azure Policy](azure-policy.md)
- [Azure RBAC](../02-identity/azure-rbac.md)

