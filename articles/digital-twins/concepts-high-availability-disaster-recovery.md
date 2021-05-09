---
title: 高可用性和灾难恢复
titleSuffix: Azure Digital Twins
description: 介绍了Azure 和 Azure 数字孪生功能，这些功能有助于构建带灾难恢复功能的 Azure IoT 高可用性解决方案。
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ee2bd4ab66b7a67be04c1a710caa12c57c705d19
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108209960"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure 数字孪生高可用性和灾难恢复

可复原 IoT 解决方案的一个重要考量因素是业务连续性和灾难恢复。 此设计专门针对高可用性 (HA) 和灾难恢复 (DR) ，帮助用户为解决方案定义和实现相应的运行时间目标。

本文介绍了 Azure 数字孪生服务专门提供的 HA 和 DR 功能。

Azure 数字孪生支持以下功能选项：
* 区域内部 HA – 内置冗余，可实现服务运行时间
* 跨区域灾难恢复 – 在数据中心出现意外故障时故障转移到异地配对的 Azure 区域

还可以参阅最佳做法部分中有关设计以实现 HA/DR 的 Azure 常规指南。

## <a name="intra-region-ha"></a>区域内部 HA
 
Azure 数字孪生通过在服务内实现冗余来提供区域内部 HA。 这反映在指示运行时间的[服务 SLA](https://azure.microsoft.com/support/legal/sla/digital-twins) 中。 Azure 数字孪生解决方案开发人员无需完成任何额外工作就能利用这些 HA 功能。 尽管 Azure 数字孪生提供相当高的运行时间保证，但与任何分布式计算平台一样，暂时性的故障仍有可能出现。 必须在与云应用程序交互的组件中内置相应重试策略，以处理暂时性故障。

## <a name="cross-region-dr"></a>跨区域 DR

在极少见的情况下，区域中的电源故障或其他事件会导致数据中心遇到长时间的服务中断。 此类事件非常罕见，在这类故障期间，上述区域内部 HA 不一定总能发挥作用。 Azure 数字孪生通过 Microsoft 发起的故障转移解决这种问题。

在少数情况下，Microsoft 会执行 Microsoft 发起的故障转移，以将所有 Azure 数字孪生实例从受影响的区域故障转移到对应的异地配对区域。 该过程是默认选项（用户无法选择禁用），且无需用户的干预。 Microsoft 有权决定何时执行此选项。 在故障转移用户的实例之前，此机制不要求用户许可。

>[!NOTE]
> 某些 Azure 服务还提供名为“客户发起的故障转移”的附加选项，允许客户只为其实例发起故障转移，如运行 DR 钻取。 Azure 数字孪生目前不支持此机制。 

## <a name="monitor-service-health"></a>监视服务运行状况

故障转移和恢复 Azure 数字孪生实例时，可以使用 [Azure 服务运行状况](../service-health/service-health-overview.md)工具监视此过程。 服务运行状况跨不同区域和订阅跟踪 Azure 服务的运行状况，并共享有关服务中断和故障时间的服务影响通信。

在故障转移事件期间，服务运行状况可指示服务关闭时间和备份时间。

查看服务运行状况事件...
1. 导航到 Azure 门户中的[服务运行状况](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues)（可使用此链接，也可以在门户搜索栏中搜索它）。
1. 使用左侧菜单切换到“运行状况历史记录”页。
1. 查找以“Azure 数字孪生”开头的问题名称，并选择它。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="显示“运行状况历史记录”页的 Azure 门户的屏幕截图，其中列出了过去几天出现的几个问题，并突出显示了一个名为“Azure 数字孪生 - 西欧 - 已缓解”的问题。" lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. 有关该服务中断的一般信息，请查看“摘要”选项卡。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="在“运行状况历史记录”页上，“摘要”选项卡突出显示。该选项卡显示一般信息，如受影响的资源及其所在的区域和订阅。" lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. 有关一段时间内问题的详细信息和更新，请查看“问题更新”选项卡。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="在“运行状况历史记录”页上，“问题更新”选项卡突出显示。该选项卡显示多个条目，指示一天前的最新状态。" lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


请注意，此工具中显示的信息并不特定于某一个 Azure 数字实例。 使用服务运行状况了解某个区域或订阅中 Azure 数字孪生服务的运行状况后，可以通过使用[资源运行状况工具](troubleshoot-resource-health.md) 向下钻取到特定实例并查看这些实例是否受影响，来实施进一步监视。

## <a name="best-practices"></a>最佳做法

有关 HA/DR 的最佳做法，请参阅以下关于本主题的 Azure 指南： 
* Azure 业务连续性技术指南一文介绍了一个常规框架来帮助你思考业务连续性和灾难恢复。 
* [Azure 应用程序的灾难恢复和高可用性](/azure/architecture/framework/resiliency/backup-and-recovery)一文针对 Azure 应用程序的高可用性 (HA) 和灾难恢复 (DR) 实现策略提供了体系结构指导。

## <a name="next-steps"></a>后续步骤 

详细了解 Azure 数字孪生解决方案入门：
 
* [Azure 数字孪生是什么？](overview.md)
* [快速入门：探索示例方案](quickstart-azure-digital-twins-explorer.md)