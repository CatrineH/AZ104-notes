# Network Fundamentals – Overview

[← Back to README](../README.md)

This topic follows **Business-A**, a company whose networking needs grow over time. The goal is to understand **why each networking piece exists** and which real problem it solves.

## How the pieces build on each other

| Step | Problem | Solution | Notes |
|---|---|---|---|
| 1 | Users need to find the website | DNS | [dns.md](dns.md) |
| 2 | One server runs several apps | Ports | [ports-and-protocols.md](ports-and-protocols.md) |
| 3 | Data must be delivered by shared rules | TCP / UDP | [ports-and-protocols.md](ports-and-protocols.md) |
| 4 | Everything is on one flat network | Subnets | [subnets-and-routing.md](subnets-and-routing.md) |
| 5 | Subnets need to talk to each other | Routing | [subnets-and-routing.md](subnets-and-routing.md) |
| 6 | Not everything should talk to everything | Firewalls | [firewalls.md](firewalls.md) |
| 7 | Private servers need internet access | NAT | [nat.md](nat.md) |
| 8 | Buying and running servers is slow | The cloud | See below |
| 9 | Apps run in containers | Container networking | [container-networking.md](container-networking.md) |

## Moving to the cloud

### Why?
On-premises, the company must:
- Predict capacity months in advance
- Buy, install, and maintain servers
- Wait weeks before new capacity is ready

In the cloud, resources are created in minutes and you pay for what you use.

### The concepts stay the same
The cloud still uses the same networking concepts. Only the names change:

| Concept | Azure service |
|---|---|
| Network | Virtual Network (vNet) |
| Subnets | Subnets in a vNet |
| Routing | System routes, User-Defined Routes (UDR) |
| Firewalls | Network Security Groups (NSG), Azure Firewall |
| NAT | NAT Gateway |
| DNS | Azure DNS, Azure Private DNS |
| IP addresses | Private and public IP addresses |
| Ports | Allowed or blocked in NSG rules |
