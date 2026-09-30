# Lab: Application Gateway with Path-Based Routing

[← Back to README](../README.md)

## Goal
Path-based (layer 7) routing to two VMs, with an **internal-only endpoint reached through a point-to-site (P2S) VPN**, which is later made externally accessible.

## Prerequisites
- A **new vNet** 
- No existing VPN Gateway
- Working in the **Azure portal**
- Using **Bastion** for SSH

## Why this order?
Provisioning a **VPN Gateway takes 20–45 minutes**. Everything else takes seconds to minutes. So start the VPN Gateway as early as possible and do the rest of the lab while it deploys.

The **GatewaySubnet** is created together with the vNet. Adding it later is possible, but planning everything at once avoids address conflicts.

## Checklist
- [ ] 0. Plan the address space
- [ ] 1. Create the vNet with all subnets
- [ ] 2. Start the VPN Gateway deployment
- [ ] 3. Set up the web servers
- [ ] 4. Check NSG rules
- [ ] 5. Deploy Application Gateway
- [ ] 6. *(continued in next notes)*

---

## Step 0: Plan

**vNet:** `vnet-appgw-cat` – `10.10.0.0/16`

| Subnet | Purpose | Address range |
|---|---|---|
| `snet-appgw` | Application Gateway (dedicated) | 10.10.0.0/24 |
| `snet-vm` | Web server VMs | 10.10.1.0/24 |
| `GatewaySubnet` | VPN Gateway | 10.10.2.0/27 |

<!-- Update the ranges to match my own diagram -->

**Why:**
- Application Gateway requires a **dedicated subnet**
- `GatewaySubnet` is a **reserved name** that Azure looks for when creating a VNet gateway. The portal won't accept any other name

## Step 1: Create the vNet with all subnets
Portal: **Virtual networks → + Create**

1. Name: `vnet-appgw-cat`
2. Under **IP addresses**, add the address space `10.10.0.0/16`
3. Add all three subnets from the plan, **including GatewaySubnet**

**Why now:** you avoid editing the vNet twice, and you don't discover too late that the App Gateway subnet is too small.

## Step 2: Start the VPN Gateway deployment
Portal: **Virtual network gateways → + Create**

| Setting | Value |
|---|---|
| Name | `vgw-appgw-cat` |
| Gateway type | VPN |
| VPN type | **Route-based** (required for P2S) |
| SKU | **VpnGw1** – Basic doesn't support P2S with modern authentication |
| Virtual network | `vnet-appgw-cat` (GatewaySubnet is detected automatically) |
| Public IP | Create new: `pip-vgw-appgw-cat` |

Click **Review + create → Create**.

**Why now:** this is the only slow resource. Steps 3–5 can be done while it deploys.

## Step 3: Set up the web servers
Create two VMs in `snet-vm` and connect with Bastion.

Configure nginx on each VM to respond differently depending on the path:

```bash
sudo tee /etc/nginx/sites-available/default > /dev/null << 'EOF'
server {
    listen 80;

    location /health {
        default_type text/plain;
        return 200 "OK";
    }

    location /api {
        default_type text/plain;
        return 200 "Hit VM1 - api-path\n";
    }

    location /images {
        default_type text/plain;
        return 200 "Hit VM1 - images-path\n";
    }
}
EOF
```

On **VM2**, change the text to "Hit VM2" so you can see which VM answered.

Restart nginx:
```bash
sudo systemctl restart nginx
```

**Test locally first:**
```bash
curl localhost/api
curl localhost/health
```
If this works, any later errors come from the App Gateway configuration, not the web server.

**Why separate location blocks?** App Gateway's URL path map decides *which VM* gets the request. nginx decides *how the VM responds*.

## Step 4: Check NSG rules

**VM NSG** – must allow:
- Inbound **port 80** from `snet-appgw` (`10.10.0.0/24`), so App Gateway can reach the VMs and run health probes

**App Gateway subnet NSG** (only if you attach one) – must allow:
- Inbound TCP **65200–65535** from the **GatewayManager** service tag
- Inbound from **AzureLoadBalancer**
- Inbound on the listener port (for example 80) from the clients

**NIC vs subnet NSG:** in my setup, the NSG is on the **NIC**, not the subnet. That works. If both have an NSG, traffic must be allowed by **both**.

## Step 5: Deploy Application Gateway
Portal: **Application Gateway → + Create**

**Basics tab**

| Setting | Value |
|---|---|
| Name | `appgw-lab` |
| Tier | **Standard V2** (supports path-based routing and public + private frontend at the same time) |
| Virtual network | `vnet-appgw-cat` |
| Subnet | `snet-appgw` |

**Frontends tab**
- Select **both Public and Private** IP – this is the feature the Load Balancer lab lacked

**Backends tab**
- Create **two backend pools**: one for VM1 and one for VM2
- For path-based routing to different VMs, you need separate pools

**Configuration tab**

<!-- To be continued -->

---

## Troubleshooting tips
- **Backend unhealthy?** The default health probe checks `/`, but this nginx config has no `location /`, so it returns 404. Create a **custom health probe** with the path **`/health`**
- Check **Backend health** in the App Gateway menu to see why a backend is failing
- Test the VMs with `curl localhost/...` to rule out nginx problems

## Related
- [Application Gateway](../03-networking/application-gateway.md)
- [Subnets and Routing](../03-networking/subnets-and-routing.md)
- [Firewalls](../03-networking/firewalls.md)
