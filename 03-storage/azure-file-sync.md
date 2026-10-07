
# Azure File Sync

[← Back to README](../README.md)

## What is it?
**Azure File Sync** keeps **on-premises Windows file servers** in sync with an **Azure file share**.

- Users keep working on their **local file server** – same speed, same drive letters, same NTFS permissions
- **Azure holds the full copy** of all files
- Several servers in different offices can sync to the **same** Azure file share

```
Office Oslo                    Azure                     Office Bergen
Windows Server ◄──sync──► Azure file share ◄──sync──► Windows Server
  D:\Data                  (full copy)                  D:\Data
```

## Why use it?

| Use case | How Azure File Sync helps |
|---|---|
| **Cloud tiering** | Keep only frequently used files on the local server, the rest in Azure – saves local disk space |
| **Multi-site sync** | Several offices work on the same files through their own local server |
| **Replace DFS Replication (DFS-R)** | Azure becomes the hub instead of server-to-server replication |
| **Backup** | Back up the Azure file share with Azure Backup instead of backing up every server |
| **Disaster recovery** | If a server fails, set up a new one and the files come back from Azure |
| **Migration** | Move file server data to Azure while users keep working |

---

## Components

| Component | What it is |
|---|---|
| **Storage Sync Service** | The top-level Azure resource. Everything else is created inside it |
| **Sync group** | Defines **what** to sync – which share and which server folders |
| **Cloud endpoint** | The **Azure file share** in a sync group |
| **Registered server** | A Windows Server with the **Azure File Sync agent** installed, registered to the Storage Sync Service |
| **Server endpoint** | A **folder or volume** on a registered server, for example `D:\Data` |

```
Storage Sync Service
├── Registered servers
│   ├── FS-SERVER1
│   └── FS-SERVER2
└── Sync group: "CompanyData"
    ├── Cloud endpoint  → Azure file share "companydata"
    ├── Server endpoint → FS-SERVER1   D:\Data
    └── Server endpoint → FS-SERVER2 D:\Data
```

## Rules and limits (exam favorites)

- A sync group has **exactly one cloud endpoint** but can have **many server endpoints**
- A server can be registered to **only one Storage Sync Service** at a time
- A server can have **several server endpoints**, in **different sync groups**, as long as the paths **don't overlap**
- The **Storage Sync Service** and the **storage account** must be in the **same region**
- Server endpoints must be on **NTFS** volumes
- **Cloud tiering is not supported on the system volume** (usually `C:`)
- The agent supports **Windows Server only** – not Linux, not Windows 10/11

---

## Setup order

| Step | What to do | Where |
|---|---|---|
| 1 | Create a **storage account** and an **Azure file share** | Azure portal |
| 2 | Create the **Storage Sync Service** | Azure portal |
| 3 | **Install the Azure File Sync agent** on the Windows Server | The server |
| 4 | **Register the server** with the Storage Sync Service (the agent opens a sign-in window after install) | The server |
| 5 | Create a **sync group** and choose the file share as the **cloud endpoint** | Azure portal |
| 6 | Add a **server endpoint** (server + folder path), and choose cloud tiering settings | Azure portal |

> **Exam tip:** questions often ask for the correct order. Remember: **share → Storage Sync Service → agent → register → sync group → server endpoint.**

### Network requirements
- The agent communicates over **HTTPS (TCP 443) outbound** only
- **Port 445 (SMB) is not needed** for sync – that's only for mounting the share directly

---

## Cloud tiering

With cloud tiering, the local server works like a **cache**. Popular files stay local. Rarely used files are kept **only in Azure**, and the server keeps a small **pointer** (a "tiered file").

- To users, tiered files look like normal files in the folder
- When someone opens a tiered file, it is **downloaded automatically** ("recalled")
- Tiered files have the **Offline** attribute set

### Two policies

| Policy | What it does | Example |
|---|---|---|
| **Volume free space policy** | Keeps a percentage of the volume **free** by tiering the least used files | Keep 20% free on `D:` |
| **Date policy** | Tiers files that haven't been used for a number of days | Tier files not opened in 60 days |

- Both can be used together
- If they conflict, the **volume free space policy wins**

---

## How sync works

| Change made on | When it syncs |
|---|---|
| A **server endpoint** | Almost immediately – the agent detects changes on the server |
| The **Azure file share directly** (portal, SMB mount, REST) | Within **24 hours** – a change detection job scans the share once a day |

> **Exam tip:** if a file is changed directly in the Azure file share and doesn't appear on the servers right away, that's expected. It can take up to 24 hours.

### Sync conflicts
If the same file is changed in two places at the same time, **both versions are kept**:
- The latest change keeps the original name
- The other version is saved as `FileName-ServerName.ext`

---

## Backup and recovery
- Back up the **Azure file share** with **Azure Backup**, not each server
- Don't use on-premises backup software that reads every file on a server with cloud tiering – it will recall all tiered files
- **Disaster recovery:** install the agent on a new server, register it, and add a server endpoint. The folder structure comes back first, then files are downloaded as they are used

---

## Azure Files vs Azure File Sync

| | Azure Files | Azure File Sync |
|---|---|---|
| What it is | The file share in Azure | A service that syncs servers with that share |
| Users connect to | The Azure share (SMB, port 445) | Their **local** Windows Server |
| Needs a local server | No | Ye - Windows Server with agent |
| Speed for users | Depends on the internet connection | Local network speed |
| Works without Azure Files | – | No - Always needs an Azure file share |

## Related
- [Azure Files](azure-files.md)
- [Lab: Azure File Share with CLI](../labs/azure-file-share-lab.md)
- [Storage Redundancy](storage-redundancy.md)
