---
title: Azure 中虚拟机规模集的灵活业务流程
description: 了解如何对 Azure 中的虚拟机规模集使用灵活业务流程模式。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/11/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: dc687c2f3d14c2da02fa3ce5b3a3357292977771
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648955"
---
# <a name="preview-flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>预览：Azure 中虚拟机规模集的灵活业务流程

适用于：:heavy_check_mark: 灵活规模集

带有灵活业务流程的虚拟机规模集让你可以将[虚拟机规模集](../virtual-machine-scale-sets/overview.md)的可伸缩性与[可用性集](availability-set-overview.md)的区域可用性保证相结合。

使用 Azure 虚拟机规模集可以创建并管理一组负载均衡的 VM。 可以根据需求或定义的计划自动增减 VM 实例的数目。 规模集具有下述主要优势：
- 易于创建和管理多个 VM
- 将 VM 分散到多个容错域，从而提供高可用性和应用程序复原能力
- 允许应用程序随资源需求变化自动进行缩放
- 大规模工作

借助灵活业务流程，Azure 可在整个 Azure VM 生态系统中提供统一的体验。 灵活业务流程将 VM 分散到某个地理区域或可用性区域的多个容错域中，从而提供高可用性保证（最多支持 1000 个 VM）。 这样，你便可以横向扩展应用程序，同时保持对容错域的隔离，这对于运行基于仲裁的工作负载或有状态工作负载而言至关重要，包括：
- 基于仲裁的工作负载
- 开源数据库
- 有状态应用程序
- 需要高可用性和较大规模的服务
- 需要混合虚拟机类型或同时利用现成 VM 和按需 VM 的服务
- 现有的可用性集应用程序

在[业务流程模式](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md)中详细了解统一规模集与灵活规模集之间的差异。


> [!IMPORTANT]
> 采用灵活业务流程模式的虚拟机规模集目前以公共预览版提供。 需要执行一个选用过程才能使用下述公共预览版功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


> [!CAUTION]
> 业务流程模式是在创建规模集时定义的，以后无法更改或更新。


## <a name="register-for-flexible-orchestration-mode"></a>注册灵活业务流程模式
在以灵活业务流程模式部署虚拟机规模集之前，必须先注册订阅以获取预览版功能。 注册可能需要几分钟才能完成。 可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 进行注册。

### <a name="azure-portal"></a>Azure 门户

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 转到你的订阅。
1. 选择订阅的名称，导航到你要在灵活业务流程模式下为其创建规模集的订阅的详细信息页。
1. 在菜单中的“设置”下，选择“预览功能” 。
1. 选择要启用的四个业务流程协调程序功能：VMOrchestratorSingleFD、VMOrchestratorMultiFD、VMScaleSetFlexPreview 和 SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview   。
1. 选择“注册”  。

为订阅注册功能后，通过将更改传播到计算资源提供程序来完成选用过程。 

1. 在菜单中的“设置”下，选择“资源提供程序” 。
1. 选择 `Microsoft.compute`。
1. 选择“重新注册”。


### <a name="azure-powershell"></a>Azure PowerShell
使用 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 为订阅启用预览版。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMScaleSetFlexPreview -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview -ProviderNamespace Microsoft.Compute
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az feature register](/cli/azure/feature#az_feature_register) 为订阅启用预览版。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

功能注册最多可能需要 15 分钟。 若要检查注册状态，请使用以下命令：

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```

为订阅注册该功能后，通过将更改传播到计算资源提供程序来完成选用过程。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="get-started-with-flexible-orchestration-mode"></a>开始使用灵活业务流程模式

通过 [Azure 门户](flexible-virtual-machine-scale-sets-portal.md)、[Azure CLI](flexible-virtual-machine-scale-sets-cli.md)、[Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md) 或 [ARM 模板](flexible-virtual-machine-scale-sets-rest-api.md)开始对规模集使用灵活业务流程模式。 


## <a name="add-instances-with-autoscaling-or-manually"></a>通过自动缩放或以手动方式添加实例
带有灵活业务流程的虚拟机规模集充当一个精简业务流程层来管理多个 VM。 可通过多种方式添加由规模集管理的 VM：

- 设置实例计数

    创建使用灵活业务流程的规模集时，定义一个 VM 配置文件或模板来描述用于横向扩展的模板。然后，可以设置容量参数以增加或减少规模集管理的 VM 实例数。 

- 使用指标或计划进行自动缩放 

    或者，可以设置自动缩放规则以根据指标或计划增加或减少容量。 请参阅[可自动缩放的虚拟机规模集](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md)。 

- 创建 VM 时指定规模集

    创建 VM 时，可以选择性地指定要将该 VM 添加到虚拟机规模集。 只能在创建 VM 时将其添加到规模集。

灵活的业务流程模式可与支持[内存保留更新或实时迁移](../virtual-machines/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)的 VM SKU 一起使用，包括 Azure 中部署的 90% 的 IaaS VM。 从广义上说，这包括常规用途的型号系列，例如 B、D、E 和 F 系列 VM。 目前，灵活模式无法协调 VM SKU 或不支持内存保留更新的系列，包括 G、H、L、M、N 系列 VM。 你可以使用[计算资源 SKU API](/rest/api/compute/resource-skus/list) 来确定是否支持特定的 VM SKU。

```azurecli-interactive
az vm list-skus -l eastus --size standard_d2s_v3 --query "[].capabilities[].[name, value]" -o table
```

## <a name="explicit-network-outbound-connectivity-required"></a>需要显式网络出站连接 

为了增强默认网络安全，带有灵活业务流程的虚拟机规模集要求通过以下方法之一，为通过自动缩放配置文件隐式创建的实例显式定义出站连接： 

- 对于大多数方案，我们建议使用[已附加到子网的 NAT 网关](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md)。
- 对于安全要求较高的方案，或者在使用 Azure 防火墙或网络虚拟设备 (NVA) 时，你可以将自定义的用户定义路由指定为防火墙中的下一个跃点。 
- 实例位于标准 SKU 实例 Azure 负载均衡器的后端池。 
- 将公共 IP 地址附加到实例网络接口。 

使用具有统一业务流程的单实例 VM 和虚拟机规模集时，系统会自动提供出站连接。 

需要显式出站连接的常见方案包括： 

- Windows VM 激活要求你已定义从 VM 实例到 Windows 激活密钥管理服务 (KMS) 的出站连接。 有关详细信息，请参阅[排查 Windows VM 激活问题](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)。  
- 访问存储帐户或 Key Vault。 你还可以通过[专用链接](../private-link/private-link-overview.md)与 Azure 服务建立连接。 

有关定义安全出站连接的更多详细信息，请参阅 [Azure 中的默认出站访问](https://aka.ms/defaultoutboundaccess)。


## <a name="assign-fault-domain-during-vm-creation"></a>创建 VM 期间分配容错域
可为灵活业务流程规模集选择容错域的数目。 默认情况下，将 VM 添加到灵活规模集时，Azure 会在多个容错域之间均匀分散实例。 尽管我们建议让 Azure 分配容错域，但对于高级或故障排除方案，你可以替代此默认行为，指定要将实例放入哪个容错域。

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

## <a name="instance-naming"></a>实例命名
创建 VM 并将其添加到灵活规模集时，可以在 Azure 命名约定规则中全面控制实例名称。 通过自动缩放将 VM 自动添加到规模集时，你可以提供一个前缀，而 Azure 会在名称的末尾追加唯一的编号。 

## <a name="list-scale-sets-vm-api-changes"></a>列出规模集 VM API 更改
虚拟机规模集允许列出属于该规模集的实例。 对于灵活业务流程，“列出虚拟机规模集 VM”命令将提供规模集 VM ID 的列表。 然后，可以调用“获取虚拟机规模集 VM”命令，以获取有关规模集如何使用 VM 实例的更多详细信息。 若要获取 VM 的完整详细信息，请使用标准的“获取 VM”命令或 [Azure Resource Graph](../governance/resource-graph/overview.md)。


## <a name="query-instances-for-power-state"></a>查询实例以获取电源状态
首选方法是使用 Azure Resource Graph 查询虚拟机规模集中的所有 VM。 Azure Resource Graph 针对各个订阅中的 Azure 资源提供大规模有效查询功能。

```
| where type =~ 'Microsoft.Compute/virtualMachines'
| where properties.virtualMachineScaleSet contains "demo"
| extend powerState = properties.extended.instanceView.powerState.code
| project name, resourceGroup, location, powerState
| order by resourceGroup desc, name desc
```

使用 [Azure Resource Graph](../governance/resource-graph/overview.md) 查询资源是查询 Azure 资源的方便高效方式，并可以最大程度地减少对资源提供程序的 API 调用。 Azure Resource Graph 是最终一致性缓存，其中的新资源或已更新的资源可能在最长 60 秒内不会反映出来。 方法：
- 列出资源组或订阅中的 VM。
- 使用展开选项检索订阅中所有 VM 的实例视图（容错域分配、电源和预配状态）。
- 使用“获取 VM”API 和命令获取单个实例的模型与实例视图。


## <a name="scale-sets-vm-batch-operations"></a>规模集 VM 批处理操作
使用标准 VM 命令来启动、停止、重启、删除实例，而无需使用虚拟机规模集 VM API。 虚拟机规模集 VM 批处理操作（全部启动、全部停止、全部重置映像等）不适合与灵活业务流程模式配合使用。


## <a name="monitor-application-health"></a>监视应用程序运行状况
应用程序运行状况监视可让应用程序为 Azure 提供检测信号，以确定应用程序是否正常。 Azure 可以自动替换不正常的 VM 实例。 对于灵活规模集实例，必须在虚拟机上安装并配置应用程序运行状况扩展。 对于统一规模集实例，可以使用应用程序运行状况扩展，或者使用 Azure 负载均衡器自定义运行状况探测来度量运行状况。


## <a name="retrieve-boot-diagnostics-data"></a>检索启动诊断数据
使用标准 VM API 和命令检索实例启动诊断数据和屏幕截图。 虚拟机规模集 VM 启动诊断 API 和命令不适用于灵活业务流程模式实例。


## <a name="vm-extensions"></a>VM 扩展
使用面向标准虚拟机的扩展，而不是面向统一业务流程模式实例的扩展。


## <a name="features"></a>功能
下表列出了灵活业务流程模式功能以及相应文档的链接。

| 功能 | 受灵活业务流程（预览版）的支持 |
|-|-|
| 虚拟机类型 | 标准 Azure IaaS VM (Microsoft.compute /virtualmachines) |
| 最大实例计数（提供 FD 可用性保证） | 1000 |
| 支持的 SKU | D 系列、E 系列、F 系列、A 系列、B 系列、Intel、AMD |
| 可用性区域 | （可选）指定将所有实例放入单个可用性区域 |
| 容错域 - 最大分散（Azure 将最大程度地分散实例） | 是 |
| 容错域 - 固定分散 | 2-3 个 FD（取决于 Azure 区域的最大 FD 计数），为局部区域部署提供 1 个 FD |
| 更新域 | 已弃用，按 FD 依次执行平台维护 |
| 可用性 SLA | 无（在预览期） |
| 全面控制 VM、NIC、磁盘 | 是 |
| 将 VM 分配到特定的容错域 | 是 |
| 加速网络 | 否（在预览期） |
| 来宾安全修补 | 是 |
| “零星”实例和定价  | 是，可以配置“现成”和“常规”优先级实例 |
| 混合操作系统 | 是，Linux 和 Windows 可以驻留在同一个灵活规模集中 |
| 监视应用程序运行状况 | 应用程序运行状况扩展 |
| UltraSSD 磁盘  | 是 |
| 邻近放置组  | 是，请参阅[邻近放置组文档](../virtual-machine-scale-sets/proximity-placement-groups.md) |
| Azure 负载均衡器标准版 SKU | 是 |
| 列出集中的 VM | 是 |
| Azure 备份 | 是 |
| 终止通知（VM 规模集） | 是，请参阅[终止通知文档](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md) |
| 实例修复（VM 规模集） | 是，请参阅[实例修复文档](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md) |
| 自动缩放 | 是 |
| 删除 VM 实例时移除 NIC 和磁盘 | 是 |
| 升级策略（VM 规模集） | 否 |
| 基于映像的自动 OS 更新 | 否 |
| Infiniband  | 否 |
| 写入加速器  | 否 |
| Azure 专用主机  | 否 |
| 基本 SLB  | 否 |
| 应用程序网关 | 是 |
| 维护控制  | 否 |
| Azure 警报 | 否 |
| VM Insights | 否 |
| Azure Site Recovery |  是，通过 PowerShell |
| Service Fabric | 否 |
| Azure Kubernetes 服务 (AKS) | 否 |
| 受信任的虚拟机（预览版） | 是 |
| 自动扩展更新 | 是 |


### <a name="unsupported-parameters"></a>不支持的参数

虚拟机规模集灵活业务流程公共预览版目前不支持以下虚拟机规模集参数：
- 单一放置组 - 必须选择 `singlePlacementGroup=False`。
- 多局部区域部署 - 可以在 Azure 区域中部署，也可以将所有 VM 部署到单个局部区域
- 使用专用 SKU 进行部署：G、H、L、M、N 系列 VM
- VMSS 超量预配
- 基于映像的自动 OS 升级
- 通过 SLB 运行状况探测监视应用程序运行状况 - 在实例上使用应用程序运行状况扩展
- 虚拟机规模集升级策略 - 必须为 null 或为空
- 部署到 Azure 专用主机
- 非托管磁盘
- 虚拟机规模集横向缩减策略
- 虚拟机规模集实例保护
- 加速网络
- 基本负载均衡器
- 通过标准负载均衡器 NAT 池进行端口转发 - 可对特定实例配置 NAT 规则


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>排查使用灵活业务流程的规模集的问题
找到适合你的故障排除方案的解决方案。

<!-- error -->
### <a name="invalidparameter-parameter-virtualmachineprofile-is-not-allowed"></a>InvalidParameter。 不允许使用参数“virtualMachineProfile”。

```
InvalidParameter. Parameter 'virtualMachineProfile' is not allowed.
```

**原因：** 未为订阅注册灵活业务流程模式公共预览版。

解决方法：必须为订阅注册 VMScaleSetPublicPreview 功能。 按照前面的说明注册灵活业务流程模式公共预览版。


<!-- error -->
### <a name="badrequest-creating-a-virtual-machine-with-a-public-ip-address-via-networkinterfaceconfigurations"></a>BadRequest。 通过 NetworkInterfaceConfigurations 创建带有公共 IP 地址的虚拟机

```
BadRequest. Creating a Virtual Machine with a Public IP Address via NetworkInterfaceConfigurations during the Public Preview of VM NetworkInterfaceConfigurations initially requires the feature 'Microsoft.Compute/SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview'.
```

**原因：** 未为订阅注册灵活业务流程模式公共预览版。

解决方法：必须为订阅注册 `SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview` 功能。 按照前面的说明注册灵活业务流程模式公共预览版。


<!-- error -->
### <a name="invalidparameter-the-value-false-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-true"></a>InvalidParameter。 不允许为参数“singlePlacementGroup”指定“False”值。 允许的值为：True

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**原因：** 未为订阅注册灵活业务流程模式公共预览版。

**解决方法：** 按照前面的说明注册灵活业务流程模式公共预览版。


<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-2-must-fall-in-the-range-1-to-1"></a>InvalidParameter。 指定的容错域计数为 2，但该计数必须在 1 到 1 的范围内。

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**原因：** `platformFaultDomainCount` 参数对于所选的地理区域或局部区域无效。

**解决方法：** 必须选择有效的 `platformFaultDomainCount` 值。 对于局部区域部署，`platformFaultDomainCount` 最大值为 1。 对于未指定局部区域的地理区域部署，`platformFaultDomainCount` 最大值因地理区域而异。 请参阅[管理用于脚本的 VM 的可用性](../virtual-machines/availability.md)，以确定每个区域的最大容错域数目。


<!-- error -->
### <a name="operationnotallowed-deletion-of-virtual-machine-scale-set-is-not-allowed-as-it-contains-one-or-more-vms-please-delete-or-detach-the-vms-before-deleting-the-virtual-machine-scale-set"></a>OperationNotAllowed。 不允许删除虚拟机规模集，因为它包含一个或多个 VM。 在删除该虚拟机规模集之前，请先删除或拆离 VM。

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**原因：** 尝试删除与一个或多个虚拟机关联的、处于灵活业务流程模式的规模集。

**解决方法：** 删除与处于灵活业务流程模式的规模集关联的所有虚拟机，然后即可删除该规模集。


<!-- error -->
### <a name="invalidparameter-the-value-true-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-false"></a>InvalidParameter。 不允许为参数“singlePlacementGroup”指定“True”值。 允许的值为：False。

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**原因：** 为订阅注册了灵活业务流程模式预览版；但是，`singlePlacementGroup` 参数设置为 *True*。

**解决方法：** 必须将 `singlePlacementGroup` 设置为 *False*。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用 Azure 门户创建采用灵活业务流程模式的规模集。](flexible-virtual-machine-scale-sets-portal.md)