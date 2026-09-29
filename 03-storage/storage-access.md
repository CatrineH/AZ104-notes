# Storage Account Access

[← Back to README](../README.md)

## Storage Account Keys
- Every storage account has **two keys**: key1 and key2
- Each key gives **full access** to the whole account
- Two keys allow rotation without downtime
- Store keys in **Azure Key Vault**, never in code

```powershell
Get-AzStorageAccountKey -ResourceGroupName rg1 -Name mystorage
```

## Shared Access Signatures (SAS)
A SAS is a token added to a URL that gives **limited, delegated access**: which permissions, for how long, and from which IPs.

| Type | Signed with | Notes |
|---|---|---|
| User delegation SAS | Entra ID credentials | Blob only, most secure |
| Service SAS | Account key | One service |
| Account SAS | Account key | One or more services |

### Full access vs delegated access
| | Account key | SAS |
|---|---|---|
| Access level | Everything | Only what the token allows |
| Time limit | None | Has an expiry time |
| Revoke | Regenerate the key | Expiry, stored access policy, or regenerate key |

## Azure Storage Explorer
Desktop app for working with storage. I used it to:
- Connect to storage accounts
- Browse containers
- List blobs
- Download files

## PowerShell

```powershell
# Connect with account key (full access)
New-AzStorageContext -StorageAccountName mystorage -StorageAccountKey "<key>"

# Connect with SAS token (delegated access)
New-AzStorageContext -StorageAccountName mystorage -SasToken "<sas>"

# Browse containers
Get-AzStorageContainer -Context $ctx

# List blobs
Get-AzStorageBlob -Container "data" -Context $ctx

# Download a file
Get-AzStorageBlobContent -Container "data" -Blob "file.txt" -Destination "C:\temp" -Context $ctx
```

## Related
- [Managed Identities](../01-identity/managed-identities.md)
