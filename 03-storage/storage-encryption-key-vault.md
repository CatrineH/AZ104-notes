# Storage Encryption and Azure Key Vault

[← Back to README](../README.md)

## Encryption at rest
All data in Azure Storage is **always encrypted at rest** with 256-bit AES. I can't turn it off. The question is only **who manages the key**.

| Option | Key managed by | Notes |
|---|---|---|
| **Microsoft-managed keys** | Microsoft | **The default.** Nothing to configure |
| **Customer-managed keys (CMK)** | **I**, in **Azure Key Vault** | I control rotation, and can revoke access to the data |

Why choose customer-managed keys?
- **Compliance** rules that require the company to own and control the keys
- **Control over rotation** – when and how often
- **Revoke access:** disable the key, and the storage data becomes **inaccessible**

---

## Azure Key Vault
A secure service for storing three kinds of objects:

| Object | Example |
|---|---|
| **Keys** | Encryption keys, for example for storage CMK |
| **Secrets** | Passwords, connection strings, API keys |
| **Certificates** | TLS/SSL certificates |

### Two permission models

| Model | How access is given |
|---|---|
| **Azure RBAC** (recommended) | Role assignments, just like other Azure resources |
| **Vault access policy** (older) | Permissions set inside the vault |

### Control plane vs data plane

| | Control plane | Data plane |
|---|---|---|
| Is about | **The vault itself** – create, delete, settings, network | **What's inside** – keys, secrets, certificates |
| Example roles | Owner, Contributor, Key Vault Contributor | Key Vault Crypto Officer, Key Vault Secrets User |

A **Contributor can create the vault**, but **can't create keys** in it. A **data plane role** is needed for that.

### Key Vault roles

| Role | Can |
|---|---|
| **Key Vault Administrator** | Everything on the data plane (keys, secrets, certificates) |
| **Key Vault Crypto Officer** | Create and manage **keys** |
| **Key Vault Crypto Service Encryption User** | **Use** a key to encrypt and decrypt – for a **service's managed identity** |
| **Key Vault Secrets Officer** | Create and manage **secrets** |
| **Key Vault Secrets User** | **Read** secrets |

### Network
- Key Vault has its own **firewall**, just like storage: all networks, selected networks, or private endpoint only
- If I restrict the network, turn on **"Allow trusted Microsoft services to bypass this firewall"** so Azure Storage can still reach the key

---

## How customer-managed keys work with storage

```
Storage account
  │ system-assigned managed identity
  │ role: Key Vault Crypto Service Encryption User
  ▼
Key Vault ── key: cmk-storage
```

1. The storage account gets a **managed identity** – in the portal, the **system-assigned identity is enabled automatically** when I choose CMK
2. The identity gets the role **Key Vault Crypto Service Encryption User** on the vault (or the key)
3. The storage account uses the key to **protect its own encryption key** (key wrapping). My data isn't re-encrypted

### Requirements
- The Key Vault must have **soft delete** and **purge protection** enabled
- Key type: **RSA** or **RSA-HSM** (2048, 3072, or 4096 bits)

## Key rotation
- **Rotating a key creates a new version** – the key name stays the same, the old versions remain
- With **automatic key version updates** (no version chosen), the storage account **starts using the newest version automatically**, normally within 24 hours
- If I chose a **specific version**, I must update the storage account manually after rotating
- Rotation doesn't re-encrypt the data, so it's quick
- A **rotation policy** in Key Vault can rotate keys automatically, for example every 90 days

## Revoking access
Disable or delete the key, or remove the role assignment → the storage account can't use the key → **data can't be read** until access is restored.

## Exam tips
- Encryption at rest is **always on** – CMK only changes who manages the key
- CMK needs **soft delete + purge protection** on the Key Vault
- The storage account's **managed identity** needs **Key Vault Crypto Service Encryption User**
- Contributor = control plane only. Creating keys needs **Key Vault Crypto Officer** (or Administrator)
- Rotation = **new version** of the same key

## Related
- [Lab: Storage Encryption with Customer-Managed Keys](../labs/storage-cmk-lab.md)
- [Managed Identities](../02-identity/managed-identities.md)
- [Azure RBAC](../02-identity/azure-rbac.md)
- [Storage Network Security](storage-network-security.md)
