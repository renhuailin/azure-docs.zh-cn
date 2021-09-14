---
title: 从专用化映像版本创建 VM
description: 使用共享映像库中的专用化映像版本创建 VM。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 009678278d7900908572e81b7aab4222dc3c4a07
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452280"
---
# <a name="create-a-vm-using-a-specialized-image-version"></a>使用专用化映像版本创建 VM 

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM 

从共享映像库中存储的[专用化映像版本](./shared-image-galleries.md#generalized-and-specialized-images)创建 VM。 若要使用通用化映像版本创建 VM，请参阅[从通用化映像版本创建 VM](vm-generalized-image-version.md)。

在这些示例中，请根据需要替换资源名称。 

### <a name="portal"></a>[门户](#tab/portal)

现在，可以创建一个或多个新的 VM。 本示例在美国东部数据中心的“myResourceGroup”中创建名为“myVM”的 VM  。

1. 转到映像定义。 可以使用资源筛选器显示所有可用的映像定义。
1. 在映像定义的页面顶部，从菜单中选择“创建 VM”。
1. 对于“资源组”，请选择“新建”并键入 *myResourceGroup* 作为名称。 
1. 在“虚拟机名称”中键入 *myVM*。
1. 对于“区域”，请选择“美国东部”。
1. 对于“可用性选项”，请保留默认设置“无需基础结构冗余”。
1. 如果你是从映像定义的页面开始操作的，系统会自动使用 `latest` 映像版本填充“映像”的值。
1. 对于“大小”，请从可用大小列表中选择一种 VM 大小，然后选择“选择”。 
1. 由于使用的是源 VM 的用户名和凭据，因此“管理员帐户”下的用户名会灰显。
1. 如果要允许远程访问 VM，请在“公共入站端口”下选择“允许所选端口”，然后从下拉列表中选择“SSH (22)”或“RDP (3389)”   。 如果你不希望允许远程访问 VM，请为“公共入站端口”保留选择“无”。 
1. 完成后，选择页面底部的“查看 + 创建”按钮。
1. VM 通过验证后，选择页面底部的“创建”以开始部署。


### <a name="cli"></a>[CLI](#tab/cli)

使用 [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) 列出库中的映像定义，以查看定义的名称和 ID。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

结合 --specialized 参数使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM 可以指明该映像是专用化映像。 

使用 `--image` 的映像定义 ID 从可用的最新映像版本创建 VM。 还可以通过为 `--image` 提供映像版本 ID 从特定版本创建 VM。 

在此示例中，我们将从 myImageDefinition 映像的最新版本创建 VM。

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

获得专用化映像版本后，可以使用 [New-AzVM](/powershell/module/az.compute/new-azvm) cmdlet 创建一个或多个新 VM。 

在此示例中，我们使用映像定义 ID 来确保新 VM 会使用最新版本的映像。 也可通过将映像版本 ID 用作 `Set-AzVMSourceImage -Id` 来使用特定版本。 例如，若要使用映像版本 *1.0.0*，请键入：`Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`。 

请注意，使用特定映像版本意味着：如果该特定映像版本由于已删除或已从区域中删除而无法使用，则自动化可能会失败。 建议使用映像定义 ID 来创建新的 VM（除非需要特定的映像版本）。

在此示例中，请根据需要替换资源名称。 


```azurepowershell-interactive

# Create some variables for the new VM.

$resourceGroup = "mySIGSpecializedRG"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition


# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.

$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig

```

---

**后续步骤**

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
