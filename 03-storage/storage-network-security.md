# Storage Network Security

[← Back to README](../README.md)

How to control **which networks** can reach a storage account. This works **together with** authorization (keys, SAS, Entra ID): a request must come from an allowed network **and** be authorized.

## Public network access – three options

Portal: **Storage account → Networking → Public network access**

| Option | What it means |
|---|---|
| **Enabled from all networks** | Default. Anyone on the internet can reach the endpoint (but still needs a key, SAS, or Entra ID access) |
| **Enabled from selected virtual networks and IP addresses** | Storage firewall: only the subnets and IP addresses you list are allowed |
| **Disabled** | No public access at all. Only **private endpoints** work |

> *!!!* When you restrict access, also add **your own client IP address**, or you will get "This request is not authorized" when browsing data in the portal from your PC.

**Exceptions:** you can allow **trusted Azure services** (for example Azure Backup) to bypass the firewall.

---

## Service endpoints

Lets a **subnet** reach the storage account over the **Azure backbone network**, and lets the storage firewall recognize that subnet.

```
VM in snet-vm ──(Azure backbone)──► mystorage.blob.core.windows.net
                                     (still a public IP)
```

**How to set it up:**
1. Enable the service endpoint **`Microsoft.Storage`** on the subnet
2. Add the vNet and subnet to the storage account firewall
3. Set public network access to **Enabled from selected virtual networks and IP addresses**

The portal enables the service endpoint on the subnet automatically when you add the subnet in step 2.

**Key facts:**
- The storage account **keeps its public IP address** – traffic just takes an optimized route
- The source of the traffic is seen as the VM's **private IP** and subnet
- **Free**
- Only works for resources **in that subnet** – not from on-premises or peered vNets

---

## Private endpoints

Gives the storage account a **private IP address inside your vNet**. The storage account is reached as if it were a local resource.

```
VM in snet-vm ──► 10.10.3.4 (private endpoint in snet-pe) ──► storage account
```

**Key facts:**
- Built on **Azure Private Link**
- The private endpoint is a **network interface (NIC)** with a private IP from your subnet
- Works from the vNet, **peered vNets**, and **on-premises** (through VPN or ExpressRoute)
- Lets you set public network access to **Disabled**
- **Costs money** (per hour and per GB)
- **Needs DNS** configuration (see below)
- One private endpoint per **sub-resource**: `blob`, `file`, `queue`, `table`, `dfs`, `web`

---

## Private DNS zones

Apps still connect to the normal name, `mystorage.blob.core.windows.net`. DNS must return the **private IP** instead of the public IP.

### How the name is resolved

```
mystorage.blob.core.windows.net
        │  CNAME (created by Azure)
        ▼
mystorage.privatelink.blob.core.windows.net
        │  A record in the private DNS zone
        ▼
10.10.3.4
```

### What you need
1. A **private DNS zone** named `privatelink.blob.core.windows.net`
2. An **A record** for the storage account pointing to the private IP (created automatically with a DNS zone group)
3. A **virtual network link** from the zone to every vNet that needs to resolve the name

### Zone names per storage service

| Sub-resource | Private DNS zone |
|---|---|
| Blob | `privatelink.blob.core.windows.net` |
| File | `privatelink.file.core.windows.net` |
| Queue | `privatelink.queue.core.windows.net` |
| Table | `privatelink.table.core.windows.net` |
| Data Lake (dfs) | `privatelink.dfs.core.windows.net` |

---

## Service endpoint vs private endpoint

| | Service endpoint | Private endpoint |
|---|---|---|
| Storage IP address | Public | **Private, in your vNet** |
| Configured on | The subnet | A NIC in a subnet |
| Access from peered vNets | No | Yes |
| Access from on-premises | Np | Yes (VPN/ExpressRoute) |
| Public access can be disabled | No | Yes |
| DNS changes needed | No | Yes |
| Cost | Free | Paid |

> **Exam tip:** If the question mentions **on-premises access**, **disabling public access**, or a **private IP**, the answer is a **private endpoint**. If it only needs a subnet in Azure to reach storage cheaply, a **service endpoint** is enough.

## Related
- [Lab: Storage account with private access](../labs/storage-private-access-lab.md)
- [Storage Access](storage-access.md)
- [DNS](../01-networking/dns.md)
- [Subnets and Routing](../01-networking/subnets-and-routing.md)
