---
title: Azure 服务总线 - 自动更新消息传送单元
description: 本文介绍如何使用服务总线命名空间的自动更新消息传送单元。
ms.topic: how-to
ms.date: 03/03/2021
ms.openlocfilehash: 7fc3aca82b8f01d70dec4fc2dac7842895417ec9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177949"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>自动更新 Azure 服务总线命名空间的消息传送单元。 
自动缩放是指在处理应用程序负载时让适当数量的资源运行。 当负载增加时，它可以添加资源来处理增加的负载；当资源空闲时，它可以删除资源以节省资金。 请参阅 [Microsoft Azure 自动缩放概述](../azure-monitor/autoscale/autoscale-overview.md)，详细了解 Azure Monitor 的自动缩放功能。 

服务总线高级消息传送在 CPU 和内存级别提供资源隔离，以便每个客户工作负荷以隔离方式运行。 此资源容器称为 **消息传送单元**。 若要详细了解消息传送单元，请参阅[服务总线高级消息传送](service-bus-premium-messaging.md)。 

通过使用服务总线高级命名空间的自动缩放功能，你可以指定最大或最小的[消息传送单元](service-bus-premium-messaging.md)数，并基于一组规则自动添加或删除消息传送单元。 

例如，你可以使用自动缩放功能为服务总线命名空间实现以下缩放场景。 

- 在命名空间的 CPU 使用率超过 75% 时增加服务总线命名空间的消息传送单元。 
- 在命名空间的 CPU 使用率低于 25% 时减少服务总线命名空间的消息传送单元。 
- 在营业时间内使用较多的消息传送单元，在下班时间内使用较少的消息传送单元。 

本文介绍如何在 Azure 门户中自动缩放服务总线命名空间（更新[消息传送单元](service-bus-premium-messaging.md)）。 

> [!IMPORTANT]
> 本文仅适用于 Azure 服务总线的高级层。 

## <a name="autoscale-setting-page"></a>“自动缩放设置”页
首先，按照以下步骤导航到服务总线命名空间的“自动缩放设置”页。

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 在搜索栏中，键入“服务总线”，从下拉列表中选择“服务总线”，然后按“ENTER”  。 
1. 从命名空间列表中选择“高级命名空间”。 
1. 切换到“缩放”页。 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="服务总线命名空间 -“缩放”页":::

## <a name="manual-scale"></a>手动缩放 
此设置让你可以为命名空间设置固定的消息传送单元数。 

1. 在“自动缩放设置”页上，选择“手动缩放”（如果尚未选择） 。 
1. 对于“消息传送单元”设置，从下拉列表中选择消息传送单元数。
1. 在工具栏上选择“保存”，保存该设置。 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="手动缩放消息传送单元":::       


## <a name="custom-autoscale---default-condition"></a>自定义自动缩放 - 默认条件
可以通过使用条件来配置消息传送单元的自动缩放。 当没有其他缩放条件匹配时将执行此缩放条件。 可以通过下列方式之一设置默认条件：

- 基于指标的缩放（例如 CPU 或内存使用率）
- 缩放到特定的消息传送单元数

你无法将计划设置为针对默认条件在特定日期或日期范围进行自动缩放。 当没有其他缩放条件匹配计划时将执行此缩放条件。 

### <a name="scale-based-on-a-metric"></a>基于指标缩放
下面的过程演示了如何添加一个条件，以在 CPU 使用率大于 75% 时自动增加消息传送单元（横向扩展），并在 CPU 使用率小于 25% 时减少消息传送单元（横向缩减）。 增量的执行是从 1 到 2，2 到 4，4 到 8 以及 8 到 16 。 与此类似，减量的执行是从 16 到 8，8 到 4，4 到 2 以及 2 到 1。 

1. 在“自动缩放设置”页上，为“选择缩放资源方式”选项选择“自定义自动缩放”  。 
1. 在页面的“默认”部分，指定默认条件的“名称” 。 选择“铅笔”图标来编辑该文本。 
1. 为“缩放模式”选择“基于指标缩放” 。 
1. 选择“+ 添加规则”。 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="默认 - 基于指标缩放":::    
1. 在“缩放规则”页上执行以下步骤：
    1. 从“指标名称”下拉列表中选择指标。 在此示例中，它是 CPU。 
    1. 选择运算符和阈值。 在此示例中，运算符为“大于”，“触发缩放操作的指标阈值”为“75”  。 
    1. 在“操作”部分中选择“操作” 。 在此示例中，操作设置为“增加”。 
    1. 然后选择“添加”
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="默认 - 如果 CPU 使用率大于 75%，则进行横向扩展":::       

        > [!NOTE]
        > 如果在此示例中总体 CPU 使用率超过 75%，则自动缩放功能将增加命名空间的消息传送单元。 增量的执行是从 1 到 2，2 到 4，4 到 8 以及 8 到 16 。 
1. 再次选择“+ 添加规则”，然后在“缩放规则”页上执行以下步骤 ：
    1. 从“指标名称”下拉列表中选择指标。 在此示例中，它是 CPU。 
    1. 选择运算符和阈值。 在此示例中，运算符为“小于”，“触发缩放操作的指标阈值”为“25”  。 
    1. 在“操作”部分中选择“操作” 。 在此示例中，操作设置为“减少”。 
    1. 然后选择“添加” 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="默认 - 如果 CPU 使用率小于 25%，则进行横向缩减":::       

        > [!NOTE]
        > 如果在此示例中总体 CPU 使用率低于 25%，则自动缩放功能将减少命名空间的消息传送单元。 减量的执行是从 16 到 8，8 到 4，4 到 2 以及 2 到 1。 
1. 设置最大和最小及默认的消息传送单元数  。

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="基于指标的默认规则":::
1. 在工具栏上选择“保存”，保存自动缩放设置。 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>缩放到特定的消息传送单元数
请执行以下步骤，将该规则配置为缩放命名空间以使用特定数量的消息传送单元。 同样，如果其他任何缩放条件都不匹配，则会应用默认条件。 

1. 在“自动缩放设置”页上，为“选择缩放资源方式”选项选择“自定义自动缩放”  。 
1. 在页面的“默认”部分，指定默认条件的“名称” 。 
1. 为“缩放模式”选择“缩放到特定消息传送单元数” 。 
1. 对于“消息传送单元数”，选择默认消息传送单元数。 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="默认值 - 缩放到特定消息传送单元":::       

## <a name="custom-autoscale---additional-conditions"></a>自定义自动缩放 - 其他条件
上一部分说明了如何为自动缩放设置添加默认条件。 该部分说明如何将更多条件添加到自动缩放设置。 对于这些额外的非默认条件，可以基于特定的一周中的某天或某个日期范围设置计划。 

### <a name="scale-based-on-a-metric"></a>基于指标缩放
1. 在“自动缩放设置”页上，为“选择缩放资源方式”选项选择“自定义自动缩放”  。 
1. 选择“默认”块下的“添加缩放条件” 。 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="自定义 - 添加缩放条件链接":::    
1. 为条件指定名称。 
1. 确认已选择“基于指标的缩放”选项。 
1. 选择“+ 添加规则”，添加一个规则，以在总体 CPU 使用率超过 75% 时增加消息传送单元。 执行[默认条件](#custom-autoscale---default-condition)部分中的步骤。 
5. 设置最大和最小及默认的消息传送单元数  。
6. 可以针对自定义条件（而不是默认条件）设置计划。 可以为条件指定开始日期和结束日期（或）选择一周中的特定日期（星期一、星期二等）。 
    1. 如果选择“指定开始/结束日期”，则选择“时区”、“开始日期和时间”和“结束日期和时间”（如下图所示）以使条件生效   。 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="消息传送单元数的最小值、最大值和默认值":::
    1. 如果选择“重复特定日期”，则选择应用该条件的星期、时区、开始时间和结束时间。 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="重复特定日期":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>缩放到特定的消息传送单元数
1. 在“自动缩放设置”页上，为“选择缩放资源方式”选项选择“自定义自动缩放”  。 
1. 选择“默认”块下的“添加缩放条件” 。 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="自定义 - 添加缩放条件链接":::    
1. 为条件指定名称。 
2. 为“缩放模式”选择“缩放到特定消息传送单元数”选项 。 
1. 从下拉列表中选择“消息传送单元”数。 
6. 对于计划，为条件指定开始日期和结束日期（或）选择一周中的特定日期（星期一、星期二等）和时间。 
    1. 如果选择“指定开始/结束日期”，则选择“时区”、“开始日期和时间”和“结束日期和时间”以使条件生效   。 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="缩放到特定消息传送单元 - 开始和结束日期":::        
    1. 如果选择“重复特定日期”，则选择应用该条件的星期、时区、开始时间和结束时间。
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="缩放到特定消息传送单元 - 重复特定日期":::

    
    若要详细了解自动缩放设置的工作原理，尤其是它如何选择配置文件或条件并评估多个规则的详细信息，请参阅[了解自动缩放设置。](../azure-monitor/autoscale/autoscale-understanding-settings.md)          

    > [!NOTE]
    > - 针对自动缩放做出的决定的指标可能为 5-10 分钟。 当你处理高峰工作负载时，建议纵向扩展的持续时间短一点，纵向缩减的持续时间长一点（> 10 分钟），以确保有足够的消息传送单元来处理高峰工作负载。 
    > 
    > - 如果出现由于缺少容量而导致的故障（没有可用的消息传送单元），请向我们提出支持票证。  


## <a name="next-steps"></a>后续步骤
若要了解消息传送单元，请参阅[高级消息传送](service-bus-premium-messaging.md)

