---
title: 使用 Azure Functions 通过事件网格处理服务总线事件
description: 本文提供了使用 Azure Functions 通过事件网格处理服务总线事件的步骤。
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 09/29/2021
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 46dc7a9ac1b985c116162f60c58afb0e8dff9c3a
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273134"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>教程：使用 Azure Functions 响应通过 Azure 事件网格收到的 Azure 服务总线事件
本教程介绍如何使用 Azure Functions 和 Azure 逻辑应用对通过 Azure 事件网格收到的 Azure 服务总线事件做出响应。 

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 创建服务总线命名空间
> * 准备用于发送消息的示例应用程序
> * 向服务总线主题发送消息
> * 使用逻辑应用接收消息
> * 在 Azure 上设置测试函数
> * 通过事件网格连接函数和命名空间
> * 使用 Azure Functions 接收消息

[!INCLUDE [service-bus-event-grid-prerequisites](./includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>其他先决条件
安装 [Visual Studio 2019](https://www.visualstudio.com/vs)，并包含 Azure 开发工作负载。 此工作负载中有在 Visual Studio 中创建、生成和部署 Azure Functions 项目所需的 Azure Functions 工具。 

## <a name="deploy-the-function-app"></a>部署函数应用 

>[!NOTE]
> 若要详细了解如何创建和部署 Azure Functions 应用，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md)

1. 从 SBEventGridIntegration.sln 解决方案的 FunctionApp1 项目中打开 ReceiveMessagesOnEvent.cs 文件  。 
1. 将 `<SERCICE BUS NAMESPACE - CONNECTION STRING>` 替换为服务总线命名空间的连接字符串。 它应与你在同一解决方案中的 MessageSender 项目的 Program.cs 文件中使用的字符串相同 。 
1. 右键单击“FunctionApp1”，然后选择“发布” 。 
1. 在“发布”页面上选择“启动” 。 这些步骤可能与你看到的不同，但发布过程应该是类似的。 
1. 在“发布”向导的“目标”页面上，为“目标”选择“Azure”   。 
1. 在“特定目标”页面上，选择“Azure 函数应用(Windows)” 。 
1. 在“函数实例”页面上，选择“创建新的 Azure 函数” 。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/add-function-button.png" alt-text="图像显示 Visual Studio 的“添加函数”按钮 -“发布”对话框。":::
1. 在“函数应用(Windows)”页面上，执行以下步骤：
    1. 输入函数应用的名称。
    1. 选择 Azure 订阅。
    1. 选择现有 **资源组** 或创建新资源组。 在本教程中，请选择具有服务总线命名空间的资源组。 
    1. 为应用服务选择计划类型。
    1. 选择一个位置。 选择与服务总线命名空间相同的位置。 
    1. 选择一个现有的 Azure 存储，或者选择“新建”来创建一个新的存储帐户供函数应用使用 。 
    1. 选择“创建”以创建函数应用。 
1. 返回到“发布”向导的“函数实例”页面，选择“完成”  。 
1. 在 Visual Studio 中的“发布”页面上选择“发布”，将函数应用发布到 Azure 。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="发布函数应用":::    
1. 在“输出”窗口中，查看来自生成和发布的消息，并确认它们都已成功。 
1. 现在，在“发布”页面上，选择“在 Azure 门户中管理” 。 
1. 在 Azure 门户的“函数应用”页面上，在左侧菜单中选择“函数”，并确认你看到了两个函数 ： 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="用于处理事件网格触发器和 HTTP 触发器的两个函数":::

    > [!NOTE]
    > `EventGridTriggerFunction` 使用[事件网格触发器](../azure-functions/functions-bindings-event-grid-trigger.md)，`HTTPTriggerFunction` 使用 [HTTP 触发器](../azure-functions/functions-bindings-http-webhook-trigger.md)。
1. 从列表中选择 EventGridTriggerFunction。 建议将事件网格触发器用于 Azure Functions，因为它与使用 HTTP 触发器相比有一些优势。 有关详细信息，请参阅 [Azure Functions 作为事件网格事件的事件处理程序](../event-grid/handler-functions.md)。
1. 在 EventGridTriggerFunction 的“函数”页面上，选择左菜单上的“监视”  。 
1. 选择“配置”，配置 Application Insights 来捕获调用日志。 可使用此页面监视从事件网格接收服务总线事件时的函数执行。 
1. 在 Application Insights 页面上，输入资源的名称，为资源选择一个位置，然后选择“确定”  。 
1. 在顶部（痕迹导航菜单）选择 EventGridTriggerFunction 函数，导航回到“函数”页面 。 
1. 确认你在“监视”页面上。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="函数调用前函数的“监视”页面":::
    
    在 Web 浏览器的标签页中将此页面保持在打开状态。 稍后你将刷新此页面来查看对该函数的调用。

## <a name="connect-the-function-and-namespace-via-event-grid"></a>通过事件网格连接函数和命名空间
在本部分，你要使用 Azure 门户将函数和服务总线命名空间绑定在一起。 

若要创建 Azure 事件网格订阅，请执行以下操作：

1. 在 Azure 门户中转到你的命名空间，然后在左窗格中选择“事件”。 此时会打开命名空间窗口，两个事件网格订阅显示在右窗格中。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="服务总线 - 事件页":::
2. 在工具栏上选择“+ 事件订阅”。 
3. 在“创建事件订阅”页中执行以下步骤：
    1. 输入订阅的 **名称**。 
    2. 输入系统主题的名称 。 系统主题是为 Azure 资源（如 Azure 存储帐户和 Azure 服务总线）创建的主题。 若要详细了解系统主题，请参阅[系统主题概述](../event-grid/system-topics.md)。
    2. 选择“Azure Function”作为终结点类型，然后单击“选择终结点”  。 

        ![服务总线 - 事件网格订阅](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. 在“选择 Azure 函数”页面上，选择订阅、资源组、函数应用、槽和函数，然后选择“确认选择” 。 

        ![函数 - 选择终结点](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. 在“创建事件订阅”页面上，切换到“筛选器”选项卡，并执行以下任务 ：
        1. 选择“启用主题筛选”
        2. 输入先前创建的服务总线主题 (S1) 的订阅的名称。
        3. 选择“创建”按钮。 

            ![事件订阅筛选器](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. 切换到“事件”页面的“事件订阅”选项卡，并确认列表中显示了事件订阅 。

    ![列表中的事件订阅](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>监视函数应用
先前发送到服务总线主题的消息将转发到订阅 (S1)。 事件网格将订阅时的消息转发到 Azure 函数。 在本教程的此步骤中，你将确认已调用该函数并查看记录的信息性消息。 

1. 在 Azure 函数应用页面上，切换到“监视”选项卡（如果它尚未激活）。 应会看到发布到服务总线主题的每个消息的条目。 如果没有看到这些条目，请在等待几分钟后刷新页面。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="调用后函数的“监视”页面":::
2. 从列表中选择调用以查看详细信息。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="函数调用详细信息" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    还可以使用“监视”页的“日志”选项卡在发送消息时查看日志信息 。 可能会有一些延迟，因此请在几分钟后查看记录的消息。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="函数日志" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>疑难解答
如果在等待和刷新一段时间后没有看到任何函数调用，请执行以下步骤： 

1. 确认消息已到达服务总线主题。 查看“服务总线主题”页面上的“传入消息”计数器 。 在本例中，我运行了两次 MessageSender 应用程序，因此我会看到 10 条消息（每次运行 5 条消息）。

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="服务总线主题页面 - 传入消息":::    
1. 确认服务总线订阅中有“无活动消息”。 
    如果在此页面上未看到任何事件，请验证“服务总线订阅”页面没有显示任何“活动消息计数” 。 如果此计数器的数字大于零，则由于某些原因，订阅中的消息没有转发到处理程序函数（事件订阅处理程序）。 请验证是否已正确设置事件订阅。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="服务总线订阅的活动消息计数":::    
1. 你还可在服务总线命名空间的“事件”页面上查看发送的事件数 。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="事件页面 - 发送的事件数" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. 还可在“事件订阅”页面上看到事件已发送。 可在“事件”页面上选择事件订阅来访问此页面。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="事件订阅页面 - 发送的事件数":::
    


## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 事件网格](../event-grid/index.yml)。
* 详细了解 [Azure Functions](../azure-functions/index.yml)。
* 详细了解 [Azure 应用服务的逻辑应用功能](../logic-apps/index.yml)。
* 详细了解 [Azure 服务总线](/azure/service-bus/)。


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png