# NAT (Network Address Translation)

[← Back to README](../README.md)

## The problem
Business-A has **50 backend servers** with private IP addresses (for example `10.0.2.5`). The servers can talk to each other internally, but not to the internet. The database server needs to download an update.

Giving each server a public IP address is not practical:
- It costs money
- The addresses must be managed
- 50 public addresses would be needed

## The solution
**NAT** lets many devices with **private** IP addresses **share one public IP address** when accessing the internet.

```
10.0.2.5  ─┐
10.0.2.6  ─┼──►  NAT (router)  ──►  8.34.217.91  ◄──►  Internet
10.0.2.7  ─┘      translates         one public IP
```

## Benefits
- Private servers stay **hidden and protected** – the internet only sees the public address
- They can still **reach the internet** for updates and downloads
- Saves public IP addresses

## Key facts
- NAT is a core function of a router
- Outbound only: servers can start connections to the internet, but the internet cannot start connections to them

## In Azure
- **Azure NAT Gateway** gives outbound internet access to all resources in a subnet through one or more public IP addresses

## Related
- [Network Fundamentals](network-fundamentals.md)
