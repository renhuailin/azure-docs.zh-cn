---
title: 使用 PowerShell 部署 Azure 现成虚拟机
description: 了解如何使用 Azure PowerShell 部署 Azure 现成虚拟机以节省成本。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee1f915045ed3272d8f93559efcf06c0c2bcc011
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689313"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>使用 Azure PowerShell 部署 Azure 现成虚拟机

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 

使用 [Azure 现成虚拟机](../spot-vms.md)，可以利用未使用的容量，大幅降低成本。 每当 Azure 需要回收容量时，Azure 基础结构就会逐出 Azure 现成虚拟机。 因此，Azure 现成虚拟机非常适用于那些处理服务中断的工作负荷（例如批处理作业）、开发/测试环境、大型计算工作负荷等。

Azure 现成虚拟机的定价因地区和 SKU 而异。 有关详细信息，请参阅针对 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 的 VM 定价。 若要详细了解如何设置最高价格，请参阅 [Azure 现成虚拟机 - 定价](../spot-vms.md#pricing)。

可以选择对 VM 设置你愿意支付的每小时最高价格。 Azure 现成虚拟机的最高价格可以美元 (USD) 形式设置，最多保留 5 位小数。 例如，值 `0.98765` 表示最高价格为 0.98765 美元/小时。 如果将最高价格设置为 `-1`，则不会根据价格逐出 VM。 VM 的价格将是 Spot 的当前价格或是标准 VM 的价格（两者中的较低者，前提是有可用的容量和配额）。


## <a name="create-the-vm"></a>创建 VM

通过使用 [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) 来创建配置以创建现成虚拟机。 包括 `-Priority Spot` 并将 `-MaxPrice` 设置为以下项之一：
- `-1`，使 VM 不会因价格而被逐出。
- 一个美元金额，最多 5 位数。 例如，`-MaxPrice .98765` 表示一旦现成虚拟机的价格达到大约每小时 $.98765 就会解除分配 VM。


此示例创建一个现成虚拟机，该虚拟机不会根据定价解除分配（只有当 Azure 需要回收容量时，才会将其解除分配）。 逐出策略设置为解除分配 VM，以便以后可以重启该 VM。 如果要在 VM 被逐出时删除该 VM 和基础磁盘，请在 `New-AzVMConfig` 中将 `-EvictionPolicy` 设置为 `Delete`。


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

创建 VM 后，可以通过查询来查看资源组中所有 VM 的最高价格。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>模拟逐出

可以使用 REST、PowerShell 或 CLI 模拟 Azure 现成虚拟机逐出，以测试应用程序对突然逐出的响应。

大多数情况下，需要借助 REST API [虚拟机 - 模拟逐出](/rest/api/compute/virtualmachines/simulateeviction)来自动测试应用程序。 对于 REST，`Response Code: 204` 意味着模拟逐出成功。 可以将模拟逐出与[计划事件服务](scheduled-events.md)相结合，自动测试应用在 VM 被逐出时的响应。

若要了解操作中的计划事件，请观看 [Azure Friday - 使用 Azure Scheduled Events 准备 VM 维护](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)。


### <a name="quick-test"></a>快速测试

若要快速测试以了解模拟逐出的运作情况，我们可通过查询计划事件服务来查看使用 PowerShell 模拟逐出时的具体情况。

首次为事件发出请求时，为你的服务启用了计划事件服务。 

远程登录到 VM，然后打开命令提示符。 

在 VM 的命令提示符处键入：

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

第一次响应可能需要长达 2 分钟的时间。 自此之后，几乎会立即显示输出。

在已安装 Az PowerShell 模块的计算机（例如本地计算机）中，使用 [Set-AzVM](/powershell/module/az.compute/set-azvm) 模拟逐出。 将资源组名称和 VM 名称替换成你自己的名称。 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

如果请求成功完成，则响应输出将为“`Status: Succeeded`”。

快速返回到现成虚拟机的远程连接，再次查询 Scheduled Events 终结点。 重复以下命令，直到获得包含详细信息的输出：

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

当计划事件服务获得逐出通知后，响应将如下所示：

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

可以看到 `"EventType":"Preempt"`，资源为 VM 资源 `"Resources":["myspotvm"]`。 

你还可以通过检查 `"NotBefore"` 值来查看 VM 何时会被逐出。 VM 在 `NotBefore` 中给定的时间之前不会被逐出，因此，在此时间窗口内你的应用程序可以正常关闭。


## <a name="next-steps"></a>后续步骤

此外，还可以使用 [Azure CLI](../linux/spot-cli.md)、[门户](../spot-portal.md)或[模板](../linux/spot-template.md)创建 Azure 现成虚拟机。

使用 [Azure 零售价格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) 查询当前的定价信息，了解 Azure 现成虚拟机定价。 `meterName` 和 `skuName` 都会包含 `Spot`。

如果遇到错误，请参阅[错误代码](../error-codes-spot.md)。
