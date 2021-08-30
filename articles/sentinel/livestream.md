---
title: 使用 Azure Sentinel 中的搜寻实时流检测威胁 | Microsoft Docs
description: 本文介绍如何使用 Azure Sentinel 中的搜寻实时流来跟踪数据。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2021
ms.author: yelevin
ms.openlocfilehash: a711cca01431346896ac5d0aee5dce8524ed721f
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202489"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>使用 Azure Sentinel 中的搜寻实时流检测威胁

> [!IMPORTANT]
>
> - 跨资源查询体验（请参阅下面的标记项）当前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

使用搜寻实时流创建交互式会话，以便在事件发生时测试新创建的查询、在找到匹配项时从会话中获取通知，并在必要时启动调查。 可使用任何 Log Analytics 查询快速创建实时流会话。

- **在事件发生时测试新创建的查询**
    
    可测试和调整查询，而不与正在主动应用于事件的当前规则出现任何冲突。 确认这些新查询按预期工作后，可选择一个选项来将会话提升为警报，轻松地将这些查询提升到自定义警报规则。

- **出现威胁时接收通知**
    
    可将威胁数据馈送与聚合日志数据进行比较，并在出现匹配项时接收通知。 威胁数据馈送是正在进行的与潜在威胁或当前威胁相关的数据流，因此通知可能表示你的组织存在潜在威胁。 如果希望就潜在的问题收到通知，但免去自定义警报规则的维护开销，请创建实时流会话，而不是自定义警报规则。

- **启动调查**
    
    如果有待处理的调查涉及到主机或用户等资产，可在该资产上发生特定（或任何）活动时，在日志数据中查看此活动。 可在活动发生时接收通知。


## <a name="create-a-livestream-session"></a>创建实时流会话

可从现有搜寻查询创建实时流会话，也可从头开始创建会话。

1. 在 Azure 门户中，导航到“Sentinel” > “威胁管理” > “搜寻”  。

1. 若要从搜寻查询中创建实时流会话：
    
    1. 从“查询”选项卡中，找到要使用的搜寻查询。
    1. 右键单击该查询，然后选择“添加到实时流”。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![从 Azure Sentinel 搜寻查询创建实时流会话](./media/livestream/livestream-from-query.png)

1. 若要从头开始创建实时流会话： 
    
    1. 选择“实时流”选项卡
    1. 单击“+新建实时流”。
    
1. 在“实时流”窗格中：
    
    - 如果从查询中启动实时流，请查看该查询并进行所需的更改。
    - 如果从头开始启动实时流，请创建查询。

    > [!NOTE]
    > 实时流支持在 Azure 数据资源管理器中跨资源查询（预览版）数据。 [详细了解跨资源查询](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer)。

1. 从命令栏中选择“播放”。
    
    命令栏下的状态栏指示实时流会话是正在运行还是已暂停。 在以下示例中，会话正在运行：
    
    > [!div class="mx-imgBorder"]
    > ![从 Azure Sentinel 搜寻中创建实时流会话](./media/livestream/livestream-session.png)

1. 从命令栏中选择“保存”。
    
    除非选择“暂停”，否则会话将继续运行，直到你从 Azure 门户注销为止。

## <a name="view-your-livestream-sessions"></a>查看实时流会话

1. 在 Azure 门户中，导航到“Sentinel” > “威胁管理” > “搜寻” > “实时流”选项卡   。

1. 选择要查看或编辑的实时流会话。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![从 Azure Sentinel 搜寻查询创建实时流会话](./media/livestream/livestream-tab.png)
    
    你选择的实时流会话将打开，供你执行播放、暂停和编辑等操作。

## <a name="receive-notifications-when-new-events-occur"></a>发生新事件时接收通知

针对新事件的实时流通知会使用 Azure 门户通知，因此每次使用 Azure 门户时，都会看到这些通知。 例如：

![针对实时流的 Azure 门户通知](./media/livestream/notification.png)

选择通知，打开“实时流”窗格。
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>将实时流会话提升为警报

可在实时流会话上的命令栏中选择“提升为警报”，将相关实时流会话提升为新的警报：

> [!div class="mx-imgBorder"]
> ![将实时流会话提升为警报](./media/livestream/elevate-to-alert.png)

此操作将打开规则创建向导，其中预填充了与实时流会话关联的查询。

## <a name="next-steps"></a>后续步骤

在本文中，你学习了如何在 Azure Sentinel 中使用搜寻实时流。 要详细了解 Azure Sentinel，请参阅以下文章：

- [主动搜寻威胁](hunting.md)
- [使用笔记本运行自动搜寻活动](notebooks.md)
