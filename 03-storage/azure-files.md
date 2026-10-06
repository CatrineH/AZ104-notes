# Azure Files

[← Back to README](../README.md)

## What is it?
**Azure Files** gives you fully managed **file shares in the cloud**. They work like a normal network drive (for example `Z:` in Windows or `/mnt/share` in Linux), but Azure takes care of the servers, disks, and redundancy.

- Lives inside a **storage account**, next to blob containers, queues, and tables
- Can be mounted **at the same time** by many VMs and on-premises computers
- Endpoint: `https://<storageaccount>.file.core.windows.net/<share>`

## Typical use cases
- Replacing or extending **on-premises file servers**
- **Shared folders** for applications running on several VMs
- "Lift and shift" of apps that expect a file share
- Shared config files, logs, and tools for admins

## Protocols

| Protocol | Used by | Notes |
|---|---|---|
| **SMB** (2.1, 3.0, 3.1.1) | Windows, Linux, macOS | Most common. Uses **TCP port 445** |
| **NFS 4.1** | Linux | **Premium only**, and only through a private network (private endpoint or service endpoint) |
| **REST (HTTPS)** | Portal, CLI, PowerShell, Storage Explorer | Used when you upload or list files with commands |

> **!!!** **Port 445** must be open **outbound**. Many home internet providers and company networks block it, so mounting from your own PC can fail even though everything in Azure is correct. From an Azure VM it normally works.

## Blob storage vs Azure Files

| | Blob storage | Azure Files |
|---|---|---|
| Structure | Containers with objects (flat) | Real folders and files |
| Access | HTTP/REST, SDKs | **Mounted as a drive** (SMB/NFS) + REST |
| Best for | Images, videos, backups, data lakes | Shared folders, file server replacement |
| Several VMs at once | Through the app | Mounted on all of them |

## Tiers

| Tier | Storage | Account type | Use for |
|---|---|---|---|
| **Premium** | SSD | **FileStorage** (premium) | Low latency, databases, high performance. Supports NFS |
| **Transaction optimized** | HDD | General-purpose v2 | Many transactions, little storage |
| **Hot** | HDD | General-purpose v2 | General team shares |
| **Cool** | HDD | General-purpose v2 | Archive-like data that is rarely used |

- Premium requires its **own storage account type** (FileStorage). You can't move a share between standard and premium – you must copy the data
- Standard tiers can be changed on an existing share
- Premium supports only **LRS and ZRS** redundancy

## Size
- A standard share is up to **5 TiB** by default, or **100 TiB** with **large file shares** enabled on the storage account
- The **quota** sets the maximum size of a share

## Authentication

| Method | How it works |
|---|---|
| **Storage account key** | Full access. The portal's **Connect** script uses this. Good for labs, not for users |
| **Identity-based (Kerberos)** | Users sign in with their own identity through **AD DS**, **Microsoft Entra Domain Services**, or **Microsoft Entra Kerberos** (for hybrid users) |

### Permissions with identity-based access – two levels

| Level | Set with | Example |
|---|---|---|
| **Share level** | **Azure RBAC** roles | Storage File Data SMB Share **Reader** / **Contributor** / **Elevated Contributor** |
| **Folder and file level** | **Windows ACLs** (NTFS permissions) | Only HR can open the `HR` folder |

> **Exam tip:** a user needs access at **both** levels. RBAC lets them into the share, NTFS permissions decide what they can do inside.

## Data protection

| Feature | What it does |
|---|---|
| **Soft delete** | A deleted **share** can be restored within the retention period (1–365 days, default 7). On by default |
| **Share snapshots** | Read-only, point-in-time copies of the whole share. Up to **200** per share. Shown as **Previous Versions** in Windows |
| **Azure Backup** | Scheduled snapshots with retention, managed from a Recovery Services vault |

## Azure File Sync
Keeps an **on-premises Windows Server** in sync with an Azure file share. Users keep working on the local server, while Azure holds the full copy.

| Component | What it is |
|---|---|
| **Storage Sync Service** | The top-level Azure resource |
| **Sync group** | Defines what to sync |
| **Cloud endpoint** | The Azure file share – **one per sync group** |
| **Server endpoint** | A folder on a registered server, for example `D:\Data` |
| **Registered server** | A Windows Server with the **Azure File Sync agent** installed |

**Cloud tiering:** files that are rarely used are kept only in Azure. The local server keeps a small pointer and downloads the file when someone opens it. This saves local disk space.

**Setup order:** create Storage Sync Service → install agent and register the server → create sync group with the cloud endpoint → add server endpoint(s).

## Network security
The same options as for blob storage (see [Storage Network Security](storage-network-security.md)):
- Service endpoints and the storage firewall
- Private endpoint with sub-resource **`file`** and private DNS zone **`privatelink.file.core.windows.net`**

## Related
- [Lab: Azure File Share with CLI](../labs/azure-file-share-lab.md)
- [Storage Access](storage-access.md)
- [Storage Network Security](storage-network-security.md)
- [Storage Redundancy](storage-redundancy.md)
- [Azure File Sync](file-sync.md)
