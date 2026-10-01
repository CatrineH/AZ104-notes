# Virtual Networks and VNet Peering

[← Back to README](../README.md)

## Virtual network (VNet)
A **VNet** is a private network in Azure – like a company's office network, but in the cloud.

- Resources in the **same VNet** can communicate with each other by default
- A VNet is divided into **subnets** for segmentation and security (for example frontend, backend, database)
- A VNet belongs to **one region** and **one subscription**
- Separate VNets **cannot** communicate by default

```
Azure
└── VNet
    ├── Frontend subnet
    ├── Backend subnet
    └── Database subnet
```

## VNet peering
**Peering** connects two VNets privately, so their resources can talk to each other using **private IP addresses**.

**Analogy:** a private highway between two networks.

```
Before:   VNet A   ✕   VNet B
After:    VNet A ◄────► VNet B
```

### Benefits
- **Low latency** – traffic uses the **Azure backbone network**, never the public internet
- **No VPN gateway** required
- Works across subscriptions and Microsoft Entra tenants

### Types

| Type | Connects |
|---|---|
| **VNet peering** | VNets in the **same region** |
| **Global VNet peering** | VNets in **different regions** |

### Key facts for the exam
- **Peering must be created in both directions.** One link from A to B and one from B to A. Status goes from *Initiated* to **Connected** when both exist
- **Address spaces cannot overlap** – for example, two VNets with 10.0.0.0/16 can't be peered
- **Peering is not transitive.** If A ↔ B and B ↔ C, then A **cannot** reach C. You need A ↔ C, or a hub with a firewall/NVA and routing
- **Data transfer costs money** – inbound and outbound traffic over the peering is billed
- **NSGs still apply.** The default NSG rule `AllowVnetInBound` includes peered VNets, so traffic is allowed unless you add a deny rule

### Peering settings

| Setting | What it does |
|---|---|
| **Allow access to remote VNet** | Allows traffic between the VNets (on by default) |
| **Allow forwarded traffic** | Accepts traffic that didn't start in the peered VNet, for example from an NVA |
| **Allow gateway transit** | Lets the peered VNet use **this** VNet's VPN/ExpressRoute gateway |
| **Use remote gateways** | Uses the **peered** VNet's gateway instead of having its own |

> **Hub-and-spoke:** one hub VNet with a VPN gateway is peered to many spoke VNets. The hub enables **gateway transit**, and the spokes enable **use remote gateways**. Then all spokes can reach on-premises through one gateway.

### Peering vs VPN gateway

| | VNet peering | VNet-to-VNet VPN |
|---|---|---|
| Traffic path | Azure backbone | Encrypted tunnel through a VPN gateway |
| Speed | Highest, lowest latency | Limited by the gateway SKU |
| Setup | Simple | Needs a gateway in each VNet (20–45 min each) |
| Cost | Data transfer only | Gateway hours + data transfer |

## Related
- [Lab: VNet Peering with PowerShell](../labs/vnet-peering-lab.md)
- [Subnets and Routing](subnets-and-routing.md)
- [ExpressRoute and Virtual WAN](expressroute-and-virtual-wan.md)
