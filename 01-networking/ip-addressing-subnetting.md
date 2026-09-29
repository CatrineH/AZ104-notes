# IP Addressing and Subnetting

[← Back to README](../README.md)

## IP addressing
- An IPv4 address has **32 bits**, split into 4 octets of 8 bits
- Each octet is written in decimal (0–255)
- Bit positions in an octet: `128 64 32 16 8 4 2 1`

Example: `10` in binary = `00001010`

## CIDR notation
- The number after `/` = how many bits are used for the **network**
- The remaining bits are for **hosts**
- Mask bits: **1 = on (network)**, **0 = off (host)**

| CIDR | Host bits | Total addresses |
|---|---|---|
| /24 | 8 | 256 |
| /27 | 5 | 32 |
| /28 | 4 | 16 |
| /29 | 3 | 8 |

Formula: total addresses = 2^(host bits)

## Subnetting in Azure (/29 example)
Azure reserves **5 addresses** in every subnet. The smallest subnet Azure allows is **/29**.

| Address | Purpose |
|---|---|
| 10.10.1.0 | Network address |
| 10.10.1.1 | Default gateway |
| 10.10.1.2 | Azure DNS |
| 10.10.1.3 | Azure DNS |
| 10.10.1.4 – 10.10.1.6 | Usable hosts (3) |
| 10.10.1.7 | Broadcast |

Usable addresses in Azure = total addresses − 5

## Related
- [Naming Conventions](naming-conventions.md)
- [DNS](dns.md)
