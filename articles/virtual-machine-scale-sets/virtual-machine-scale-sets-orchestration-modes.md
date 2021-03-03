---
title: Azure 中虚拟机规模集的业务流程模式
description: 了解如何在 Azure 中使用虚拟机规模集的灵活且一致的业务流程模式。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 1a276915f8132f2941bfb4425011c2d34454093b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693877"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>预览： Azure 中虚拟机规模集的业务流程模式 

虚拟机规模集提供平台托管的虚拟机的逻辑分组。 使用规模集可以创建虚拟机配置模型，根据 CPU 或内存负载自动添加或删除其他实例，并自动升级到最新的 OS 版本。 在传统上，规模集允许你使用在创建规模集时提供的 VM 配置模型来创建虚拟机，并且规模集只能管理基于配置模型隐式创建的虚拟机。 

规模集业务流程模式允许您更好地控制规模集如何管理虚拟机实例。 

> [!IMPORTANT]
> 业务流程模式是在创建规模集时定义的，以后无法更改或更新。


## <a name="scale-sets-with-uniform-orchestration"></a>具有统一业务流程的规模集
针对具有相同实例的大规模无状态工作负荷进行优化。

具有统一业务流程的虚拟机规模集使用虚拟机配置文件或模板来扩展到所需的容量。 尽管有一些功能可以管理或自定义单独的虚拟机实例，但统一使用完全相同的 VM 实例。 单个统一 VM 实例通过虚拟机规模集 VM API 命令公开。 单个实例与标准 Azure IaaS VM API 命令不兼容，azure 管理功能，例如 Azure 资源管理器资源标记 RBAC 权限、Azure 备份或 Azure Site Recovery。 当配置的实例少于100个实例时，统一业务流程可提供容错域的高可用性保证。 统一的业务流程已正式发布，并支持全套规模集管理和业务流程，包括基于指标的自动缩放、实例保护和自动 OS 升级。 


## <a name="scale-sets-with-flexible-orchestration"></a>具有灵活的业务流程的规模集 
通过相同或多个虚拟机类型实现大规模的高可用性。

通过灵活的业务流程，Azure 可在 Azure VM 生态系统中提供统一的体验。 灵活的业务流程通过在某个区域或可用性区域中的容错域之间分布 Vm，提供高可用性保证 (多达 1000 Vm) 。 这使你能够扩展应用程序，同时保持对运行基于仲裁或有状态工作负荷至关重要的容错域隔离，其中包括：
- 基于仲裁的工作负荷
- Open-Source 数据库
- 有状态应用程序
- 需要高可用性和大规模的服务
- 需要混合虚拟机类型，或将点式和点播 Vm 一起使用的服务
- 现有可用性集应用程序  

> [!IMPORTANT]
> 灵活业务流程模式下的虚拟机规模集目前为公共预览版。 使用下述公共预览功能需要使用选择过程。
> 此预览版本在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>灵活的业务流程模式发生了什么变化？
灵活的业务流程的一个主要优点是，它在标准 Azure IaaS Vm 上提供了业务流程功能，而不是规模集的子虚拟机。 这意味着，在管理灵活的业务流程实例时，可以使用所有标准 VM Api，而不是在统一业务流程中使用的虚拟机规模集 VM Api。 在预览期间，在灵活的业务流程与统一的业务流程中管理实例之间有几个不同之处。 通常，我们建议尽可能使用标准的 Azure IaaS VM Api。 在本部分中，我们将重点介绍如何通过灵活的业务流程管理 VM 实例的最佳实践。  

### <a name="assign-fault-domain-during-vm-creation"></a>在创建 VM 期间分配容错域
您可以为灵活的业务流程规模集选择容错域的数量。 默认情况下，当你将 VM 添加到可变规模集时，Azure 会跨容错域均匀地分布实例。 尽管建议你允许 Azure 分配容错域，但对于高级或故障排除方案，你可以重写此默认行为，并指定实例将位于的容错域。

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>实例命名 
创建 VM 并将其添加到灵活规模集时，可以完全控制 Azure 命名约定规则中的实例名称。 通过自动缩放将 Vm 自动添加到规模集时，提供一个前缀，Azure 会在名称末尾追加一个唯一的编号。

### <a name="query-instances-for-power-state"></a>电源状态的查询实例
首选方法是使用 Azure 资源关系图查询虚拟机规模集中的所有 Vm。 Azure 资源图提供了跨订阅大规模的 Azure 资源的高效查询功能。 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

使用 [Azure 资源关系图](../governance/resource-graph/overview) 查询资源是一种方便且高效的方法，可用于查询 azure 资源并最大程度地减少对资源提供程序的 API 调用。 Azure 资源关系图是一个最终一致的缓存，其中的新资源或更新的资源可能不会反映最多60秒。 可以执行以下操作：
- 列出资源组或订阅中的 Vm。
- 使用展开选项可以检索订阅中所有 Vm 的实例视图 (容错域分配、电源和预配状态) 。
- 使用获取 VM API 和命令获取单个实例的模型和实例视图。

### <a name="scale-sets-vm-batch-operations"></a>规模集 VM 批处理操作
使用标准 VM 命令来启动、停止、重启、删除实例，而不是使用虚拟机规模集 VM Api。 虚拟机规模集 VM 批处理操作 (全部启动、全部停止、全部重置映像等，) 不会用于灵活的业务流程模式。 

### <a name="monitor-application-health"></a>监视应用程序运行状况 
应用程序运行状况监视允许应用程序向 Azure 提供检测信号，以确定应用程序是否正常运行。 Azure 可以自动替换不正常的 VM 实例。 对于可变规模集实例，你必须在虚拟机上安装和配置应用程序运行状况扩展。 对于统一规模集实例，可以使用应用程序运行状况扩展，或使用 Azure 负载均衡器自定义运行状况探测来衡量运行状况。 

### <a name="list-scale-sets-vm-api-changes"></a>列出规模集 VM API 更改 
虚拟机规模集允许列出属于规模集的实例。 通过灵活的业务流程，列表虚拟机规模集 VM 命令提供了一个规模集 VM Id 列表。 然后，可以调用获取虚拟机规模集 VM 命令，以获取有关规模集如何使用 VM 实例的更多详细信息。 若要获取 VM 的完整详细信息，请使用标准的 GET VM 命令或 [Azure 资源关系图](https://docs.microsoft.com/azure/governance/resource-graph/overview)。 

### <a name="retrieve-boot-diagnostics-data"></a>检索启动诊断数据 
使用标准 VM Api 和命令检索实例启动诊断数据和屏幕截图。 虚拟机规模集 VM 启动诊断 Api 和命令不用于灵活的业务流程模式实例。

### <a name="vm-extensions"></a>VM 扩展 
使用针对标准虚拟机的扩展，而不是针对统一业务流程模式实例的扩展。


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>灵活、统一和可用性集的比较 
下表按功能比较灵活的业务流程模式、统一业务流程模式和可用性集。

| 功能 | 通过灵活的业务流程支持 (预览)  | 由统一业务流程支持 (公开上市)  | 受 AvSets 支持 (公开上市)  |
|-|-|-|-|
|         虚拟机类型  |  (/virtualmachines 的标准 Azure IaaS VM)   |  (/virtualmachinescalesets/virtualmachines 的规模集特定 Vm)   |  (/virtualmachines 的标准 Azure IaaS VM)   |
|         支持的 SKU  |            D 系列、E 系列、F 系列、A 系列、B 系列、Intel、AMD  |            所有 Sku  |            所有 Sku  |
|         可用性区域  |            根据需要，可以在单个可用性区域中指定所有实例 |            指定1、2或3个可用性区域上的实例  |            不支持  |
|         完全控制 VM、Nic、磁盘  |            是  |            受限控制虚拟机规模集 VM API  |            是  |
|         自动缩放  |            否  |            是  |            否  |
|         将 VM 分配到特定的容错域  |            是  |             否   |            否  |
|         删除 VM 实例时删除 Nic 和磁盘  |            否  |            是  |            否  |
|         升级策略 (VM 规模集)  |            否  |            自动、滚动、手动  |            不适用  |
|          (VM 规模集的自动 OS 更新)  |            否  |            是  |            不适用  |
|         来宾安全修补  |            是  |            否  |            是  |
|          (VM 规模集终止通知)  |            否  |            是  |            不适用  |
|         实例 (VM 规模集修复)  |            否  |            是   |            不适用  |
|         加速网络  |            是  |            是  |            是  |
|         发现实例和定价   |            是的，你可以同时拥有点和常规优先级实例  |            是的，实例必须全部为专色或全部正常  |            否，仅限常规优先级实例  |
|         混合操作系统  |            是的，Linux 和 Windows 可以位于同一可变规模集 |            否，实例是相同的操作系统  |               是的，Linux 和 Windows 可以位于同一可变规模集 |
|         监视应用程序运行状况  |            应用程序运行状况扩展  |            应用程序运行状况扩展或 Azure 负载均衡器探测  |            应用程序运行状况扩展  |
|         UltraSSD 磁盘   |            是  |            是，仅适用于区域部署  |            否  |
|         Infiniband   |            否  |            是，仅限单个放置组  |            是  |
|         写入加速器   |            否  |            是  |            是  |
|         邻近位置组   |            是  |            是  |            是  |
|         Azure 专用主机   |            否  |            是  |            是  |
|         基本 SLB   |            否  |            是  |            是  |
|         Azure 负载均衡器标准版 SKU |            是  |            是  |            是  |
|         应用程序网关  |            否  |            是  |            是  |
|         维护控制   |            否  |            是  |            是  |
|         列出集中的 Vm  |            是  |            是  |            是，在 AvSet 中列出 Vm  |
|         Azure 警报  |            否  |            是  |            是  |
|         VM Insights  |            否  |            是  |            是  |
|         Azure 备份  |            是  |            是  |            是  |
|         Azure Site Recovery  |            是，仅限 PowerShell  |            是  |            是  |
|         向组中添加/删除现有 VM  |            否  |            否  |            否  | 


## <a name="register-for-flexible-orchestration-mode"></a>注册以实现灵活的业务流程模式
在灵活的业务流程模式下部署虚拟机规模集之前，必须先注册预览功能的订阅。 注册可能需要几分钟才能完成。 你可以使用以下 Azure PowerShell 或 Azure CLI 命令注册。

### <a name="azure-powershell"></a>Azure PowerShell 
使用 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 为你的订阅启用预览。 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令： 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Azure CLI 2.0 
使用 [az feature register](/cli/azure/feature#az-feature-register) 为你的订阅启用预览。 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

功能注册可能最多需要15分钟。 若要检查注册状态，请使用以下命令： 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

为订阅注册此功能后，通过将更改传播到计算资源提供程序来完成选择加入过程。 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>灵活的业务流程模式入门

通过 Azure 门户、Azure CLI、Terraform 或 REST API，开始使用规模集灵活的业务流程模式。

### <a name="azure-portal"></a>Azure 门户

通过 Azure 门户在灵活的业务流程模式下创建虚拟机规模集。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在搜索栏中，搜索并选择 " **虚拟机规模集**"。 
1. 在 "**虚拟机规模集**" 页上选择 "**创建**"。
1. 在 " **创建虚拟机规模集** " 页上，查看 **业务流程** 部分。
1. 对于 **业务流程模式**，请选择 " **灵活** " 选项。
1. 设置 **容错域计数**。
1. 完成规模集的创建。 有关如何创建规模集的详细信息，请参阅 [在 Azure 门户中创建规模集](quick-create-portal.md#create-virtual-machine-scale-set) 。

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="创建规模集时门户中的业务流程模式":::

接下来，在灵活的业务流程模式下将虚拟机添加到规模集。

1. 在搜索栏中，搜索并选择 " **虚拟机**"。
1. 在 "**虚拟机**" 页上选择 "**添加**"。
1. 在 " **基本** 信息" 选项卡中，查看 " **实例详细信息** " 部分。
1. 通过在 **可用性选项** 中选择规模集，将 VM 添加到灵活业务流程模式下的规模集。 可以将虚拟机添加到相同区域、区域和资源组中的规模集。
1. 完成虚拟机的创建。 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="将 VM 添加到灵活的业务流程模式规模集":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 Azure CLI 创建灵活的虚拟机规模集。 下面的示例演示如何创建一个可变规模集，其中的容错域计数设置为3，创建一个虚拟机，然后将其添加到灵活规模集。 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
使用 Terraform 创建灵活的虚拟机规模集。 此过程需要 **Terraform Azurerm provider v 2.15.0** 或更高版本。 请注意以下参数：
- 如果未指定区域，则 `platform_fault_domain_count` 可以为1、2或3，具体取决于区域。
- 指定区域后， `the fault domain count` 可以为1。
- `single_placement_group` 参数必须 `false` 为灵活的虚拟机规模集。
- 如果要执行区域部署，则无需指定 `zones` 。

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>REST API

1. 创建空规模集。 下列参数必填：
    - API 版本 2019-12-01 (或更高版本)  
    - `false`创建灵活规模集时，单个放置组必须是

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. 将虚拟机添加到规模集。
    1. 将 `virtualMachineScaleSet` 属性分配给之前创建的规模集。 需要在 `virtualMachineScaleSet` 创建 VM 时指定属性。 
    1. 可以使用 **复制 ()** Azure 资源管理器模板功能同时创建多个 vm。 请参阅 Azure 资源管理器模板中的 [资源迭代](https://docs.microsoft.com/azure/azure-resource-manager/templates/copy-resources#iteration-for-a-child-resource) 。 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

有关完整示例，请参阅 [Azure 快速入门](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) 。


## <a name="frequently-asked-questions"></a>常见问题

**灵活的业务流程支持多少缩放？**

最多可以在灵活的业务流程模式下将 1000 Vm 添加到规模集。

**灵活的业务流程如何与可用性集或统一的业务流程进行比较？**

|   | 灵活的业务流程  | 统一业务流程  | 可用性集  |
|-|-|-|-|
| 跨可用性区域部署  | 否  | 是  | 否  |
| 区域内的容错域可用性保证  | 是的，最多可在该区域的最多3个容错域中分布1000个实例。 容错域的最大数量因区域而异  | 是，最多100实例  | 是，最多200实例  |
| 放置组  | 灵活模式始终使用多个放置组 (singlePlacementGroup = false)   | 可以选择单个放置组或多个放置组 | 不适用  |
| 更新域  | 无，维护或主机更新是由容错域完成的容错域  | 最多5个更新域  | 最多20个更新域  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>利用灵活的业务流程对规模集进行故障排除
找到适合您的故障排除方案的解决方案。 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**原因：** 没有为灵活的业务流程模式公共预览版注册订阅。 

**解决方案：** 按照上面的说明注册，以注册灵活的业务流程模式公共预览。 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**原因：**`platformFaultDomainCount`参数对于所选的区域或区域无效。 

**解决方案：** 您必须选择一个有效的 `platformFaultDomainCount` 值。 对于区域部署，最大 `platformFaultDomainCount` 值为1。 对于未指定区域的区域部署，最大值 `platformFaultDomainCount` 因区域而异。 请参阅 [管理用于脚本的 vm 的可用性](../virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) ，以确定每个区域的最大容错域计数。 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**原因：** 正在尝试删除与一个或多个虚拟机关联的灵活业务流程模式下的规模集。 

**解决方案：** 在灵活的业务流程模式下删除与规模集关联的所有虚拟机，然后可以删除规模集。

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**原因：** 订阅注册为灵活的业务流程模式预览;但是，此 `singlePlacementGroup` 参数设置为 *True*。 

**解决方案：**`singlePlacementGroup`必须设置为 *False*。 


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解如何在规模集上部署应用程序。](virtual-machine-scale-sets-deploy-app.md)
