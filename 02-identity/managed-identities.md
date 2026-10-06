# Managed Identities

[← Back to README](../README.md) · Lab 10

## What is it?
A **Managed Identity** lets an Azure resource authenticate to other Azure services **without storing credentials in code** – passwordless authentication. Azure handles the credentials and tokens automatically.

It is attached to a resource, such as:
- A Virtual Machine (VM)
- An Azure App Service
- Other supported Azure resources

## Types

| | System-assigned | User-assigned |
|---|---|---|
| Created by | Azure, when enabled on a resource | You, as a separate resource |
| Tied to | One resource – follows its lifecycle | Independent |
| Shared between resources | NO | YES |
| Deleted when resource is deleted | YES | NO |

## When to use which
- **System-assigned:** one resource needs its own identity
- **User-assigned:** many resources need the same access, or the identity must survive the resource being recreated

## Lab: VM reading from storage with its managed identity
1. Enable a managed identity on the VM
2. Assign a data role on the storage account or container
3. Sign in **from the VM** and work with blobs – no keys or secrets

```powershell
# Sign in as the VM's managed identity
Connect-AzAccount -Identity

# Storage context using Entra ID (no key)
$ctx = New-AzStorageContext -StorageAccountName staz104cat -UseConnectedAccount

# List blobs
Get-AzStorageBlob -Container training -Context $ctx

# Download a blob
Get-AzStorageBlobContent -Container training -Blob training.txt -Context $ctx

# Upload a file
Set-AzStorageBlobContent -Container training -File .\ny.txt -Context $ctx

# Delete a blob
Remove-AzStorageBlob -Container training -Blob ny.txt -Context $ctx
```

| Role on the storage account | List and read | Upload and delete |
|---|---|---|
| **Storage Blob Data Reader** | YES | NO |
| **Storage Blob Data Contributor** | YES | YES |

> RBAC changes can take **a few minutes** to apply. If you get *AuthorizationPermissionMismatch* right after assigning a role, wait and try again.

## Exam tips
- System-assigned is **deleted with the resource**, user-assigned is **not**
- The managed identity still needs an **RBAC role** – having an identity gives no access by itself
- `-UseConnectedAccount` = use the signed-in identity (Entra ID) instead of a key

## Related
- [Service Principals](service-principals.md)
- [Azure RBAC](azure-rbac.md)
- [Storage Access](../03-storage/storage-access.md)
