---
title: 查看 Azure 事件网格指标和设置警报
description: 本文介绍如何使用 Azure 门户查看 Azure 事件网格主题和订阅的指标，并创建相关警报。
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 6f6c119c16452246ec6eeb57ab392b29608938a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598552"
---
# <a name="monitor-event-grid-message-delivery"></a>监视事件网格消息传送 
本文介绍如何使用门户查看事件网格主题和订阅的指标，并创建相关警报。 

> [!IMPORTANT]
> 有关 Azure 事件网格支持的指标的列表，请参阅[指标](metrics.md)。

## <a name="view-custom-topic-metrics"></a>查看自定义主题指标

如果已发布自定义主题，则可以查看其指标。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在主题的搜索栏中，键入“事件网格主题”，然后从下拉列表中选择“事件网格主题” 。 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="搜索并选择“事件网格主题”":::
3. 从主题列表中选择自定义主题。 

    :::image type="content" source="./media/monitor-event-delivery/select-custom-topic.png" alt-text="选择自定义主题":::
4. 在“事件网格主题”页上查看自定义事件主题的指标。 在下图中，展示资源组、订阅等内容的“基本信息”部分已最小化。 

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics.png" alt-text="查看事件指标":::

    可以使用“事件网格主题”页面的“指标”选项卡，创建包含支持的指标的图表。

    :::image type="content" source="./media/monitor-event-delivery/topics-metrics-page.png" alt-text="“主题 - 指标”页":::

    有关示例，请参阅“已发布事件”指标的指标图表。

    :::image type="content" source="./media/monitor-event-delivery/custom-topic-metrics-example.png" alt-text="“已发布事件”指标":::


## <a name="view-subscription-metrics"></a>查看订阅指标
1. 按照上一部分的步骤导航到“事件网格主题”页。 
2. 从底部窗格中选择订阅，如以下示例所示。 

    :::image type="content" source="./media/monitor-event-delivery/select-event-subscription.png" alt-text="选择事件订阅":::    

    也可在 Azure 门户的搜索栏中搜索“事件网格订阅”，然后通过选择“主题类型”、“订阅”和“位置”来查看事件订阅。 

    :::image type="content" source="./media/monitor-event-delivery/event-subscriptions-page.png" alt-text="从“事件网格订阅”页选择事件订阅":::        

    对于自定义主题，请选择“事件网格主题”作为 **主题类型**。 对于系统主题，请选择 Azure 资源的类型，例如“存储帐户(Blob，GPv2)”。 
3. 对于图表中的订阅，请在主页上查看订阅的指标。 可以查看过去 1 小时、6 小时、12 小时、1 天、7 天或 30 天的“常规”、“错误”和“延迟”指标。 

    :::image type="content" source="./media/monitor-event-delivery/subscription-home-page-metrics.png" alt-text="订阅主页上的指标":::    

## <a name="view-system-topic-metrics"></a>查看系统主题指标

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在主题的搜索栏中，键入“事件网格系统主题”，然后从下拉列表中选择“事件网格系统主题” 。 

    :::image type="content" source="./media/monitor-event-delivery/search-system-topics.png" alt-text="搜索并选择“事件网格系统主题”":::
3. 从主题列表中选择系统主题。 

    :::image type="content" source="./media/monitor-event-delivery/select-system-topic.png" alt-text="选择系统主题":::
4. 在“事件网格系统主题”页上查看系统主题的指标。 在下图中，展示资源组、订阅等内容的“基本信息”部分已最小化。 

    :::image type="content" source="./media/monitor-event-delivery/system-topic-overview-metrics.png" alt-text="在概览页上查看系统主题指标":::

    可以使用“事件网格主题”页面的“指标”选项卡，创建包含支持的指标的图表。

    :::image type="content" source="./media/monitor-event-delivery/system-topic-metrics-page.png" alt-text="“系统主题 - 指标”页":::

    > [!IMPORTANT]
    > 有关 Azure 事件网格支持的指标的列表，请参阅[指标](metrics.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- 若要了解如何创建有关指标和活动日志操作的警报，请参阅[设置警报](set-alerts.md)。
- 有关事件传送和重试的信息，请参阅[事件网格消息传送和重试](delivery-and-retry.md)。
