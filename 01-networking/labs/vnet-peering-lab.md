# Lab: VNet Peering with PowerShell

[← Back to README](../README.md)

## Objectives
- Create two virtual networks (VNets) with subnets
- Peer the VNets
- Create a virtual machine (VM)
- Test network communication

## Architecture

```
VNet-Prod-cat (10.0.0.0/16)
└── Subnet-Servers-cat (10.0.1.0/24)
    └── VM-Test-cat
          ⇅  Peering
VNet-Dev-cat (10.1.0.0/16)
└── Subnet-Apps-cat (10.1.1.0/24)
```

## Checklist
- [ ] 1. Create the resource group
- [ ] 2. Create VNet-Prod-cat with its subnet
- [ ] 3. Create VNet-Dev-cat with its subnet
- [ ] 4. Create the peering (both directions)
- [ ] 5. Create the NSG
- [ ] 6. Create the public IP and network interface
- [ ] 7. Create the VM
- [ ] 8. Connect with Remote Desktop
- [ ] 9. Verify the peering
- [ ] 10. Clean up

---

## Step 1: Create the resource group

```powershell
$rgName   = "Lab01-vnet-peering-cat"
$location = "westeurope"

New-AzResourceGroup -Name $rgName -Location $location
```

**Takeaway:** a resource group is a logical container. Everything in this lab goes in one group, so it's easy to manage – and easy to delete afterwards.

```
Resource Group
├── VNet-Prod-cat
├── VNet-Dev-cat
├── VM-Test-cat
├── NIC
├── Public IP
└── NSG
```

## Step 2: Create VNet-Prod-cat with its subnet

In PowerShell, you first create the **subnet configuration**, then the VNet that uses it.

```powershell
$subnet1 = New-AzVirtualNetworkSubnetConfig `
  -Name "Subnet-Servers-cat" `
  -AddressPrefix "10.0.1.0/24"

$vnet1 = New-AzVirtualNetwork `
  -ResourceGroupName $rgName `
  -Location $location `
  -Name "VNet-Prod-cat" `
  -AddressPrefix "10.0.0.0/16" `
  -Subnet $subnet1
```

**Takeaway:** `New-AzVirtualNetworkSubnetConfig` only creates the configuration in memory. Nothing exists in Azure until `New-AzVirtualNetwork` runs.

## Step 3: Create VNet-Dev-cat with its subnet

```powershell
$subnet2 = New-AzVirtualNetworkSubnetConfig `
  -Name "Subnet-Apps-cat" `
  -AddressPrefix "10.1.1.0/24"

$vnet2 = New-AzVirtualNetwork `
  -ResourceGroupName $rgName `
  -Location $location `
  -Name "VNet-Dev-cat" `
  -AddressPrefix "10.1.0.0/16" `
  -Subnet $subnet2
```

**Takeaway:** there are now two separate networks that **cannot communicate** yet. Their address spaces (10.0.0.0/16 and 10.1.0.0/16) don't overlap, which is required for peering.

## Step 4: Create the peering (both directions)

```powershell
# Prod → Dev
Add-AzVirtualNetworkPeering `
  -Name "Prod-to-Dev-cat" `
  -VirtualNetwork $vnet1 `
  -RemoteVirtualNetworkId $vnet2.Id

# Dev → Prod
Add-AzVirtualNetworkPeering `
  -Name "Dev-to-Prod-cat" `
  -VirtualNetwork $vnet2 `
  -RemoteVirtualNetworkId $vnet1.Id
```

Check the peering:

```powershell
Get-AzVirtualNetworkPeering -VirtualNetworkName "VNet-Prod-cat" -ResourceGroupName $rgName |
  Select-Object Name, PeeringState
```

Expected: `PeeringState` = **Connected**. After only the first command, it shows **Initiated**.

**Takeaway:** peering needs a link in **each** direction. The portal creates both for you; PowerShell and CLI don't.

## Step 5: Create the NSG

```powershell
$nsg = New-AzNetworkSecurityGroup `
  -Name "nsg-lab01-vnet-peering-cat" `
  -ResourceGroupName $rgName `
  -Location $location

$myIp = (Invoke-RestMethod -Uri "https://api.ipify.org")

$nsg | Add-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-MyIP" `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix $myIp `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

$nsg | Set-AzNetworkSecurityGroup
```

**Takeaway:** an NSG works like a firewall that decides what traffic is allowed in and out.

```
Internet → NSG → VM
```

> *!!!* **Limit RDP to your own IP.** `-SourceAddressPrefix *` opens RDP to the whole internet, and VMs with open RDP are attacked within minutes.

> **ICMP (ping) between the VNets** is already allowed by the default NSG rule `AllowVnetInBound`, which includes peered VNets. No extra NSG rule is needed for that.

## Step 6: Create the public IP and network interface

```powershell
$pip = New-AzPublicIpAddress `
  -Name "pip-vm-test-cat" `
  -ResourceGroupName $rgName `
  -Location $location `
  -AllocationMethod Static `
  -Sku Standard

# Get the latest version of the VNet (to read the subnet ID)
$vnet1 = Get-AzVirtualNetwork -Name "VNet-Prod-cat" -ResourceGroupName $rgName

$nic = New-AzNetworkInterface `
  -Name "nic-vm-test-cat" `
  -ResourceGroupName $rgName `
  -Location $location `
  -SubnetId $vnet1.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

**Takeaway:** the NIC connects the VM to the subnet. Here, the NSG is attached to the **NIC**, not the subnet.

## Step 7: Create the VM

```powershell
$cred = Get-Credential   # username and password for the VM

$vmConfig = New-AzVMConfig -VMName "VM-Test-cat" -VMSize "Standard_B2s"

$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
  -Windows -ComputerName "VM-Test-cat" -Credential $cred

$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2022-datacenter-azure-edition `
  -Version latest

$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```

**Takeaway:** an Azure VM is built from several separate resources that Azure puts together:

```
VM
├── OS disk
├── NIC
├── Public IP
├── NSG
└── Operating system
```

> **Note:** a Windows computer name can be at most **15 characters**. `VM-Test-cat` is 11, so it works.

## Step 8: Connect with Remote Desktop

```powershell
(Get-AzPublicIpAddress -Name "pip-vm-test-cat" -ResourceGroupName $rgName).IpAddress
mstsc
```

Connect to the public IP with the credentials from step 7.

## Step 9: Verify the peering

This needs a **second VM in VNet-Dev-cat**. Its first private IP will be **10.1.1.4**, because Azure reserves .0–.3 in every subnet.

**Allow ping in Windows Firewall** (on both VMs – Windows blocks it by default):

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -IcmpType 8 -Direction Inbound -Action Allow
```

Test from VM-Test-cat:

```powershell
ping 10.1.1.4
Test-NetConnection 10.1.1.4 -Port 3389
```

`Test-NetConnection -Port` tests a TCP port, so it works even if ping is blocked.

## Step 10: Clean up

```powershell
Remove-AzResourceGroup -Name $rgName -Force
```

---

## Key takeaways

| Concept | Remember |
|---|---|
| **VNet** | A private network in Azure – like an office network |
| **Subnet** | A segment of the VNet (a department), used for segmentation and security |
| **Peering** | A private highway between VNets over the Azure backbone |
| **Two directions** | Peering must be created from both sides |
| **Not transitive** | A ↔ B and B ↔ C does **not** mean A ↔ C |
| **No overlap** | Peered VNets can't have overlapping address spaces |

## Related
- [Virtual Networks and VNet Peering](../01-networking/vnet-peering.md)
- [IP Addressing and Subnetting](../01-networking/ip-addressing-subnetting.md)
- [Firewalls](../01-networking/firewalls.md)
