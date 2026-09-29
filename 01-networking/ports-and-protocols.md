# Ports and Protocols

[← Back to README](../README.md)

## Ports

One server can run several apps, for example a web app, a MySQL database, and a payment service. Each app **listens on its own port**.

- Every app needs a port
- A port number can only be used by **one app at a time** on a device
- Port range: 0–65535

**Analogy:** IP address = the building, port = the apartment.
A complete destination = **IP address + port**, for example `10.0.1.4:443`.

### Standard ports

| Port | Protocol / service | Used for |
|---|---|---|
| 22 | SSH | Remote admin access to Linux |
| 53 | DNS | Name lookups |
| 80 | HTTP | Web traffic (unencrypted) |
| 443 | HTTPS | Web traffic (encrypted) |
| 3306 | MySQL | Database |
| 3389 | RDP | Remote admin access to Windows |
| 9090 | Custom | Example: payment service API |

> **Exam tip:** Know 22, 80, 443, and 3389 by heart – they appear in NSG rule questions.

## Protocols

Protocols are **shared delivery rules** for how data is sent.

| | TCP | UDP |
|---|---|---|
| Analogy | Registered mail | Postcard |
| Delivery confirmed | Yes | No |
| Resends lost data | Yes | No |
| Keeps order | Yes | No |
| Speed | Slower | Faster |
| Used for | Web pages, file transfer, email | Video calls, streaming, gaming, DNS |

**Rule of thumb:** TCP when accuracy matters, UDP when speed matters more than perfection.

## Related
- [Firewalls](firewalls.md)
- [Network Fundamentals](network-fundamentals.md)
