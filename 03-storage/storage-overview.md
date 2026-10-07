# Azure Storage – Overview

[← Back to README](../README.md)

Storage belongs to the exam domain **"Implement and manage storage"** (about **15–20%** of AZ-104).

## The storage account
A **storage account** is the container for all Azure Storage services. Everything starts here.

- The name must be **globally unique**, **3–24 characters**, **lowercase letters and numbers only**
- The name becomes part of every endpoint, for example `https://az104catrinesta.blob.core.windows.net`
- Redundancy, network rules, and access keys are set **on the account** and apply to all services in it

## Services in a storage account

| Service | Stores | Endpoint | Notes |
|---|---|---|---|
| **Blob** | Objects: files, images, videos, backups | `<account>.blob.core.windows.net` | Containers with blobs |
| **Files** | File shares (SMB/NFS) | `<account>.file.core.windows.net` | [azure-files.md](azure-files.md) |
| **Queue** | Messages between app components | `<account>.queue.core.windows.net` | |
| **Table** | NoSQL key-value data | `<account>.table.core.windows.net` | |

> **Managed disks** for VMs are also Azure Storage, but they're separate resources – not part of a storage account.

## Account types

| Type | Supports | Use for |
|---|---|---|
| **Standard general-purpose v2** | Blob, Files, Queue, Table | **The default** for almost everything |
| **Premium block blobs** | Blob only (SSD) | Low latency, many small transactions |
| **Premium file shares** (FileStorage) | Files only (SSD) | High-performance file shares, NFS |
| **Premium page blobs** | Page blobs only (SSD) | Special cases, like unmanaged VM disks |

## My storage notes

### Topics

| Topic | Notes | Covers |
|---|---|---|
| Redundancy | [storage-redundancy.md](storage-redundancy.md) | LRS, ZRS, GRS, RA-GRS, GZRS, RA-GZRS |
| Access and authorization | [storage-access.md](storage-access.md) | Account keys, SAS, Storage Explorer, PowerShell |
| Network security | [storage-network-security.md](storage-network-security.md) | Public network access, service endpoints, private endpoints, private DNS zones |
| Azure Files | [azure-files.md](azure-files.md) | SMB/NFS, tiers, authentication, snapshots |
| Azure File Sync | [azure-file-sync.md](azure-file-sync.md) | Storage Sync Service, sync groups, cloud tiering |

### Labs

| Lab | Covers |
|---|---|
| [Storage Account with Private Access](../labs/storage-private-access-lab.md) | Jump server, Azure CLI and PowerShell on VMs, service endpoint, private endpoint, DNS |
| [Azure File Share with CLI](../labs/azure-file-share-lab.md) | Create a share, upload files, mount on Linux and Windows |

<!-- Add new storage topics and labs here -->

## Three themes that come back again and again

| Theme | Remember |
|---|---|
| **Who can access the data?** | Account keys = full access, SAS = limited and time-bound, Entra ID + data roles = preferred |
| **From which network?** | Storage firewall, service endpoints, private endpoints – on top of authorization |
| **How safe is the data?** | Redundancy protects against hardware, zone, or region failure. Soft delete and snapshots protect against mistakes |

## Related
- [Managed Identities](../02-identity/managed-identities.md) – passwordless access to storage
- [Azure RBAC](../04-governance/azure-rbac.md) – control plane vs data plane
- [DNS](../01-networking/dns.md)
