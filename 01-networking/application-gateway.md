# Application Gateway

[← Back to README](../README.md)

## What is it?
**Azure Application Gateway** is a **layer 7 load balancer** for web traffic (HTTP/HTTPS). Unlike Azure Load Balancer (layer 4), it can read the request and route based on the **URL path** or **host name**.

## Key facts
- Needs its **own dedicated subnet** – no other resources can be in it, not even an Application Gateway with a different SKU
- Microsoft recommends a **/24** subnet for v2, so it has room to scale
- Use the **v2 SKU** (Standard_v2 or WAF_v2). v1 is being retired
- Can have a **public frontend IP, a private frontend IP, or both**
- Can include a **Web Application Firewall (WAF)** with the WAF_v2 SKU

## Load Balancer vs Application Gateway

| | Azure Load Balancer | Application Gateway |
|---|---|---|
| OSI layer | Layer 4 (TCP/UDP) | Layer 7 (HTTP/HTTPS) |
| Routes on | IP and port | URL path, host name |
| Public + private frontend at the same time | No  | Yes (v2) |
| WAF | No | Yes (WAF_v2) |
| Dedicated subnet |  | Yes |

## Main components

| Component | What it does |
|---|---|
| **Frontend IP** | The address clients connect to (public, private, or both) |
| **Listener** | Listens on a port and protocol, for example HTTP 80 |
| **Routing rule** | Connects a listener to backends |
| **URL path map** | Sends paths to different backends, for example `/api/*` → pool 1, `/images/*` → pool 2 |
| **Backend pool** | The servers that receive traffic (VMs, scale sets, IP addresses, App Services) |
| **Backend settings** | Port and protocol used to reach the backend |
| **Health probe** | Checks if backends are healthy. Only healthy backends get traffic |

## Path-based routing
- **App Gateway** decides *which backend* receives the request
- **The web server** on the backend (for example nginx) decides *how it responds*

## Health probes
- The default probe checks **`/`** on the backend and expects a status code **200–399**
- If the backend doesn't answer on `/`, create a **custom probe**, for example with the path `/health`

## NSG requirements

| Where | Rule |
|---|---|
| **App Gateway subnet** (if it has an NSG) | Allow inbound TCP **65200–65535** from the **GatewayManager** service tag (required for v2), and allow traffic from **AzureLoadBalancer** |
| **Backend VMs** | Allow inbound on the backend port (for example 80) from the App Gateway subnet |

## Related
- [Application Gateway lab](../labs/application-gateway-lab.md)
- [Firewalls](firewalls.md) – WAF
- [Subnets and Routing](subnets-and-routing.md)
