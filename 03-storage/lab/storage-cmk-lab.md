# Lab: Storage Encryption with Customer-Managed Keys

[← Back to README](../README.md)

## Goal
Encrypt a storage account with **your own key** from **Azure Key Vault**, using the storage account's **system-assigned managed identity** – then rotate the key.

## Checklist
- [ ] 1. Create the Key Vault
- [ ] 2. Give yourself a role to create keys
- [ ] 3. Create the key
- [ ] 4. Enable the storage account's managed identity
- [ ] 5. Give the managed identity access to the key
- [ ] 6. Configure customer-managed keys on the storage account
- [ ] 7. Rotate the key
- [ ] 8. Clean up

## Resource names

| Resource | Name |
|---|---|
| Resource group | `AZ104-catrine-rg` |
| Storage account | `az104catrinesta` |
| Key Vault | `kv-az104-catrine` – globally unique, 3–24 characters, letters, numbers, and hyphens |
| Key | `cmk-storage` |

<!-- Adjust the names to what I used in the lab -->

```bash
RG=AZ104-catrine-rg
SA=az104catrinesta
KV=kv-az104-catrine
KEY=cmk-storage
```

---

## Step 1: Create the Key Vault

```bash
az keyvault create \
  --name $KV \
  --resource-group $RG \
  --location norwayeast \
  --enable-rbac-authorization true \
  --enable-purge-protection true

KV_ID=$(az keyvault show -n $KV --query id -o tsv)
```

- **Soft delete** is on by default
- **Purge protection** must be turned on for CMK – and it **can't be turned off** again

## Step 2: Give yourself a role to create keys

Even as Owner or Contributor, you **can't create keys** without a data plane role.

```bash
ME=$(az ad signed-in-user show --query id -o tsv)

az role assignment create \
  --assignee $ME \
  --role "Key Vault Crypto Officer" \
  --scope $KV_ID
```

Portal: **Key Vault → Access control (IAM) → Add role assignment**.

> Wait a minute or two – role assignments take time to apply.

## Step 3: Create the key

```bash
az keyvault key create --vault-name $KV --name $KEY --kty RSA --size 3072
```

Portal: **Key Vault → Objects → Keys → + Generate/Import**.

## Step 4: Enable the storage account's managed identity

```bash
az storage account update -n $SA -g $RG --assign-identity

SA_PRINCIPAL=$(az storage account show -n $SA -g $RG --query identity.principalId -o tsv)
```

In the portal, this happens **automatically** when you choose customer-managed keys with the system-assigned identity.

## Step 5: Give the managed identity access to the key

```bash
az role assignment create \
  --assignee-object-id $SA_PRINCIPAL \
  --assignee-principal-type ServicePrincipal \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $KV_ID
```

## Step 6: Configure customer-managed keys on the storage account

```bash
az storage account update -n $SA -g $RG \
  --encryption-key-source Microsoft.Keyvault \
  --encryption-key-vault https://$KV.vault.azure.net \
  --encryption-key-name $KEY
```

No `--encryption-key-version` = **automatic key version updates**.

Portal: **Storage account → Security + networking → Encryption → Customer-managed keys → Select key vault and key**.

**Check:**
```bash
az storage account show -n $SA -g $RG --query encryption.keyVaultProperties
```

## Step 7: Rotate the key

```bash
az keyvault key rotate --vault-name $KV --name $KEY

az keyvault key list-versions --vault-name $KV --name $KEY -o table
```

- Rotating creates a **new version** – the key name stays the same and the old version is kept
- The storage account picks up the new version automatically (normally within 24 hours)
- Check `currentVersionedKeyIdentifier` with the command from step 6 to see which version is in use

## Step 8: Clean up

Switch the storage account back to Microsoft-managed keys before deleting the vault:

```bash
az storage account update -n $SA -g $RG --encryption-key-source Microsoft.Storage
```

> Because of **purge protection**, a deleted Key Vault stays in the soft-deleted state for the whole retention period (7–90 days). The name can't be reused until then.

---

## Troubleshooting
- **"Forbidden" when creating a key** → you're missing a data plane role such as Key Vault Crypto Officer
- **Storage can't access the key** → check the managed identity's role, and the Key Vault firewall (allow trusted Microsoft services)
- **"Purge protection must be enabled"** → turn it on in Key Vault → Properties

## Related
- [Storage Encryption and Azure Key Vault](../03-storage/storage-encryption-key-vault.md)
- [Managed Identities](../02-identity/managed-identities.md)
