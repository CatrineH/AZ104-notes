# Firewalls

[← Back to README](../README.md)

## What is it?
Firewalls **control what traffic is allowed where**. Routing makes it *possible* for subnets to talk; firewalls decide what is *allowed*.

## Types by location

| Type | Where | Example rule |
|---|---|---|
| **Host firewall** | On an individual server | Only accept connections on port 3306, and only from IP addresses in the frontend subnet |
| **Network firewall** | Between network segments | Allow incoming traffic on port 443, block everything else |

## Types by how they inspect traffic

| Type | Analogy | How it works |
|---|---|---|
| **Stateless** | "Do you have the key?" | Checks **each packet on its own** against the rules. Remembers nothing, so return traffic needs its own rule |
| **Stateful** | "Where are you going?" | **Tracks connections.** If a request is allowed out, the reply is automatically allowed back in |
| **Application firewall** | "Where are you going, and what are you doing there?" | Inspects the **content** of the traffic (layer 7), such as the website name, URL, or HTTP request |

### Stateful vs stateless

| | Stateless | Stateful |
|---|---|---|
| Remembers connections | No | Yes |
| Return traffic | Needs its own rule | Allowed automatically |
| Speed | Faster, simpler | Slightly more work per connection |
| Security | Lower | Higher |
| Examples | Simple router access lists (ACLs) | NSGs, Azure Firewall |

> **Exam tip:** NSGs are **stateful**. If you allow inbound traffic on port 443, you don't need an outbound rule for the reply.

## In Azure

| Azure service | Type | Notes |
|---|---|---|
| **Network Security Group (NSG)** | Stateful, basic network firewall | Filters by IP, port, and protocol. Attached to a subnet or a network interface (NIC). Free |
| **Azure Firewall** | Stateful, managed, central firewall | Filters by IP, port, **and domain name**. Usually placed in a hub vNet |
| **Web Application Firewall (WAF)** | Application firewall | Protects web apps against attacks like SQL injection. Runs on Application Gateway or Front Door |

### NSG basics
- Rules have a **priority** (100–4096). **Lower number = checked first.**
- The first rule that matches wins
- Default **inbound** rules: allow traffic from inside the vNet and from the Azure load balancer, **deny everything else** (including the internet)
- Default **outbound** rules: allow traffic to the vNet and **to the internet**
- So by default, **outbound traffic is not restricted**, but inbound traffic from the internet **is blocked**

### Azure Firewall rules
Rules are managed through **Firewall Policy** (recommended) or classic rules.

| Rule type | Filters on | Example |
|---|---|---|
| **DNAT rules** | Incoming traffic from the internet, translated to a private IP | Forward port 3389 on the public IP to a VM |
| **Network rules** | IP address, port, and protocol | Allow TCP 1433 from 10.0.1.0/24 to 10.0.3.4 |
| **Application rules** | Domain names (FQDNs) | Allow `vy.no`, deny `test.com` |

- Processing order: **DNAT → Network → Application**
- Azure Firewall **denies all traffic by default** – you must allow what you need

## Related
- [Ports and Protocols](ports-and-protocols.md)
- [Subnets and Routing](subnets-and-routing.md)
- [ExpressRoute and Virtual WAN](expressroute-and-virtual-wan.md)
