# Firewalls

[← Back to README](../README.md)

## What is it?
Firewalls **control what traffic is allowed where**. Routing makes it *possible* for subnets to talk; firewalls decide what is *allowed*.

## Types

| Type | Where | Example rule |
|---|---|---|
| **Host firewall** | On an individual server | Only accept connections on port 3306, and only from IP addresses in the frontend subnet |
| **Network firewall** | Between network segments | Allow incoming traffic on port 443, block everything else |

## In Azure

| Azure service | Works like | Notes |
|---|---|---|
| **Network Security Group (NSG)** | Basic network firewall | Allow/deny rules by IP, port, and protocol. Attached to a subnet or a network interface (NIC) |
| **Azure Firewall** | Advanced, central network firewall | Managed service with more features, such as filtering by domain name |

### NSG basics
- Rules have a **priority** (100–4096). **Lower number = checked first.**
- The first rule that matches wins
- Default rules allow traffic inside the vNet and deny inbound traffic from the internet

## Related
- [Ports and Protocols](ports-and-protocols.md)
- [Subnets and Routing](subnets-and-routing.md)
