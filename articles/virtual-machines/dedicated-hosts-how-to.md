---
title: 部署 Azure 专用主机
description: 将 VM 和规模集部署到专用主机。
author: brittanyrowe
ms.author: brittanyrowe
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2021
ms.reviewer: cynthn, zivr
ms.openlocfilehash: c4d895551f852c26d9083c570de29d5cbdedfa53
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439830"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts"></a>将 VM 和规模集部署到专用主机

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 统一规模集

本文介绍如何创建用于托管虚拟机 (VM) 和规模集实例的 Azure [专用主机](dedicated-hosts.md)。


## <a name="limitations"></a>限制

- 专用主机可用的大小和硬件类型因区域而异。 请参阅主机[定价页](https://aka.ms/ADHPricing)来了解详细信息。

## <a name="create-a-host-group"></a>创建主机组

主机组是表示专用主机集合的资源。 可在区域和可用性区域中创建主机组，并向其添加主机。 规划高可用性时，有其他选项可供选择。 你可以将以下一个或两个选项与专用主机一起使用：
- 跨多个可用性区域。 在这种情况下，你需要在要使用的每个区域中都有一个主机组。
- 跨映射到物理机架的多个容错域。

在任一情况下，都需要为主机组提供容错域计数。 如果你不希望跨组中的容错域，请使用容错域计数 1。

还可以决定使用可用性区域和容错域。

### <a name="portal"></a>[门户](#tab/portal)

在此示例中，我们将创建使用 1 个可用性区域和 2 个容错域的主机组。

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 选择左上角的“创建资源”。
1. 搜索“主机组”，然后从结果中选择“主机组”。
1. 在“主机组”页中，选择“创建”。
1. 选择要使用的订阅，然后选择“新建”以创建新的资源组。
1. 键入“myDedicatedHostsRG”作为“名称”，然后选择“确定”。
1. 对于“主机组名称”，请键入“myHostGroup”。
1. 对于“位置”，请选择“美国东部”。
1. 对于“可用性区域”，请选择“1”。 
1. 选择 **2** 作为“容错域计数”。
1. 选择“自动放置”以自动为此组中的可用主机分配 VM 和规模集实例。
1. 选择“查看 + 创建”，然后等待验证。
1. 看到“验证通过”消息后，选择“创建”以创建主机组。 

应当只需很短时间便可创建主机组。


### <a name="cli"></a>[CLI](#tab/cli)

并非所有主机 SKU 都适用于所有区域和可用性区域。 在开始预配专用主机之前，可以列出主机可用性和任何套餐限制。

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table
```

在此示例中，我们将通过 [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) 使用可用性区域和容错域创建主机组。

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2
```

添加 `--automatic-placement true` 参数，将 VM 和规模集实例自动放置在主机组中的主机上。 如需了解详情，请参阅[手动放置与自动放置](dedicated-hosts.md#manual-vs-automatic-placement)。


**其他示例**

还可以使用 [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) 在可用性区域 1（无容错域）中创建主机组。

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1
```

下面通过 [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create)，只使用容错域创建主机组（在不支持可用性区域的区域中使用）。

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

此示例使用 [New-AzHostGroup](/powershell/module/az.compute/new-azhostgroup) 在区域 1 中创建具有 2 个容错域的主机组。


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


添加 `-SupportAutomaticPlacement true` 参数，将 VM 和规模集实例自动放置在主机组中的主机上。 如需了解详情，请参阅[手动放置与自动放置](dedicated-hosts.md#manual-vs-automatic-placement)。

---


## <a name="create-a-dedicated-host"></a>创建专用主机

现在，在主机组中创建一个专用主机。 除了主机名称外，还需要提供主机的 SKU。 主机 SKU 捕获受支持的 VM 系列以及专用主机的硬件代系。

有关主机 SKU 和定价的详细信息，请参阅 [Azure 专用主机定价](https://aka.ms/ADHPricing)。

如果为主机组设置了容错域计数，则需要为主机指定容错域。

### <a name="portal"></a>[门户](#tab/portal)

1. 选择左上角的“创建资源”。
1. 搜索“专用主机”，然后从结果中选择“专用主机”。
1. 在“专用主机”页中，选择“创建”。
1. 选择要使用的订阅。
1. 选择“myDedicatedHostsRG”作为“资源组”。
1. 在“实例详细信息”中，键入“myHost”作为“名称”，并选择“美国东部”作为位置。
1. 在“硬件配置文件”中，对于“大小系列”，请选择“标准 Es3 系列 - 类型 1”；对于“主机组”，请选择“myHostGroup”；对于“容错域”，请选择 1。 至于其余字段，请保留默认值。
1. 完成后，选择“查看 + 创建”，然后等待验证。
1. 看到“验证通过”消息后，选择“创建”以创建主机。 

### <a name="cli"></a>[CLI](#tab/cli)

使用 [az vm host create](/cli/azure/vm/host#az_vm_host_create) 可创建主机。 如果为主机组设置了容错域计数，则系统会要求你为主机指定容错域。

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

在此示例中，我们使用 [New-AzHost](/powershell/module/az.compute/new-azhost) 创建主机并将容错域设置为 1。


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

---

## <a name="create-a-vm"></a>创建 VM

现在，在主机上创建 VM。

### <a name="portal"></a>[门户](#tab/portal)

1. 在 Azure 门户的左上角，选择“创建资源”。
1. 在 Azure 市场资源列表上方的搜索框中，搜索并选择要使用的映像，然后选择“创建”。
1. 在“基本信息”选项卡中的“项目详细信息”下，确保选择了正确的订阅，然后选择 myDedicatedHostsRG 作为“资源组” 。
1. 在“实例详细信息”下，对于“虚拟机名称”键入 *myVM*，对于“位置”选择“美国东部”。
1. 在“可用性选项”中，选择“可用性区域”，然后从下拉列表中选择 1 。
1. 对于大小，选择“更改大小”。 在可用大小列表中，选择 Esv3 系列其中一个，例如“标准 E2s v3”。 可能需要清除筛选器才能查看所有可用大小。
1. 根据需要完成“基本信息”选项卡上的其余字段。
1. 如果要指定用于 VM 的主机，则在页面顶部，选择“高级”选项卡，然后在“主机”部分，对于“主机组”，选择“myHostGroup”，对于“主机”，选择“myHost” 。 否则，VM 将自动置于具有容量的主机上。
    ![选择主机组和主机](./media/dedicated-hosts-portal/advanced.png)
1. 保留剩余的默认值，然后选择页面底部的“查看 + 创建”按钮。
1. 显示验证通过的消息时，选择“创建”。

部署 VM 需要数分钟。


### <a name="cli"></a>[CLI](#tab/cli)

使用 [az vm create](/cli/azure/vm#az_vm_create) 在专用主机中创建虚拟机。 如果在创建主机组时指定了可用性区域，则需要在创建虚拟机时使用同一区域。 将映像和主机名等值替换为你自己的值。 如果要创建 Windows VM，请删除 `--generate-ssh-keys`，以使系统提示输入密码。

```azurecli-interactive
az vm create \
   -n myVM \
   --image myImage \
   --host-group myHostGroup \
   --admin-username azureuser \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

若要将 VM 放置在特定主机上，请使用 `--host`，而不是使用 `--host-group` 指定主机组。

> [!WARNING]
> 如果在没有足够资源的主机上创建虚拟机，则虚拟机将创建为“失败”状态。


### <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 在主机上创建新 VM。对于本示例，由于主机组位于区域 1，因此需要在区域 1 中创建 VM。


```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image myImage `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> 如果在没有足够资源的主机上创建虚拟机，则虚拟机将创建为“失败”状态。

---

## <a name="create-a-scale-set"></a>创建规模集

也可以在主机上创建规模集。

### <a name="portal"></a>[门户](#tab/portal)

部署规模集时，需要指定主机组。

1. 搜索“规模集”，然后从列表中选择“虚拟机规模集”。
1. 选择“添加”，创建新的规模集。
1. 如同往常一样完成“基本信息”选项卡上的字段，但请确保选择的 VM 大小来自为专用主机选择的系列，如“标准 E2s v3” 。
1. 在“高级”选项卡上，选择“最大传播”作为“传播算法”  。
1. 在“主机组”中，从下拉菜单中选择主机组。 如果最近创建了组，则该组可能需要一分钟才能添加到列表中。


### <a name="cli"></a>[CLI](#tab/cli)

使用 [az vmss create](/cli/azure/vmss#az_vmss_create) 部署规模集时，请使用 `--host-group` 指定主机组。 在此示例中，我们将部署最新的 Ubuntu LTS 映像。 若要部署 Windows 映像，请替换 `--image` 的值并删除 `--generate-ssh-keys`，以使系统提示输入密码。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

如果要手动选择要将规模集部署到哪个主机，请添加 `--host` 和主机名称。


### <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 [New-AzVMSS](/powershell/module/az.compute/new-azvmss) 将规模集部署到主机。 部署规模集时，需要指定主机组。

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

如果要手动选择要将规模集部署到哪个主机，请添加 `--host` 和主机名称。

---

## <a name="add-an-existing-vm"></a>添加现有 VM

可将现有 VM 添加到专用主机，但必须先停止/解除分配该 VM。 在将 VM 移动到专用主机之前，请确保 VM 配置受支持：

- VM 大小必须属于专用主机所用的同一大小系列。 例如，如果专用主机是 DSv3，则 VM 大小可以是 Standard_D4s_v3，但不能是 Standard_A4_v2。
- VM 需要位于专用主机所在的同一区域。
- VM 不能是邻近放置组的一部分。 在将 VM 移动到专用主机之前，请先从邻近放置组中删除该 VM。 有关详细信息，请参阅[将 VM 移出邻近放置组](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位于可用性集中。
- 如果 VM 位于可用性区域中，则该可用性区域必须与主机组相同。 VM 和主机组的可用性区域设置必须匹配。

### <a name="portal"></a>[门户](#tab/portal2)

使用[门户](https://portal.azure.com)将 VM 迁移到专用主机。

1. 打开 VM 所对应的页。
1. 选择“停止”以停止/解除分配 VM。
1. 在左侧菜单中选择“配置”。
1. 从下拉菜单中选择主机组和主机。
1. 完成操作后，在页面顶部选择“保存”。
1. 将 VM 添加到主机之后，从左侧菜单中选择“概述”。
1. 在页面顶部，选择“启动”以重启 VM。


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

将变量值替换为你自己的信息。

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName

$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName

$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force

Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug

Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```

---


## <a name="check-the-status-of-the-host"></a>检查主机的状态

如需了解主机上的可用容量，可以查看状态。

### <a name="portal"></a>[门户](#tab/portal)

1. 搜索并选择主机。
1. 在主机的“概述”页面中，向下滚动以查看主机仍可用的大小列表。 它看起来应该类似于：

:::image type="content" source="media/dedicated-hosts-portal/host-status.png" alt-text="从主机的“概述”页面查看主机的可用容量。":::

### <a name="cli"></a>[CLI](#tab/cli)

可以使用 [az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view) 查看主机运行状况以及仍可部署到主机的虚拟机数。

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```

输出类似于以下内容：

```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


可以使用带有 `-InstanceView` 参数的 [Get-AzHost](/powershell/module/az.compute/get-azhost) 查看主机运行状况以及仍可部署到主机的虚拟机数量。

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

输出类似于以下内容：

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           :
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    :
    AllocatableVMs[0]  :
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  :
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  :
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  :
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  :
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  :
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  :
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  :
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  :
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  :
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          :
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          :
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    :
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

---

## <a name="deleting-hosts"></a>删除主机 
即使没有部署虚拟机，也会对专用主机收费。 你应删除当前未使用的任何主机以节省成本。

只有当不再有虚拟机使用主机时，才能删除该主机。

### <a name="portal"></a>[门户](#tab/portal)

1. 搜索并选择主机。
1. 在左侧菜单中，选择“实例”。
1. 选择并删除每个虚拟机。
1. 删除所有 VM 后，返回主机的“概述”页面并从顶部菜单中选择“删除” 。
1. 删除主机后，打开主机组的页面并选择“删除主机组”。

### <a name="cli"></a>[CLI](#tab/cli)

 使用 [az vm delete](/cli/azure/vm#az_vm_delete) 删除 VM。

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

删除 VM 之后，可以使用 [az vm host delete](/cli/azure/vm/host#az_vm_host_delete) 删除主机。

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost
```

删除所有主机后，可以使用 [az vm host group delete](/cli/azure/vm/host/group#az_vm_host_group_delete) 删除主机组。

```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup
```

还可以在单个命令中删除整个资源组。 这会删除在组中创建的所有资源，包括所有 VM、主机和主机组。

```azurecli-interactive
az group delete -n myDHResourceGroup
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

使用 [Remove-AzVM](/powershell/module/az.compute/remove-azvm) 删除 VM。

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

删除 VM 后，可以使用 [Remove-AzHost](/powershell/module/az.compute/remove-azhost) 删除主机。

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

删除所有主机后，可以使用 [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup) 删除主机组。

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

还可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 通过单条命令删除整个资源组。 这会删除在组中创建的所有资源，包括所有 VM、主机和主机组。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```

---

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[专用主机](dedicated-hosts.md)概述。

- [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)提供了示例模板，该模板同时使用区域和容错域以实现区域中的最大复原能力。

