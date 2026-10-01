# Lab: Storage Account with Private Access

[← Back to README](../README.md)

## Goal
Create a storage account, manage it from a Linux VM (Azure CLI) and a Windows VM (PowerShell), then **block public access** – first with a **service endpoint**, then with a **private endpoint and private DNS zone**.

## Checklist
- [ ] 0. Connect to the VMs through the jump server
- [ ] 1. Install Azure CLI on the Linux VM
- [ ] 2. Install PowerShell and the Az module on the Windows VM
- [ ] 3. Create the storage account
- [ ] 4. Restrict access with a service endpoint
- [ ] 5. Create a private endpoint
- [ ] 6. Set up the private DNS zone
- [ ] 7. Disable public network access
- [ ] 8. Test

## Resource names

| Resource | Name |
|---|---|
| Resource group | `AZ104-catrine-rg` |
| vNet | `AZ104-catrine-storage-vnet` |
| VM subnet | `<vm-subnet>` |
| Private endpoint subnet | `<pe-subnet>` |
| Jump server (Windows) | `AZ104-vm2-jump-catrine` |
| Linux VM | `AZ104-catrine-linux-vm` |
| Windows VM | `AZ104-vm-catrine` |
| Storage account | `az104catrinesta` – globally unique, 3–24 characters, lowercase letters and numbers only |
| Private endpoint | `az104catrinesta-pe` |
| Container | `data` |

<!-- Replace <vm-subnet> and <pe-subnet> with my subnet names -->

---

## Step 0: Connect to the VMs through the jump server

In this lab, I used a **jump server** instead of Azure Bastion.

```
My PC
  │  Remote Desktop (RDP, port 3389) – public IP
  ▼
Jump server: AZ104-vm2-jump-catrine (Windows)
  ├── PuTTY (SSH, port 22) – private IP ──► AZ104-catrine-linux-vm
  └── Remote Desktop (RDP, port 3389) – private IP ──► AZ104-vm-catrine
```

### What is a jump server?
A **jump server** (also called a jump box) is a VM used as a **single entry point** for admin access. Only the jump server is reachable from the outside. The other VMs only have **private IP addresses** and are reached from the jump server.

### How to connect
1. **My PC → jump server:** open **Remote Desktop Connection** (`mstsc`) and connect to the jump server's **public IP**
2. **Jump server → Linux VM:** open **PuTTY**, enter the Linux VM's **private IP**, port **22**, connection type **SSH**
3. **Jump server → Windows VM:** open **Remote Desktop Connection** and connect to the Windows VM's **private IP**

### NSG rules

| VM | Allow inbound | From |
|---|---|---|
| Jump server | TCP 3389 (RDP) | **My own public IP only** – never "Any" |
| Linux VM | TCP 22 (SSH) | The jump server's private IP |
| Windows VM | TCP 3389 (RDP) | The jump server's private IP |

### Jump server vs Azure Bastion

| | Jump server (VM) | Azure Bastion |
|---|---|---|
| What it is | A VM you manage yourself | A managed Azure service |
| Public IP on a VM | Yes, on the jump server | No – connect through the Azure portal |
| Open RDP/SSH ports to the internet | Yes, on the jump server | No |
| Patching and maintenance | You | Microsoft |
| Required subnet | Any | `AzureBastionSubnet` (/26 or larger) |

> **Security tip:** A jump server with RDP open to the internet is a common attack target. Limit the NSG rule to your own IP, or use **Just-in-time (JIT) VM access** in Microsoft Defender for Cloud to open the port only when needed.

---

## Step 1: Install Azure CLI on the Linux VM

Connect to `AZ104-catrine-linux-vm` with **PuTTY** from the jump server, then run (Ubuntu/Debian):

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az version
```

Sign in:

```bash
az login --use-device-code   # sign in with your own account in a browser
az login --identity          # or: sign in with the VM's managed identity
```

> **Tip:** with `--use-device-code`, the Linux VM shows a code. Open the link in a browser (on the jump server or your PC) and enter the code there.

## Step 2: Install PowerShell and the Az module on the Windows VM

Connect to `AZ104-vm-catrine` with **Remote Desktop** from the jump server.

**Install PowerShell 7** (in Windows PowerShell as administrator):

```powershell
winget install --id Microsoft.PowerShell --source winget
```

If `winget` is missing (common on Windows Server), download the PowerShell 7 MSI installer from Microsoft instead.

**Install the Az module** (in PowerShell 7):

```powershell
Install-Module -Name Az -Repository PSGallery -Scope CurrentUser -Force
```

Sign in:

```powershell
Connect-AzAccount -UseDeviceAuthentication   # your own account
Connect-AzAccount -Identity                  # or: the VM's managed identity
```

> **Windows PowerShell 5.1 vs PowerShell 7:** 5.1 is built into Windows. PowerShell 7 is the newer, cross-platform version and is recommended for the Az module.

## Step 3: Create the storage account

**Azure CLI**
```bash
az storage account create \
  --name az104catrinesta \
  --resource-group AZ104-catrine-rg \
  --location norwayeast \
  --sku Standard_LRS \
  --kind StorageV2 \
  --allow-blob-public-access false
```

**PowerShell**
```powershell
New-AzStorageAccount -ResourceGroupName AZ104-catrine-rg -Name az104catrinesta `
  -Location norwayeast -SkuName Standard_LRS -Kind StorageV2 `
  -AllowBlobPublicAccess $false
```

Create a container to test with:
```bash
az storage container create --account-name az104catrinesta --name data --auth-mode login
```

> `--auth-mode login` uses Entra ID, so your account needs a data role such as **Storage Blob Data Contributor**.

## Step 4: Restrict access with a service endpoint

```bash
# Enable the service endpoint on the subnet
az network vnet subnet update -g AZ104-catrine-rg --vnet-name AZ104-catrine-storage-vnet \
  --name <vm-subnet> --service-endpoints Microsoft.Storage

# Allow the subnet in the storage firewall
az storage account network-rule add -g AZ104-catrine-rg --account-name az104catrinesta \
  --vnet-name AZ104-catrine-storage-vnet --subnet <vm-subnet>

# Block everything else
az storage account update -g AZ104-catrine-rg -n az104catrinesta --default-action Deny
```

**Test:** the VMs in `<vm-subnet>` can list blobs. Your own PC can't (unless you add your IP).

## Step 5: Create a private endpoint

```bash
STORAGE_ID=$(az storage account show -g AZ104-catrine-rg -n az104catrinesta --query id -o tsv)

az network private-endpoint create -g AZ104-catrine-rg \
  --name az104catrinesta-pe \
  --vnet-name AZ104-catrine-storage-vnet --subnet <pe-subnet> \
  --private-connection-resource-id $STORAGE_ID \
  --group-id blob \
  --connection-name az104catrinesta-pe-conn
```

`--group-id blob` = the sub-resource. File, queue, and table would each need their own private endpoint.

## Step 6: Set up the private DNS zone

```bash
# 1. Create the zone
az network private-dns zone create -g AZ104-catrine-rg \
  --name privatelink.blob.core.windows.net

# 2. Link it to the vNet
az network private-dns link vnet create -g AZ104-catrine-rg \
  --zone-name privatelink.blob.core.windows.net \
  --name link-AZ104-catrine-storage-vnet \
  --virtual-network AZ104-catrine-storage-vnet \
  --registration-enabled false

# 3. Create the A record automatically (DNS zone group)
az network private-endpoint dns-zone-group create -g AZ104-catrine-rg \
  --endpoint-name az104catrinesta-pe \
  --name default \
  --private-dns-zone privatelink.blob.core.windows.net \
  --zone-name blob
```

In the portal, choosing **Integrate with private DNS zone = Yes** when creating the private endpoint does all three steps for you.

## Step 7: Disable public network access

```bash
az storage account update -g AZ104-catrine-rg -n az104catrinesta --public-network-access Disabled
```

Now the storage account can **only** be reached through the private endpoint.

## Step 8: Test

**DNS – from the Linux VM (PuTTY) or Windows VM:**
```bash
nslookup az104catrinesta.blob.core.windows.net
```
Expected: an alias to `az104catrinesta.privatelink.blob.core.windows.net` and a **private IP** (10.x.x.x).

From your own PC, the same command returns a **public IP** – and access is denied.

**Data access – Linux VM:**
```bash
az storage blob list --account-name az104catrinesta --container-name data --auth-mode login -o table
```

**Data access – Windows VM:**
```powershell
$ctx = New-AzStorageContext -StorageAccountName az104catrinesta -UseConnectedAccount
Get-AzStorageBlob -Container data -Context $ctx
```

---

## Troubleshooting
- **Can't connect to the jump server** → check that the NSG allows RDP 3389 from your current public IP. Your IP can change, for example on a different network
- **PuTTY times out** → check that the Linux VM's NSG allows port 22 from the jump server, and that you used the **private** IP
- **"This request is not authorized to perform this operation"** → the request comes from a network that isn't allowed. Check the firewall settings and where you're connecting from
- **`nslookup` returns a public IP from the VM** → the private DNS zone isn't linked to the vNet, or the A record is missing
- **"AuthorizationPermissionMismatch"** → the network is fine, but your identity is missing a data role, such as Storage Blob Data Reader

## Clean up
```bash
az group delete --name AZ104-catrine-rg --yes --no-wait
```

> *!!!* This deletes **everything** in the resource group, including the VMs and the vNet. Only run it when finished.

## Related
- [Storage Network Security](../03-storage/storage-network-security.md)
- [Storage Access](../03-storage/storage-access.md)
- [Managed Identities](../02-identity/managed-identities.md)
