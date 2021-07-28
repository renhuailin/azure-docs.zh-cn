---
title: Azure NetApp 文件服务卷硬配额方式的更改 | Microsoft Docs
description: 介绍卷硬配额使用方式的更改、如何对更改做好规划，以及如何监视和管理容量。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: a0080687d65c7165b0c2a463229a9a817fb045e0
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108288171"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Azure NetApp 文件服务卷硬配额方式的更改

Azure NetApp 文件服务从最初开始就一直使用容量池预配和自动增长机制。 Azure NetApp 文件卷是在所选层和大小的、客户预配的底层容量池中精简预配的。 卷大小（配额）用于提供性能和容量，配额可随时动态调整。 此行为意味着，卷配额目前是用于控制卷带宽的性能杠杆。 目前，当容量占满时，底层容量池会自动增长。   

> [!IMPORTANT] 
> 卷和容量池预配的 Azure NetApp 文件行为将更改为手动且可控的机制 。 从 2021 年 4 月 30 日开始（已更新），卷大小（配额）将管理带宽性能和预配的容量，底层容量池将不再自动增长。 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>更改卷硬配额的原因

许多客户指出初始行为存在三个主要挑战：
* 使用 OS 空间或容量监视工具时，VM 客户端看到的是任何给定卷的精简预配 (100 TiB) 容量，也就是说，我们看到的客户端或应用程序端容量并不准确。
* 由于容量池的自动增长行为，应用程序所有者无法控制预配的容量池空间（和关联的成本）。 在可能快速填满“失控进程”和增大预配容量与成本的环境中，这种情况会带来麻烦。
* 客户希望看到并维持卷大小（配额）与性能之间的直接关联。 在当前的（隐式）过度订阅卷（容量范围）和池自动增长的行为下，只有在主动设置或重置卷配额之后，客户才能获得直接关联。 

许多客户已请求直接控制预配的容量。 他们想要控制和平衡存储容量与利用率。 他们还想要控制成本，及其应用程序卷的可用、已用和预配容量与性能的应用程序端和客户端可见性。 

## <a name="what-is-the-volume-hard-quota-change"></a>什么是卷硬配额更改   

随着卷硬配额的更改，Azure NetApp 文件卷将不再精简预配为（最大）100 TiB。 卷将预配为实际的配置大小（配额）。 此外，底层容量池的使用量达到全容量时，将不再自动增长。 此项更改将反映类似于 Azure 托管磁盘的行为，这种磁盘也是按原样预配的，而不会自动增大容量。

例如，假设在 4-TiB 的超级服务级别容量池中配置了一个 1-TiB 大小（配额）的 Azure NetApp 文件卷。 某个应用程序持续向该卷写入数据。

初始行为：  
* 预期带宽：128 MiB/秒
* 总可用（和客户端可见的）容量：100 TiB   
    你无法在该卷上写入超过此大小的更多数据。
* 容量池：已满时，以 1 TiB 为增量自动增长。
* 卷配额更改：仅更改卷的性能（带宽）。 不会更改客户端可见的容量或可用容量。

更改后的行为：  
* 预期带宽：128 MiB/秒
* 总可用（和客户端可见的）容量：1 TiB。你无法在该卷上写入超过此大小的更多数据。
* 容量池：大小保持为 4 TiB，不会自动增长。 
* 卷配额更改：更改卷的性能（带宽），以及客户端可见的容量或可用容量。

你需要主动监视 Azure NetApp 文件卷和容量池的利用率。 你需要有针对性地更改卷和池的利用率，以实现接近全容量使用。 Azure NetApp 文件将继续允许[动态卷和容量池大小调整操作](azure-netapp-files-resize-capacity-pools-or-volumes.md)。

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>如何操作化卷硬配额更改

本部分提供有关如何操作化卷硬配额的更改以实现平稳过渡的指导。 本部分还将提供有关处理当前预配的卷和容量池、持续监视和警报以及容量管理选项的见解。

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>当前预配的卷和容量池

由于卷硬配额更改，你应该更改自己的操作模型。 预配的卷和容量池需要持续的容量管理。  由于更改后的行为会即刻发生，Azure NetApp 文件团队针对现有的以及以前预配的卷和容量池建议了一系列一次性纠正措施，具体如本部分中所述。

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>一次性纠正或预防措施建议  

卷硬配额更改将导致以前预配的卷和池的预配容量和可用容量发生变化。 因此，可能会出现一些容量分配挑战。 为了避免客户遇到短期的空间不足情况，Azure NetApp 文件团队建议了以下一次性纠正/预防措施： 

* 预配的卷大小：   
    调整每个预配卷的大小，使其中的适当缓冲区基于更改率和警报或大小调整周转时间（20% 的缓冲区基于典型工作负载考虑因素），最大大小为 100 TiB（即[卷大小限制](azure-netapp-files-resource-limits.md#resource-limits)）。 此新卷大小（包括缓冲区容量）应基于以下因素：
    * 预配的卷容量（如果已用的容量小于预配的卷配额）。
    * 已用的卷容量（如果已用的容量大于预配的卷配额）。  
    如果底层容量池无需增长，则卷级别的容量不会额外收费。 作为此项更改的效果，你可能发现卷的带宽限制提高了（如果使用[自动 QoS 容量池类型](azure-netapp-files-understand-storage-hierarchy.md#qos_types)）。

* 预配的容量池大小：   
    调整卷大小后，如果卷大小的总和大于宿主容量池的大小，则容量池必须增大至大于或等于卷总和的大小，最大值为 500 TiB（即[容量池大小限制](azure-netapp-files-resource-limits.md#resource-limits)）。 增加的容量池容量将如常收取 ACR 费用。

在按照以下部分中所述设置监视或警报时如需帮助，请咨询 Azure NetApp 文件专家以验证你的环境。

### <a name="ongoing-capacity-management"></a>持续容量管理  

执行一次性纠正措施后，应实施持续性的流程来监视和管理容量。 以下部分提供有关容量监视和管理的建议与替代做法。

### <a name="monitor-capacity-utilization"></a>监视容量利用率

可以在不同的级别监视容量利用率。 

#### <a name="vm-level-monitoring"></a>VM 级别的监视 

最高级别的监视（与应用程序最靠近）是从应用程序虚拟机内部进行监视。 从 VM 客户端 OS 内部提供的容量报告中，你将发现行为有更改。

在以下两个场景中，假设在 4-TiB 的超级服务级别容量池中配置了一个 1-TiB 大小（配额）的 Azure NetApp 文件卷。 

##### <a name="windows"></a>Windows

Windows 客户端可以使用网络映射驱动器属性来检查卷的已用容量和可用容量。 你可以使用“资源管理器” -> “驱动器” -> “属性”选项  。  

以下示例显示了在行为更改之前 Windows 中的卷容量报告：

![显示行为更改之前卷的示例存储容量的屏幕截图。](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

还可以如下所示在命令提示符下使用 `dir` 命令：

![显示在行为更改之前使用命令显示卷存储容量的屏幕截图。](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

以下示例显示了在行为更改之后 Windows 中的卷容量报告：

![显示行为更改之后卷的示例存储容量的屏幕截图。](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

以下示例显示 `dir` 命令的输出：  

![显示在行为更改之后使用命令显示卷存储容量的屏幕截图。](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux 客户端可以使用 [`df` 命令](https://linux.die.net/man/1/df)检查卷的已用容量和可用容量。 `-h` 选项以用户可读的格式显示大小、已用空间和可用空间，大小单位为 M、G 和 T。

以下示例显示了在行为更改之前 Linux 中的卷容量报告：  

![显示在行为更改之前使用 Linux 显示卷存储容量的屏幕截图。](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

以下示例显示了在行为更改之后 Linux 中的卷容量报告：  

![显示在行为更改之后使用 Linux 显示卷存储容量的屏幕截图。](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>使用 ANFCapacityManager 配置警报

可以使用社区支持的逻辑应用 ANFCapacityManager 工具来监视 Azure NetApp 文件容量并接收定制的警报。 ANFCapacityManager 工具已在 [ANFCapacityManager GitHub 页](https://github.com/ANFTechTeam/ANFCapacityManager)上提供。

ANFCapacityManager 是一个 Azure 逻辑应用，用于管理基于容量的警报规则。 它会自动增大卷大小，以防止 Azure NetApp 文件卷耗尽空间。 此工具易于部署，可提供以下警报管理功能：

* 创建 Azure NetApp 文件容量池或卷时，ANFCapacityManager 会根据指定的已用百分比阈值创建指标警报规则。
* 调整 Azure NetApp 文件容量池或卷的大小时，ANFCapacityManager 会根据指定的已用容量百分比阈值修改指标警报规则。 如果该警报规则不存在，将创建该规则。
* 删除 Azure NetApp 文件容量池或卷时，将删除相应的指标警报规则。

可配置以下关键警报设置：  

* 容量池已满阈值(百分比) - 此设置确定触发容量池警报的已用量阈值。 如果值为 90，则会在容量池使用率达到 90% 时触发警报。
* 卷已满阈值(百分比) - 此设置确定触发卷警报的已用量阈值。 如果值为 80，则会在卷使用率达到 80% 时触发警报。
* 用于处理容量通知的现有操作组 - 此设置指定基于容量的警报触发的操作组。 系统应该已预先创建此设置。 操作组可以发送电子邮件、短信或其他格式。

以下插图显示了警报配置：  

![显示使用 ANFCapacityManager 配置警报的插图。](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

安装 ANFCapacityManager 后，预期可能会出现以下行为：创建、修改或删除 Azure NetApp 文件容量池或卷时，逻辑应用将自动创建、修改或删除名为 `ANF_Pool_poolname` 或 `ANF_Volume_poolname_volname` 的基于容量的指标警报规则。 

### <a name="manage-capacity"></a>管理容量

除了监视和警报外，还应结合应用程序容量管理做法来管理 Azure NetApp 文件（增大的）容量使用率。 当 Azure NetApp 文件卷或容量池已填满时，[可以动态提供额外的容量，而无需中断应用程序](azure-netapp-files-resize-capacity-pools-or-volumes.md)。 本部分介绍按需增大卷和容量池预配空间的各种手动和自动方法。
 
#### <a name="manual"></a>手动 

可以使用门户或 CLI 来手动增大卷或容量池的大小。 

##### <a name="portal"></a>门户 

可根据需要[更改卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume-using-the-azure-portal)。 卷的容量消耗是依据其池的预配容量计数的。

1. 在“管理 NetApp 帐户”边栏选项卡中，单击“卷”。  
2. 右键单击要调整大小的卷的名称，或单击卷所在行末尾的 `…` 图标以显示上下文菜单。 
3. 使用上下文菜单选项来重设卷大小或删除卷。   

   ![显示卷上下文菜单选项的屏幕截图。](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![显示“更新卷配额”窗口的屏幕截图。](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

在某些情况下，宿主容量池不提供足够的容量用于调整卷的大小。 但是，可以以 1-TiB 为增量或减量[更改容量池大小](azure-netapp-files-resize-capacity-pools-or-volumes.md#resizing-the-capacity-pool-or-a-volume-using-azure-cli)。 容量池大小不能小于 4 TiB。 *重设容量池大小会更改购买的 Azure NetApp 文件容量。*

1. 在“管理 NetApp 帐户”边栏选项卡中，单击要重设大小的容量池。
2. 右键单击容量池名称，或单击容量池所在行末尾的 `…` 图标以显示上下文菜单。
3. 使用上下文菜单选项来重设容量池大小或删除容量池。    

   ![显示容量池上下文菜单选项的屏幕截图。](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![显示“调整池大小”窗口的屏幕截图。](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI 或 PowerShell

可以使用 [Azure NetApp 文件 CLI 工具](azure-netapp-files-sdk-cli.md#cli-tools)（包括 Azure CLI 和 Azure PowerShell）手动更改卷或容量池的大小。  可使用以下两个命令来管理 Azure NetApp 文件卷和池资源：  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

若要使用 Azure CLI 管理 Azure NetApp 文件资源，可以打开 Azure 门户，并在菜单栏顶部选择 Azure“Cloud Shell”链接： 

[ ![显示如何访问“Cloud Shell”链接的屏幕截图。](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png) ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

此操作将打开 Azure Cloud Shell：

[ ![显示“Cloud Shell”窗口的屏幕截图。](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png) ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

以下示例使用命令来[显示](/cli/azure/netappfiles/volume#az_netappfiles_volume_show)和[更新](/cli/azure/netappfiles/volume#az_netappfiles_volume_update)卷的大小：
 
[ ![展示如何使用 PowerShell 显示卷大小的屏幕截图。](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png) ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[ ![展示如何使用 PowerShell 更新卷大小的屏幕截图。](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png) ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

以下示例使用命令来[显示](/cli/azure/netappfiles/pool#az_netappfiles_pool_show)和[更新](/cli/azure/netappfiles/pool#az_netappfiles_pool_update)容量池的大小：

[ ![展示如何使用 PowerShell 显示容量池大小的屏幕截图。](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png) ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[ ![展示如何使用 PowerShell 更新容量池大小的屏幕截图。](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png) ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>自动  

可以构建自动化流程来管理更改后的行为。

##### <a name="rest-api"></a>REST API   

适用于 Azure NetApp 文件服务的 REST API 定义了针对 NetApp 帐户、容量池、卷和快照等资源执行的 HTTP 操作。 Azure NetApp 文件的 REST API 规范已通过 [Azure NetApp 文件资源管理器 GitHub 页](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)发布。 可以在 GitHub 中找到 [与 REST API 配合使用的示例代码](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples)。

请参阅[使用 REST API 进行 Azure NetApp 文件开发](azure-netapp-files-develop-with-rest-api.md)。 

##### <a name="rest-api-using-powershell"></a>通过 PowerShell 使用 REST API  

适用于 Azure NetApp 文件服务的 REST API 定义了针对 NetApp 帐户、容量池、卷和快照等资源执行的 HTTP 操作。 [Azure NetApp 文件的 REST API 规范](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)已通过 GitHub 发布。

请参阅[使用 PowerShell 通过 REST API 进行 Azure NetApp 文件开发](develop-rest-api-powershell.md)。

##### <a name="capacity-management-using-anfcapacitymanager"></a>使用 ANFCapacityManager 进行容量管理

ANFCapacityManager 是一个 Azure 逻辑应用，用于管理基于容量的警报规则。 它会自动增大卷大小，以防止 Azure NetApp 文件卷耗尽空间。 除了发送警报以外，它还可以启用卷和容量池大小的自动增大，以防止 Azure NetApp 文件卷耗尽空间： 

* （可选）当 Azure NetApp 文件卷达到指定的已用百分比阈值时，卷配额（大小）将增大指定的百分比 (10-100)。  
* 如果卷大小在增大后超过了其所在容量池的容量，则还会增大容量池的大小，以适应新的卷大小。

可以配置以下关键容量管理设置：  

* 自动增长百分比 - 当现有卷达到了指定的已满百分比阈值时，要将该卷自动增长的百分比 。 0（零）值将禁用自动增长功能。 建议使用介于 10 和 100 之间的值。

    ![显示“设置卷自动增长百分比”窗口的屏幕截图。](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>常见问题解答 

本部分解答有关卷硬配额更改的一些问题。 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>快照空间是否计入卷的可用容量或预配容量？

是，已用快照容量将计入卷中预配的空间。 当卷已满时，请考虑以下两个修正选项：

* 按本文所述调整卷的大小。
* 删除较旧的快照，以释放宿主卷中的空间。

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>此项更改是否意味着卷自动增长行为将从 Azure NetApp 文件中消失？

一个常见的错误认知是，Azure NetApp 文件卷在填满后会自动增长。 不管实际设置的配额是什么，卷都精简预配为 100 TiB 大小，而底层容量池将以 1-TiB 增量自动增长。 此更改会把（可视和可用）卷大小存入集配额，并且容量池将不再自动增长。 此项更改可以提供用户通常所期望的准确客户端空间和容量报告。 它避免了“失控的”容量消耗。

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>此项更改是否对使用跨区域复制（预览版）复制的卷造成任何影响？ 

不会对复制目标卷强制实施硬性的卷配额。

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>此项更改是否对 Azure Monitor 中当前提供的指标造成任何影响？

门户指标和 Azure Monitor 统计信息将准确反映新的分配和使用模型。

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>此项更改是否对 Azure NetApp 文件的资源限制造成任何影响？

除了本文中所述的配额更改以外，Azure NetApp 文件的资源限制没有任何变化。

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>你们是否提供了示例 ANFCapacityManager 工作流？  

是。 请参阅 [卷自动增长工作流示例 GitHub 页](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md)。

### <a name="is-anfcapacitymanager-microsoft-supported"></a>ANFCapacityManager 是否由 Microsoft 提供支持？  

[ANFCapacityManager 逻辑应用是按原样提供的，NetApp 或 Microsoft 不负责为其提供支持](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer)。 我们建议你根据具体的环境或要求对其进行修改。 将此功能部署到任何业务关键型环境或生产环境之前，应该对其进行测试。

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>如何报告 ANFCapacityManger 的 bug 或提交其功能请求？
可以在 [ANFCapacityManager GitHub 页](https://github.com/ANFTechTeam/ANFCapacityManager/issues)上单击“新问题”来提交 bug 和功能请求。

## <a name="next-steps"></a>后续步骤
* [重设容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Azure NetApp 文件的指标](azure-netapp-files-metrics.md)
