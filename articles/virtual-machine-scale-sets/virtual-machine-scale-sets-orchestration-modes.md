---
title: Azure 中虚拟机规模集的业务流程模式
description: 了解如何对 Azure 中的虚拟机规模集使用灵活和统一业务流程模式。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex, devx-track-azurepowershell
ms.openlocfilehash: be8c322bafb5dfaf3fadecfadfd5f9b1ec9a95f1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804086"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>预览：Azure 中虚拟机规模集的业务流程模式


适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

虚拟机规模集提供平台管理的虚拟机的逻辑分组。 使用规模集可以创建虚拟机配置模型，根据 CPU 或内存负载自动添加或删除其他实例，并自动升级到最新的 OS 版本。 在传统上，规模集允许使用在创建规模集时提供的 VM 配置模型来创建虚拟机，并且规模集只能管理基于配置模型隐式创建的虚拟机。 

规模集业务流程模式可让你更好地控制规模集管理虚拟机实例的方式。

> [!IMPORTANT]
> 业务流程模式是在创建规模集时定义的，以后无法更改或更新。


## <a name="scale-sets-with-uniform-orchestration"></a>使用统一业务流程的规模集
已针对包含相同实例的大规模无状态工作负载进行优化。

使用统一业务流程的虚拟机规模集利用虚拟机配置文件或模板纵向扩展到所需的容量。 统一模式使用相同的 VM 实例，不过，在一定程度上也能够管理或自定义单独的虚拟机实例。 单独的统一 VM 实例通过虚拟机规模集 VM API 命令公开。 单独的实例与标准的 Azure IaaS VM API 命令以及 Azure 资源管理器资源标记 RBAC 权限、Azure 备份或 Azure Site Recovery 等 Azure 管理功能不兼容。 当统一业务流程中配置的实例数少于 100 时，它可以提供容错域高可用性保证。 统一业务流程已推出正式版，支持全套规模集管理和业务流程，包括基于指标的自动缩放、实例保护和自动 OS 升级。


## <a name="scale-sets-with-flexible-orchestration"></a>使用灵活业务流程的规模集
大规模实现相同或多个虚拟机类型的高可用性

借助灵活业务流程，Azure 可在整个 Azure VM 生态系统中提供统一的体验。 灵活业务流程将 VM 分散到某个地理区域或可用性区域的多个容错域中，从而提供高可用性保证（最多支持 1000 个 VM）。 这样，你便可以横向扩展应用程序，同时保持对容错域的隔离，这对于运行基于仲裁的工作负载或有状态工作负载而言至关重要，包括：
- 基于仲裁的工作负载
- 开源数据库
- 有状态应用程序
- 需要高可用性和较大规模的服务
- 需要混合虚拟机类型，或同时利用零星 VM（使用富余容量的 VM）和按需 VM 的服务
- 现有的可用性集应用程序

> [!IMPORTANT]
> 采用灵活业务流程模式的虚拟机规模集目前以公共预览版提供。 需要执行一个选用过程才能使用下述公共预览版功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>灵活业务流程模式有哪些变化？
灵活业务流程的一个主要优势是，它基于标准 Azure IaaS VM（而不是规模集子虚拟机）提供业务流程功能。 这意味着，可以在管理灵活业务流程实例时使用所有的标准 VM API，而无需像在统一业务流程中那样使用虚拟机规模集 VM API。 在预览期，在灵活业务流程与统一业务流程中管理实例存在几点差异。 一般情况下，我们建议尽量使用标准 Azure IaaS VM API。 本部分重点说明有关使用灵活业务流程管理 VM 实例的最佳做法示例。

### <a name="assign-fault-domain-during-vm-creation"></a>创建 VM 期间分配容错域
可为灵活业务流程规模集选择容错域的数目。 默认情况下，将 VM 添加到灵活规模集时，Azure 会在多个容错域之间均匀分散实例。 尽管我们建议让 Azure 分配容错域，但对于高级或故障排除方案，你可以替代此默认行为，指定要将实例放入哪个容错域。

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>实例命名
创建 VM 并将其添加到灵活规模集时，可以在 Azure 命名约定规则中全面控制实例名称。 通过自动缩放将 VM 自动添加到规模集时，你可以提供一个前缀，而 Azure 会在名称的末尾追加唯一的编号。

### <a name="query-instances-for-power-state"></a>查询实例以获取电源状态
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

### <a name="scale-sets-vm-batch-operations"></a>规模集 VM 批处理操作
使用标准 VM 命令来启动、停止、重启、删除实例，而无需使用虚拟机规模集 VM API。 虚拟机规模集 VM 批处理操作（全部启动、全部停止、全部重置映像等）不适合与灵活业务流程模式配合使用。

### <a name="monitor-application-health"></a>监视应用程序运行状况
应用程序运行状况监视可让应用程序为 Azure 提供检测信号，以确定应用程序是否正常。 Azure 可以自动替换不正常的 VM 实例。 对于灵活规模集实例，必须在虚拟机上安装并配置应用程序运行状况扩展。 对于统一规模集实例，可以使用应用程序运行状况扩展，或者使用 Azure 负载均衡器自定义运行状况探测来度量运行状况。

### <a name="list-scale-sets-vm-api-changes"></a>列出规模集 VM API 更改
虚拟机规模集允许列出属于该规模集的实例。 对于灵活业务流程，“列出虚拟机规模集 VM”命令将提供规模集 VM ID 的列表。 然后，可以调用“获取虚拟机规模集 VM”命令，以获取有关规模集如何使用 VM 实例的更多详细信息。 若要获取 VM 的完整详细信息，请使用标准的“获取 VM”命令或 [Azure Resource Graph](../governance/resource-graph/overview.md)。

### <a name="retrieve-boot-diagnostics-data"></a>检索启动诊断数据
使用标准 VM API 和命令检索实例启动诊断数据和屏幕截图。 虚拟机规模集 VM 启动诊断 API 和命令不适用于灵活业务流程模式实例。

### <a name="vm-extensions"></a>VM 扩展
使用面向标准虚拟机的扩展，而不是面向统一业务流程模式实例的扩展。


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>灵活模式、统一模式和可用性集的比较
下表按功能对灵活业务流程模式、统一业务流程模式和可用性集做了比较。

| 功能  | 受灵活业务流程（预览版）的支持  | 受统一业务流程（正式版）的支持  | 受 AvSets（正式版）的支持  |
|-|-|-|-|
| 虚拟机类型  | 标准 Azure IaaS VM (Microsoft.compute /virtualmachines)  | 特定于规模集的 VM (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | 标准 Azure IaaS VM (Microsoft.compute /virtualmachines)  |
| 支持的 SKU  | D 系列、E 系列、F 系列、A 系列、B 系列、Intel、AMD  | 所有 SKU  | 所有 SKU  |
| 可用性区域  | （可选）指定将所有实例放入单个可用性区域  | 指定将实例放入 1 个、2 个或 3 个可用性区域  | 不支持  |
| 容错域 - 最大分散（Azure 将最大程度地分散实例）  | 是  | 是  | 否  |
| 容错域 - 固定分散  | 2-3 个 FD（取决于区域的最大计数）；为局部区域部署提供 1 个  | 2、3、5 个 FD；为局部区域部署提供 1 个、5 个  | 2-3 个 FD（取决于区域的最大计数）  |
| 更新域  | 已弃用，按 FD 依次执行平台维护 | 5 个更新域  | 最多 20 个更新域  |
| 可用性 SLA  | 暂时不  | 如果单个放置组中 FD>1，则为 99.95%；如果实例分散到多个区域，则为 99.99%  | 99.95%  |
| 全面控制 VM、NIC、磁盘  | 是  | 使用虚拟机规模集 VM API 进行有限控制  | 是  |
| 自动缩放（手动、基于指标、基于计划）  | 是  | 是  | 否  |
| 将 VM 分配到特定的容错域  | 是  | 否  | 否  |
| 删除 VM 实例时自动删除 NIC 和磁盘  | 是  | 是  | 否  |
| 升级策略（VM 规模集）  | 否，在创建过程中，升级策略必须为 null 或 []  | 自动、滚动、手动  | 空值  |
| 基于映像的自动 OS 更新  | 否  | 是  | 空值  |
| 来宾安全修补  | 是  | 否  | 是  |
| 终止通知（VM 规模集）  | 是  | 是  | 空值  |
| 实例修复（VM 规模集）  | 是  | 是  | 空值  |
| 加速网络  | 否  | 是  | 是  |
| “零星”实例和定价   | 是，可以同时配置“零星”和“常规”优先级实例  | 是，实例必须全部为“零星”，或全部为“常规”  | 否，仅限常规优先级实例  |
| 混合操作系统  | 是，Linux 和 Windows 可以驻留在同一个灵活规模集中  | 否，实例的操作系统相同  | 是，Linux 和 Windows 可以驻留在同一个灵活规模集中  |
| 监视应用程序运行状况  | 应用程序运行状况扩展  | 应用程序运行状况扩展或 Azure 负载均衡器探测  | 应用程序运行状况扩展  |
| UltraSSD 磁盘   | 是  | 是，仅适用于局部区域部署  | 否  |
| Infiniband   | 否  | 是，仅限单个放置组  | 是  |
| 写入加速器   | 否  | 是  | 是  |
| 邻近放置组   | 是  | 是  | 是  |
| Azure 专用主机   | 否  | 是  | 是  |
| 基本 SLB   | 否  | 是  | 是  |
| Azure 负载均衡器标准版 SKU  | 是  | 是  | 是  |
| 应用程序网关  | 是  | 是  | 是  |
| 维护控制   | 否  | 是  | 是  |
| 列出集中的 VM  | 是  | 是  | 是，列出 AvSet 中的 VM  |
| Azure 警报  | 否  | 是  | 是  |
| VM Insights  | 否  | 是  | 是  |
| Azure 备份  | 是  | 否  | 是  |
| Azure Site Recovery  | 是（通过 PowerShell）  | 否  | 是  |
| Service Fabric  | 否  | 是  | 否  |
| Azure Kubernetes 服务 (AKS)/AKE  | 否  | 是  | 否  |


## <a name="get-started-with-flexible-orchestration-mode"></a>开始使用灵活业务流程模式

注册并开始使用适用于虚拟机规模集的[灵活业务流程模式](..\virtual-machines\flexible-virtual-machine-scale-sets.md)。 


## <a name="frequently-asked-questions"></a>常见问题

- **灵活业务流程支持多大的规模？**

    最多可将 1000 个 VM 添加到处于灵活业务流程模式的规模集。

- **与可用性集或统一业务流程相比，灵活业务流程的可用性如何？**

    | 可用性属性  | 灵活业务流程  | 统一业务流程  | 可用性集  |
    |-|-|-|-|
    | 跨可用性区域部署  | 否  | 是  | 否  |
    | 在地理区域中提供容错域可用性保证  | 是，最多可将 1000 个实例分散到地理区域中的最多 3 个容错域。 最大容错域数目因地理区域而异  | 是，最多 100 个实例  | 是，最多 200 个实例  |
    | 放置组  | 灵活模式始终使用多个放置组 (singlePlacementGroup = false)  | 可以选择单个放置组或多个放置组 | 空值  |
    | 更新域  | 无，维护或主机更新是按每个容错域执行的  | 最多 5 个更新域  | 最多 20 个更新域  |

- 在提供容错域可用性保证的情况下，绝对的最大实例计数是多少？

    | 功能  | 受灵活业务流程（预览版）的支持  | 受统一业务流程（正式版）的支持  | 受 AvSets（正式版）的支持  |
    |-|-|-|-|
    | 最大实例计数（提供 FD 可用性保证）  | 1000  | 3000  | 200  |

## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>排查使用灵活业务流程的规模集的问题
找到适合你的故障排除方案的解决方案。

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**原因：** 未为订阅注册灵活业务流程模式公共预览版。

**解决方法：** 按照前面的说明注册灵活业务流程模式公共预览版。

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**原因：** `platformFaultDomainCount` 参数对于所选的地理区域或局部区域无效。

**解决方法：** 必须选择有效的 `platformFaultDomainCount` 值。 对于局部区域部署，`platformFaultDomainCount` 最大值为 1。 对于未指定局部区域的地理区域部署，`platformFaultDomainCount` 最大值因地理区域而异。 请参阅[管理用于脚本的 VM 的可用性](../virtual-machines/availability.md)，以确定每个区域的最大容错域数目。

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**原因：** 尝试删除与一个或多个虚拟机关联的、处于灵活业务流程模式的规模集。

**解决方法：** 删除与处于灵活业务流程模式的规模集关联的所有虚拟机，然后即可删除该规模集。

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**原因：** 为订阅注册了灵活业务流程模式预览版；但是，`singlePlacementGroup` 参数设置为 *True*。

**解决方法：** 必须将 `singlePlacementGroup` 设置为 *False*。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解如何在规模集上部署应用程序。](virtual-machine-scale-sets-deploy-app.md)
