# Subnets and Routing

[← Back to README](../README.md)

## Subnets

**Network segmentation** divides a network into zones.

- A virtual network can be divided into one or more subnets
- Subnets are logical divisions within the network
- Subnets improve **security**, **performance**, and **manageability**
- Each subnet must have a **unique address range** that does not overlap with other subnets in the same vNet

**Analogy:** A hospital with separate departments.

### Example: three-tier app

| Subnet | Purpose | Address range |
|---|---|---|
| subnet1 | Frontend | 10.0.1.0/24 |
| subnet2 | Application | 10.0.2.0/24 |
| subnet3 | Database | 10.0.3.0/24 |

For how address ranges and CIDR work, see [IP Addressing and Subnetting](ip-addressing-subnetting.md).

## Routing

**Routing** directs traffic between different network segments.

**Rule:** Devices in different subnets need a router to talk to each other.

### In Azure
- Azure creates **system routes** automatically. Subnets in the **same vNet** can talk to each other by default – you do not need to set up a router.
- **User-Defined Routes (UDR)** override system routes, for example to send all traffic through a firewall.
- Separate vNets **cannot** talk to each other by default. They need **vNet peering** or a VPN gateway.
- vNets that will be connected (for example with peering) must **not** have overlapping address ranges.

## Related
- [Firewalls](firewalls.md) – routing allows traffic, firewalls decide what is permitted
- [IP Addressing and Subnetting](ip-addressing-subnetting.md)
