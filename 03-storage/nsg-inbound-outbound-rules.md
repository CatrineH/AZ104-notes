# NSG Inbound and Outbound Rules

[<- Back to README](../README.md)

## What are they?
A **Network Security Group (NSG)** filters traffic with **security rules**:

| Direction | Controls traffic.. | Example |
|---|---|---|
|**Inbound** | **Coming in** to a subnet or VM | Allow RDP (3389) from my IP to a VM |
| **Outbound** | **Going out** from a subnet or VM | Deny the database subnet access to the internet |

The direction is always seen ''from the VM's or subnet's point og view**

---

## Rule properties

| Property | Options / example | 
|---|---|
| **Name** | `Allow-HTTP-Inbound` |
| **Priority** | **100-4096** | **Lower numer = checked first** |
| **Source** | IP address, CIDR range, **service tag**, ** application security group (ASG)**, or Any |
| **Source port** | Usually `*` (clients use random ports) |
| **Destination** | IP address, CIDR range, service tag, ASG, or Any |
| **Destination port** | `443`, `80,443`, or range like `8000-8100` |
| **Protocol** | TCP, UDP, ICMP, ESP, AH, or Any |
| **Action** | **Allow** or **Deny** |

> **Tip:** the **source port** is almost always `*`. The **destination port** is the port the service listens on.

## How rules are evaluated
1. Rules are checked in **priority order**, lowest number first
2. The **first rule that matches** is used - processing **stops** there
3. If nothing you created matches, the **default rules** decide

## Example
| Priority | Name | Port | Source | Action |
|---|---|---|---|---|
| 100 | Allow-RDP-MyIP | 3389 | 192.168.2.10 | Allow |
| 200 | Deny-RDP-All | 3389 | Any | Deny |

 - RDP from **168.192.2.10** -> matches 100 -> ''allowed*
 - RDP from any other IP -> skips 100, matches 200 -> **denied**
 - If the priorities were swapped, **everyone** would be denied - including me

---

## Default rules
Every NSG has default rules. I **can't delete** them, but I can **override** them with my own rules, since my always have a lower number

### Inbound

| Priority | Name | Source -> Destination | Action |
|---|---|---|---|
| 6500 | AllowVnetInBound | VirtualNetwork -> VirtualNetwork | Allow |
| 65001 | AllowAzureLoadBalancerInbound | AzureLoadBalancer -> Any | Allow |
| 65500 | **DenyAllInBound** | Any -> Any | **Deny** |

### Outbound

| Priority | Name | Source -> Destination | Action |
|---|---|---|---|
| 6500 | AllowVnetOutBound | VirtualNetwork -> VirtualNetwork | Allow |
| 65001 | **AllowInternetOutBound** | Any -> Internet | **Allow** |
| 65500 | DenyAllOutBound | Any - > Any | Deny |


### WHat this means
- **Inbound from the internet is blocked** by default
- **Outbound to the internet is allowed** by default
- Traffic **inside the VNet** is allowed both ways - and `VirtualNetwork` includes ** Peered VNets** and connected on-premises networks

---

## NSGs are stateful
If a connection is allowed in **one** direction, the **reply** is allowed back automatically.

- Allow inbound 443 -> the response to the client goes out ''without'' an outbound rule
- Allow outbound 443 (default) -> the answer from the website comes in **without** ab inbound rule

Only I write rules for **who starts** the connection

---

## Subnet NSG and NIC NSG together

An NSG can be attached to a **subnet**, a **network interface (NIC)**, or both.

```
Inbound:   Internet ──► Subnet NSG ──► NIC NSG ──► VM
Outbound:  VM ──► NIC NSG ──► Subnet NSG ──► Internet
```

- When both exist, traffic must be **allowed by both**
- **Inbound:** subnet NSG first, then NIC NSG
- **Outbound:** NIC NSG first, then subnet NSG
- Best practice: use **subnet NSGs** for most rules – easier to manage

---

## Service tags
A **service tag** is a name for a group of IP addresses managed by Microsoft. You don't need to know or update the IPs yourself.

| Service tag | Means |
|---|---|
| `Internet` | Everything outside Azure's virtual networks |
| `VirtualNetwork` | The VNet, peered VNets, and connected on-premises networks |
| `AzureLoadBalancer` | Azure's load balancer health probes |
| `Storage` / `Storage.WestEurope` | Azure Storage (all regions / one region) |
| `Sql` | Azure SQL Database |
| `GatewayManager` | Management traffic for Application Gateway and VPN gateways |
| `AzureCloud` | All Azure public IP addresses |

Example: allow a VM to reach **only** storage in West Europe, and nothing else on the internet:

| Priority | Direction | Destination | Port | Action |
|---|---|---|---|---|
| 100 | Outbound | `Storage.WestEurope` | 443 | Allow |
| 200 | Outbound | `Internet` | Any | Deny |

---

## Application security groups (ASGs)
An **ASG** groups VMs by **role** instead of by IP address. You use the ASG as source or destination in rules.

```
asg-web  ← web VMs' NICs
asg-db   ← database VMs' NICs
```

| Priority | Direction | Source | Destination | Port | Action |
|---|---|---|---|---|---|
| 100 | Inbound | Internet | `asg-web` | 443 | Allow |
| 110 | Inbound | `asg-web` | `asg-db` | 1433 | Allow |

- New VMs only need to be added to the right ASG – **the rules don't change**
- All NICs in an ASG must be in the **same VNet**

---

## Example: three-tier app

| Subnet | Inbound rule | Outbound rule |
|---|---|---|
| **Frontend** | Allow 443 from `Internet` | Default |
| **Application** | Allow 8080 from the frontend subnet | Default |
| **Database** | Allow 1433 from the application subnet. Deny everything else from `VirtualNetwork` | Deny `Internet` |

> In the database subnet, the deny rule is needed because the default **AllowVnetInBound** would otherwise let the frontend reach the database directly.

---

## Create rules

**Azure CLI**
```bash
az network nsg rule create \
  --resource-group AZ104-catrine-rg \
  --nsg-name nsg-web \
  --name Allow-HTTPS-Inbound \
  --priority 100 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --source-address-prefixes Internet \
  --source-port-ranges '*' \
  --destination-address-prefixes '*' \
  --destination-port-ranges 443
```

**PowerShell**
```powershell
$nsg = Get-AzNetworkSecurityGroup -Name nsg-web -ResourceGroupName AZ104-catrine-rg

$nsg | Add-AzNetworkSecurityRuleConfig `
  -Name "Deny-Internet-Outbound" `
  -Direction Outbound `
  -Priority 4000 `
  -Access Deny `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange *

$nsg | Set-AzNetworkSecurityGroup
```

> In PowerShell, `Add-AzNetworkSecurityRuleConfig` only changes the object in memory. **`Set-AzNetworkSecurityGroup`** saves it to Azure.

---

## Troubleshooting tools

| Tool | Use it to |
|---|---|
| **Effective security rules** (on the NIC) | See the **combined** rules from both the subnet NSG and the NIC NSG |
| **IP flow verify** (Network Watcher) | Test if a specific packet is allowed or denied – and **which rule** decided it |
| **VNet flow logs** (Network Watcher) | Log all traffic for later analysis (replacing the older NSG flow logs) |

### Checklist when traffic is blocked
1. Is there an NSG on **both** the subnet and the NIC? Both must allow it
2. Is a **deny rule with a lower number** matching first?
3. Is the **destination port** correct (the service's port, not the client's)?
4. Is the **OS firewall** (Windows Firewall, `ufw`) blocking it? The NSG isn't the only firewall
5. Use **IP flow verify** to find the rule

---

## Exam tips
- **Lower priority number = higher priority**. First match wins
- Default: **inbound internet denied**, **outbound internet allowed**, **VNet traffic allowed**
- NSGs are **stateful** – no rule needed for return traffic
- Subnet + NIC NSG → **both must allow**
- Default rules can't be deleted, only overridden
- "Which rule blocks the traffic?" → **IP flow verify** or **effective security rules**

## Related
- [Firewalls](firewalls.md)
- [Ports and Protocols](ports-and-protocols.md)
- [Subnets and Routing](subnets-and-routing.md)
- [Virtual Networks and VNet Peering](vnet-peering.md)


