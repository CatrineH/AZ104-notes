# Managed Identities

[← Back to README](../README.md)

## What is it?
A **Managed Identity** lets an Azure resource authenticate to other Azure services **without storing credentials in code**. Azure handles the credentials and tokens automatically.

It is attached to a resource, such as:
- A Virtual Machine (VM)
- An Azure App Service
- Other supported Azure resources

## Types

| | System-assigned | User-assigned |
|---|---|---|
| Created by | Azure, when enabled on a resource | You, as a separate resource |
| Tied to | One resource | Independent |
| Shared between resources |No |  Yes |
| Deleted when resource is deleted |  Yes | No |

## When to use which
- **System-assigned:** one resource needs its own identity
- **User-assigned:** many resources need the same access, or the identity must survive the resource being recreated

## Example: VM reading from storage
1. Enable a managed identity on the VM
2. Assign the role **Storage Blob Data Reader** on the storage account or container
3. The app on the VM gets a token and reads blobs – no keys or secrets in the code

## Related
- [Service Principals](service-principals.md)
- [Storage Access](../02-storage/storage-access.md)
