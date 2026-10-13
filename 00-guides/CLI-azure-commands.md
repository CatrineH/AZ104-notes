# Common Azure CLI commands - Azure Virtual Machines | Microsoft Learn

| Reference docs| |
| --- | --- |
| Get-| = Read/View existing resources | `Get-AzVM` `Get-AzDisk` |
| New- | = Create resources | `New-AzVM` `New-AzDisk` |
| Set- | = Modify existing resources | `Set-AzVM` `Set-AzDisk` |
| Remove- | = Delete resources | `Remove-AzVM` `Remove-AzDisk` |


### Create VMs

| Task | Azure CLI commands |
| --- | --- |
| Create a resource group | `az group create --name myResourceGroup --location eastus` |
| Create a Linux VM | `az vm create --resource-group myResourceGroup --name myVM --image LinuxImageName` |
| Create a Windows VM | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### Manage VM state

| Task | Azure CLI commands |
| --- | --- |
| Start a VM | `az vm start --resource-group myResourceGroup --name myVM` |
| Stop a VM | `az vm stop --resource-group myResourceGroup --name myVM` |
| Deallocate a VM | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Restart a VM | `az vm restart --resource-group myResourceGroup --name myVM` |
| Redeploy a VM | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Delete a VM | `az vm delete --resource-group myResourceGroup --name myVM` |

### Get VM info

| Task | Azure CLI commands |
| --- | --- |
| List VMs | `az vm list` |
| Get information about a VM | `az vm show --resource-group myResourceGroup --name myVM` |
| Get usage of VM resources | `az vm list-usage --location eastus` |
| Get all available VM sizes | `az vm list-sizes --location eastus` |

## Disks and images

| Task | Azure CLI commands |
| --- | --- |
| Add a data disk to a VM | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| List attached disks to a VM | `az vm show --resource-group groupName --name vmName --query "storageProfile"` |
| Remove a data disk from a VM | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Resize a disk | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Snapshot a disk | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Create image of a VM | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Create VM from image | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |
