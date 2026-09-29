# ExpressRoute and Virtual WAN

[← Back to README](../README.md)

## Rule of thumb
- **ExpressRoute** = *how* traffic gets to Azure
- **Virtual WAN** = *how* traffic moves once it is connected

They are **complementary, not competing**. Many enterprise designs use ExpressRoute as the connectivity layer and Virtual WAN as the global transit and routing layer.

---

## Azure ExpressRoute

**Analogy:** a private highway into Microsoft.

### Key facts
- **Private, dedicated connection** between your datacenter and the Microsoft network
- With **private peering**, traffic does **not** go over the public internet
- **Predictable performance**, lower jitter (latency variation), and higher reliability than internet connections
- Mainly used for **hybrid connectivity** between on-premises and Azure
- Traffic is private but **not encrypted by default** – add IPsec VPN over ExpressRoute or MACsec (ExpressRoute Direct) if encryption is required

### Peering types
| Peering | Connects to |
|---|---|
| **Private peering** | Your Azure VNets (VMs and other resources with private IPs) |
| **Microsoft peering** | Microsoft public services, such as Microsoft 365 and Azure PaaS public endpoints |

### Typical use cases
- Large enterprise datacenters
- Migrating workloads to Azure
- High-bandwidth workloads, such as SAP, databases, or VDI
- Regulatory or compliance requirements for private connectivity

```
Head Office
     |
ExpressRoute
     |
Azure Region A
```

You get a private connection to Azure, but you still design the routing between multiple regions and networks yourself.

### Connection models

**1. Service provider model** – you connect through a connectivity partner:

| Model | How it works |
|---|---|
| **CloudExchange colocation** | Your equipment is colocated at a cloud exchange facility, and you connect to Microsoft through the exchange provider |
| **Point-to-point Ethernet** | A dedicated Ethernet link from your site directly to Microsoft |
| **Any-to-any (IPVPN)** | Azure joins your company's existing WAN (MPLS network), like another branch office |

**2. ExpressRoute Direct** – you connect **directly** to Microsoft's network at a peering location, without a provider in between.
- Very high bandwidth (10 Gbps or 100 Gbps ports)
- Supports MACsec encryption
- For very large organizations with large data volumes

---

## Azure Virtual WAN

**Analogy:** a global, managed transit network.

### Key facts
- **Virtual hubs** – central, Microsoft-managed hubs in Azure regions
- Connects VNets, branch offices, site-to-site VPNs, ExpressRoute, remote users (point-to-site VPN), and security services
- Creates a **hub-and-spoke architecture** without building and managing your own transit network
- Simplifies routing and connectivity management

### SKUs
| SKU | Supports |
|---|---|
| **Basic** | Site-to-site VPN only |
| **Standard** | Everything: ExpressRoute, point-to-site VPN, VNet-to-VNet transit, hub-to-hub |

### Typical use cases
- Global enterprise networks
- Multi-region Azure deployments
- Connecting many branch offices
- Central security with Azure Firewall or partner NVAs (network virtual appliances)
- Large-scale VPN and SD-WAN deployments

```
Branch A ─────┐
Branch B ─────┼── Virtual WAN Hub ── Azure VNets
Remote users ─┘
```

You get central connectivity and routing, while the connection to Azure can use VPN or internet-based transport.

---

## Using them together

An ExpressRoute circuit connects directly to a Virtual WAN hub through an **ExpressRoute gateway** in the hub. This is a very common enterprise design.

```
     Datacenter
         |
    ExpressRoute
         |
  Virtual WAN Hub ─── Other regions
    /    |    \
 VNet1 VNet2 VNet3
```

### Example 1: Global enterprise
- Datacenter in Oslo
- Azure workloads in Norway East, West Europe, and UK South
- 50 branch offices

```
Branches → SD-WAN/VPN → Virtual WAN ← ExpressRoute ← Datacenter
```

**Benefits:** private connection to the datacenter, automatic transit routing, and simpler management than dozens of separate gateways.

### Example 2: SAP migration
Requirements: low latency, private connectivity, and disaster recovery across regions.

```
On-prem SAP users
        |
   ExpressRoute
        |
   Virtual WAN
     /      \
 Prod        DR
 region      region
```

**Benefits:** ExpressRoute gives dedicated transport, and Virtual WAN gives inter-region connectivity and central routing.

### Example 3: Retail
- 500 stores using SD-WAN
- Applications hosted in Azure

```
Stores → SD-WAN → Virtual WAN Hub ← ExpressRoute ← Headquarters
```

**Benefits:** stores reach Azure apps through Virtual WAN, headquarters connects privately through ExpressRoute, and routing rules are central.

---

## When to choose which

| Choose | When |
|---|---|
| **ExpressRoute** | You need private connectivity, predictable performance, or compliance rules discourage using the internet |
| **Virtual WAN** | You need a global network, have many locations, VNets, or regions, and want central routing management |
| **Both** | Medium-to-large enterprise with on-prem datacenters, multiple Azure regions, and a need for cloud-scale hub-and-spoke |

## Related
- [Subnets and Routing](subnets-and-routing.md)
- [Firewalls](firewalls.md)
- [Network Fundamentals](network-fundamentals.md)
