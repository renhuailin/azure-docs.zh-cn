---
title: 高可用性和灾难恢复
titleSuffix: Azure Digital Twins
description: 介绍 Azure 和 Azure 数字孪生功能，这些功能可帮助你使用灾难恢复功能构建高可用性 Azure IoT 解决方案。
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 35f4aae246f105d832aaf92c5c5797c8a65b44f1
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938525"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure 数字孪生高可用性和灾难恢复

复原 IoT 解决方案的一个重要方面是业务连续性和灾难恢复。 设计 **高可用性 (HA)** 和 **灾难恢复 (DR)** 可帮助你定义和实现解决方案的正常运行时间目标。

本文介绍 Azure 数字孪生服务专门提供的 HA 和 DR 功能。

Azure 数字孪生支持以下功能选项：
* *区域内 HA* –内置冗余，可提供服务运行时间
* *跨区域灾难恢复* –在出现意外的数据中心故障时故障转移到地域配对的 Azure 区域

你还可以查看有关针对 HA/DR 进行设计的一般 Azure 指南的 [*最佳实践*](#best-practices) 部分。

## <a name="intra-region-ha"></a>区域内部 HA
 
Azure 数字孪生通过在服务内实现冗余来提供区域内的 HA。 这会在 [服务 SLA](https://azure.microsoft.com/support/legal/sla/digital-twins) 中反映正常运行时间。 **Azure 数字孪生解决方案开发人员无需执行其他操作即可利用这些 HA 功能。** 尽管 Azure 数字孪生提供相当高的运行时间保证，但仍会出现暂时性故障，这与任何分布式计算平台相同。 应将相应的重试策略内置于与云应用程序交互的组件，以处理暂时性故障。

## <a name="cross-region-dr"></a>跨区域 DR

在某些极少数情况下，数据中心会因为断电或区域中的其他事件而遇到扩展中断。 此类事件很少见，在这种情况下，以上所述的内部区域 HA 功能可能不会有帮助。 Azure 数字孪生通过 Microsoft 启动的故障转移解决了这种情况。

Microsoft 在少数情况下执行的 **故障转移** 会将受影响区域的所有 Azure 数字孪生实例故障转移到相应的地理配对区域。 此过程是 (的默认选项，用户无法选择退出) ，无需用户进行干预。 Microsoft 有权决定何时执行此选项。 在用户实例进行故障转移之前，此机制不涉及用户同意。

>[!NOTE]
> 某些 Azure 服务还提供了一个名为 " **客户启动的故障转移**" 的附加选项，使客户只需为其实例启动故障转移，如运行 DR 演练。 Azure 数字孪生目前 **不支持** 此机制。 

## <a name="monitor-service-health"></a>监视服务运行状况

当 Azure 数字孪生实例进行故障转移和恢复时，可以使用 [Azure 服务运行状况](https://docs.microsoft.com/azure/service-health/service-health-overview) 工具监视此过程。 服务运行状况跨不同的区域和订阅跟踪 Azure 服务的运行状况，并共享服务影响有关中断和停机时间的通信。

在故障转移事件期间，服务运行状况可提供服务关闭的时间和备份时间。

查看服务运行状况事件 .。。
1. 在 Azure 门户中导航到 " [服务运行状况](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) " (你可以使用此链接或使用门户搜索栏) 搜索它。
1. 使用左侧菜单切换到 *运行状况历史记录* 页。
1. 查找从 **Azure 数字孪生** 开始的 *问题名称*，并选择它。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="显示运行状况历史记录页的 Azure 门户屏幕截图。过去几天有几个问题列表，其中突出显示了一个名为 &quot;Azure 数字孪生-西欧降低&quot; 的问题。" lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. 有关中断的一般信息，请查看 " *摘要* " 选项卡。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="在 &quot;运行状况历史记录&quot; 页上，突出显示 &quot;摘要&quot; 选项卡。选项卡将显示常规信息，例如受影响的资源、其区域及其订阅。" lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. 有关随时间推移的问题的详细信息和更新，请查看 " *问题更新* " 选项卡。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="在 &quot;运行状况历史记录&quot; 页上，突出显示 &quot;问题更新&quot; 选项卡。该选项卡显示了几个显示一天前状态的条目。" lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


请注意，此工具中显示的信息并不特定于一个 Azure 数字实例。 使用服务运行状况了解在某个区域或订阅中使用 Azure 数字孪生服务时，你可以通过使用 [资源运行状况工具](troubleshoot-resource-health.md) 向下钻取到特定实例并查看这些实例是否受影响，进一步监视某个步骤。

## <a name="best-practices"></a>最佳做法

有关 HA/DR 的最佳实践，请参阅本主题中的以下 Azure 指南： 
* [*Azure 业务连续性技术指南*](/azure/architecture/framework/resiliency/overview)一文介绍了可帮助你考虑业务连续性和灾难恢复的通用框架。 
* [*Azure 应用程序的灾难恢复和高可用性*](/azure/architecture/framework/resiliency/backup-and-recovery)一文提供了有关 azure 应用程序的策略指导原则，以实现高可用性 (HA) 和灾难恢复 (DR) 。

## <a name="next-steps"></a>后续步骤 

详细了解 Azure 数字孪生解决方案入门：
 
* [*Azure 数字孪生是什么？*](overview.md)
* [*快速入门：浏览示例方案*](quickstart-adt-explorer.md)