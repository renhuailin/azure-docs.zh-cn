---
title: 对于 Azure NetApp 文件服务，对批量硬配额的更改Microsoft Docs
description: 描述使用批量硬配额的更改、如何计划更改以及如何监视和管理容量。
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
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 69a970b7b088795602dfb842b20ad6457d5f27c4
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808114"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>对于 Azure NetApp 文件服务，对批量硬配额的更改

从服务的开始，Azure NetApp 文件使用了容量池预配和自动增长机制。 Azure NetApp 文件卷是在所选层和大小的 underlaying 的、客户预配的容量池上精简预配的。 卷大小 (配额) 用于提供性能和容量，并且可随时随时调整配额。 此行为意味着，当前卷配额是用于控制卷带宽的性能杠杆。 目前，当容量填满时，underlaying 容量池会自动增长。   

> [!IMPORTANT] 
> 卷和容量池预配的 Azure NetApp 文件行为将更改为 *手动* 且 *可控* 的机制。 **从2021年3月15日开始，卷大小 (配额) 会管理带宽性能以及预配的容量，基础容量池将不再自动增长。** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>更改卷硬配额的原因

许多客户通过 *初始* 行为指出了三个主要挑战：
* 当使用 OS 空间或容量监视工具时，VM 客户端会看到精简预配的 (100 TiB) 容量，从而提供不准确的客户端或应用程序端容量可见性。
* 由于容量池自动增长行为的原因，应用程序所有者将无法控制预配的容量池空间 (和关联的成本) 。 这种情况在 "正在运行的进程" 可能快速填满并增加预配的容量和成本的环境中很繁琐。
* 客户希望查看并维护卷大小 (配额) 和性能之间的直接关联。 由于 (隐式) 隐式的行为 (容量) 和池自动增长，因此在主动设置或重置卷配额之前，客户不具有直接关联。 

许多客户已请求直接控制预配的容量。 他们要控制和平衡存储容量和利用率。 他们还想要控制成本，以及应用程序端的应用程序端和客户端可见性，以及它们的应用程序卷的可用容量和性能。 

## <a name="what-is-the-volume-hard-quota-change"></a>什么是批量硬配额更改   

随着批量硬配额的变化，Azure NetApp 文件卷将不再预配为 (最大) 100 TiB 的精简预配。 卷将按实际配置的大小预配， (配额) 。 此外，underlaying 容量池在达到全容量消耗时将不再自动增长。 此更改将反映类似 Azure 托管磁盘的行为，这些磁盘也按原样预配，无需增加容量。

例如，请考虑将 TiB 大小配置为 1-大小的 Azure NetApp 文件卷 (配额) 。 应用程序正在连续将数据写入该卷。

*初始* 行为：  
* 预期带宽： 128 MiB/秒
* ) 容量 (和客户端可见的总可用空间： 100 TiB   
    你将无法在卷上写入超过此大小的更多数据。
* 容量池：在已满时，自动增长1个 TiB 增量。
* 卷配额更改：仅更改卷 (带宽) 的性能。 它不会更改客户端可见或可用容量。

*更改后* 的行为：  
* 预期带宽： 128 MiB/秒
* 可用 (和客户端) 容量的总可用值： 1 TiB 您将无法在卷上写入超过此大小的更多数据。
* 容量池：保持4个 TiB，并且不会自动增长。 
* 卷配额更改：更改性能 (带宽) 以及卷的客户端可见或可用容量。

需要主动监视 Azure NetApp 文件量和容量池的利用率。 需要故意更改卷和池使用率，以实现接近完全的消耗。 Azure NetApp 文件将继续允许 [动态卷和容量池调整大小操作](azure-netapp-files-resize-capacity-pools-or-volumes.md)。

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>如何操作卷硬配额更改

本部分提供有关如何将更改操作批量硬配额以实现平滑转换的指导。 它还提供了有关处理当前预配的卷和容量池、不断监视和警报和容量管理选项的见解。

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>当前预配的卷和容量池

由于卷硬配额更改，你应该更改操作模型。 预配的卷和容量池需要持续的容量管理。  由于更改后的行为会立即发生，Azure NetApp 文件团队为现有的、以前预配的卷和容量池建议了一系列一次性纠正措施，如本部分中所述。

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>一次性纠正或预防措施建议  

卷硬配额更改将导致以前预配的卷和池的预配容量和可用容量发生变化。 因此，可能会出现一些容量分配挑战。 为了避免客户出现短期空间不足的情况，Azure NetApp 文件团队提出了以下一次性的纠错/预防措施： 

* **预配的卷大小**：   
    调整每个预配卷的大小，使其基于更改率和警报或调整周转时间 (例如，根据典型工作负荷注意事项) 20%，最大值为 100 TiB (，这是 [卷大小限制](azure-netapp-files-resource-limits.md#resource-limits)) 。 这一新卷的大小（包括缓冲区容量）应基于以下因素：
    * **预配** 的卷容量，以防使用的容量小于预配的卷配额。
    * **使用** 的卷容量，以防使用的容量大于预配的卷配额。  
    如果 underlaying 容量池无需增长，则不会对卷级别容量增加额外收费。 作为此更改的效果，在) 使用 [自动 QoS 容量池类型](azure-netapp-files-understand-storage-hierarchy.md#qos_types)的情况下，你可能会发现卷的带宽限制 *增加* (。

* **预配的容量池大小**：   
    调整卷大小后，如果卷大小的总和超出了托管容量池的大小，则容量池必须增加到大于或等于卷的总和，最大值为 500 TiB (，即 [容量池大小限制](azure-netapp-files-resource-limits.md#resource-limits)) 。 额外的容量池容量将按正常收费。

如果需要有关设置监视或警报的帮助，请与 Azure NetApp 文件专家合作来验证你的环境，如以下部分所述。

### <a name="ongoing-capacity-management"></a>正在进行的容量管理  

执行一次性纠正措施后，应将正在进行的进程组合在一起来监视和管理容量。 以下部分提供有关容量监视和管理的建议和替代方法。

### <a name="monitor-capacity-utilization"></a>监视容量利用率

可以在不同级别监视容量利用率。 

#### <a name="vm-level-monitoring"></a>VM 级监视 

最接近应用程序) 的最高级别的监视 (在应用程序虚拟机中。 你将在 VM 客户端操作系统内观察容量报告中的行为更改。

在以下两种情况下，请考虑将 TiB 大小配置为 1-大小的 Azure NetApp 文件卷 (配额) 在 TiB 的 Ultra 服务级别容量池。 

##### <a name="windows"></a>Windows

Windows 客户端可以使用网络映射驱动器属性来检查卷的已用容量和可用容量。 你可以使用 "**资源管理器**  ->  **驱动器**  ->  **属性**" 选项。  

以下示例显示了在更改后的行为 *之前* ，在 Windows 中报告的卷容量：

![显示在行为更改之前卷的示例存储容量的屏幕截图。](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

你还可以 `dir` 在命令提示符下使用命令，如下所示：

![屏幕截图，显示在行为更改之前使用命令显示卷的存储容量。](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

以下示例显示了更改后的行为 *后* ，Windows 中的卷容量报告：

![显示行为更改后卷的示例存储容量的屏幕截图。](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

下面的示例演示了 `dir` 命令输出：  

![屏幕截图，显示在行为更改后使用命令显示卷的存储容量。](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux 客户端可以使用[ `df` 命令](https://linux.die.net/man/1/df)检查卷的已用容量和可用容量。 `-h`选项将使用 M、G 和 T 单位大小以用户可读的格式显示大小、已用空间和可用空间。

以下示例显示了 Linux 中的卷容量报告，并 *显示了更改* 的行为：  

![屏幕截图，显示在行为更改之前使用 Linux 显示卷的存储容量。](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

下面的示例显示了在 Linux 中发生更改的行为 *后* 的卷容量报告：  

![屏幕截图，显示在行为更改后使用 Linux 显示卷的存储容量。](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>使用 ANFCapacityManager 配置警报

可以使用社区支持的逻辑应用 ANFCapacityManager 工具监视 Azure NetApp 文件容量并获得定制的警报。 ANFCapacityManager 工具在 [ANFCapacityManager GitHub 页](https://github.com/ANFTechTeam/ANFCapacityManager)上提供。

ANFCapacityManager 是一个 Azure 逻辑应用，用于管理基于容量的警报规则。 它会自动增加卷大小，以防止 Azure NetApp 文件卷用尽空间。 它易于部署，并提供以下警报管理功能：

* 创建 Azure NetApp 文件容量池或卷后，ANFCapacityManager 会根据指定的消耗百分比阈值创建指标警报规则。
* 调整 Azure NetApp 文件容量池或卷大小时，ANFCapacityManager 会根据指定的 "使用的容量百分比" 阈值修改指标警报规则。 如果警报规则不存在，则将创建它。
* 删除 Azure NetApp 文件容量池或卷时，将删除相应的指标警报规则。

你可以配置以下关键警报设置：  

* **容量池% Full 阈值** -此设置确定为容量池触发警报的已使用阈值。 如果值为90，则会在容量池使用达到90% 时触发警报。
* **卷% Full 阈值** -此设置确定触发卷警报的已使用阈值。 如果值为80，将导致在卷达到80% 时触发警报。
* **容量通知的现有操作组** -此设置是将为基于容量的警报触发的操作组。 此设置应由您预先创建。 操作组可以发送电子邮件、短信或其他格式。

下图显示了警报配置：  

![使用 ANFCapacityManager 显示警报配置的插图。](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

安装 ANFCapacityManager 后，可能会出现以下行为：创建、修改或删除 Azure NetApp 文件容量池或卷后，逻辑应用将自动创建、修改或删除具有名称或的基于容量的指标警报规则 `ANF_Pool_poolname` `ANF_Volume_poolname_volname` 。 

### <a name="manage-capacity"></a>管理容量

除了监视和警报外，还应结合应用程序容量管理实践来管理 Azure NetApp 文件 (增加) 容量消耗。 当 Azure NetApp 文件量或容量池填满时， [可以动态提供额外的容量，而无需中断应用程序](azure-netapp-files-resize-capacity-pools-or-volumes.md)。 本部分介绍各种手动和自动方式，根据需要增加卷和容量池预配空间。
 
#### <a name="manual"></a>手动 

可以使用门户或 CLI 手动增加卷或容量池大小。 

##### <a name="portal"></a>门户 

你可以根据需要 [更改卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) 。 卷的容量消耗是依据其池的预配容量计数的。

1. 在“管理 NetApp 帐户”边栏选项卡中，单击“卷”。  
2. 右键单击要调整大小的卷的名称，或单击 `…` 卷行末尾的图标以显示上下文菜单。 
3. 使用上下文菜单选项来重设卷大小或删除卷。   

   ![显示卷的上下文菜单选项的屏幕截图。](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![显示 "更新卷配额" 窗口的屏幕截图。](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

在某些情况下，承载容量池没有足够的容量来调整卷的大小。 但是，你可以 [将容量池大小更改](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) 为 1-TiB 增量或减量。 容量池大小不能小于 4 TiB。 *重设容量池大小会更改购买的 Azure NetApp 文件容量。*

1. 在“管理 NetApp 帐户”边栏选项卡中，单击要重设大小的容量池。
2. 右键单击容量池名称或单击 `…` 容量池行末尾的图标以显示上下文菜单。
3. 使用上下文菜单选项来重设容量池大小或删除容量池。    

   ![显示容量池上下文菜单选项的屏幕截图。](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![显示 "调整池大小" 窗口的屏幕截图。](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI 或 PowerShell

可以使用 [Azure NetApp 文件 CLI 工具](azure-netapp-files-sdk-cli.md#cli-tools)（包括 Azure CLI 和 Azure PowerShell）手动更改卷或容量池大小。  以下两个命令可用于管理 Azure NetApp 文件卷和池资源：  

* [`az netappfiles pool`](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest&preserve-view=true)
* [`az netappfiles volume`](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest&preserve-view=true)

若要使用 Azure CLI 管理 Azure NetApp 文件资源，可以打开 Azure 门户并在菜单栏顶部选择 "Azure **Cloud Shell** " 链接： 

[![显示如何访问 Cloud Shell 链接的屏幕截图。 ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

此操作将打开 Azure Cloud Shell：

[![显示 Cloud Shell 窗口的屏幕截图。 ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

以下示例使用命令 [显示](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show&preserve-view=true) 和 [更新](https://docs.microsoft.com/cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-update&preserve-view=true) 卷的大小：
 
[![显示使用 PowerShell 显示卷大小的屏幕截图。 ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![显示使用 PowerShell 更新卷大小的屏幕截图。 ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

以下示例使用命令 [显示](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-show&preserve-view=true) 和 [更新](https://docs.microsoft.com/cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-update&preserve-view=true) 容量池的大小：

[![显示使用 PowerShell 显示容量池大小的屏幕截图。 ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![显示使用 PowerShell 更新容量池大小的屏幕截图。 ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>自动  

你可以构建一个自动化过程来管理更改后的行为。

##### <a name="rest-api"></a>REST API   

适用于 Azure NetApp 文件服务的 REST API 定义了针对 NetApp 帐户、容量池、卷和快照等资源执行的 HTTP 操作。 Azure NetApp 文件的 REST API 规范通过 [Azure Netapp 文件资源管理器 GitHub 页面](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)] 发布。 可以在 GitHub 中找到 [与 REST api 一起使用的示例代码](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) 。

请参阅 [为 Azure NetApp 文件开发 REST API](azure-netapp-files-develop-with-rest-api.md)。 

##### <a name="rest-api-using-powershell"></a>通过 PowerShell 使用 REST API  

适用于 Azure NetApp 文件服务的 REST API 定义了针对 NetApp 帐户、容量池、卷和快照等资源执行的 HTTP 操作。 [Azure NetApp 文件的 REST API 规范](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)通过 GitHub 发布。

请参阅 [使用 PowerShell 为 Azure NetApp 文件开发 REST API](develop-rest-api-powershell.md)。

##### <a name="capacity-management-using-anfcapacitymanager"></a>使用 ANFCapacityManager 的容量管理

ANFCapacityManager 是一个 Azure 逻辑应用，用于管理基于容量的警报规则。 它会自动增加卷大小，以防止 Azure NetApp 文件卷用尽空间。 除了发送警报以外，它还可以启用卷和容量池大小的自动增加，以防止 Azure NetApp 文件卷用尽空间： 

* 根据需要，当 Azure NetApp 文件量达到指定的消耗百分比阈值时，卷配额 (大小) 将按10-100 之间的指定百分比增加。  
* 如果增加卷大小超过了包含容量池的容量，则还会增加容量池大小以容纳新的卷大小。

你可以配置以下密钥容量管理设置：  

* **增长百分比增加** -现有卷大小的百分比在达到指定的 **% Full 阈值** 时自动增长。 值 0 (零) 将禁用自动增长功能。 建议使用介于10和100之间的值。

    ![显示 "设置音量自动增长百分比" 窗口的屏幕截图。](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>常见问题解答 

本部分回答了有关批量硬配额更改的一些问题。 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>快照空间是否计入卷的可用或预配容量？

是，使用的快照容量计入卷中预配的空间。 如果卷已满，请考虑两个修正选项：

* 按本文所述调整卷的大小。
* 删除较旧的快照以释放托管卷中的空间。

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>此更改是否意味着卷自动增长行为将从 Azure NetApp 文件中消失？

常见的误解是，Azure NetApp 文件 *量* 会在填满后自动增长。 卷的预配大小为 100 TiB，而不考虑实际的设置配额，而 underlaying *容量池* 将自动增长到 1-TiB 增量。 此更改将解决 (查看和可用) *卷* 大小到集配额， *容量池* 将不再自动增长。 此更改会导致通常需要准确的客户端空间和容量报告。 它避免了 "失控" 容量消耗。

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>此更改是否对使用跨区域复制 (预览) 复制的卷有任何影响？ 

不会对复制目标卷强制使用硬卷配额。

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>此更改是否对 Azure Monitor 中当前可用的指标有任何影响？

门户指标和 Azure Monitor 统计信息将精确反映新的分配和使用模式。

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>此更改是否对 Azure NetApp 文件的资源限制有任何影响？

除了本文中所述的配额更改之外，Azure NetApp 文件的资源限制没有变化。

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>是否有示例 ANFCapacityManager 工作流？  

是的。 请参阅 [Volume 自动增长工作流示例 GitHub 页面](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md)。

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Microsoft 是否支持 ANFCapacityManager？  

[ANFCapacityManager 逻辑应用按原样提供，并不受 NetApp 或 Microsoft 支持](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer)。 建议你进行修改以适合你的特定环境或要求。 应在将功能部署到任何业务关键或生产环境之前对其进行测试。

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>如何报告 bug 或向 ANFCapacityManger 提交功能请求？
您可以通过单击 [ANFCapacityManager GitHub 页](https://github.com/ANFTechTeam/ANFCapacityManager/issues)上的 "**新问题**" 来提交 bug 和功能请求。

## <a name="next-steps"></a>后续步骤
* [重设容量池或卷的大小](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Azure NetApp 文件的指标](azure-netapp-files-metrics.md) 