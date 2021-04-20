---
title: 与 Azure 时序见解集成
titleSuffix: Azure Digital Twins
description: 了解如何设置从 Azure 数字孪生到 Azure 时序见解的事件路由。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 608f883304dbc8e1ea8b0127668125ae50ca0b11
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564936"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>将 Azure 数字孪生与 Azure 时序见解相集成

本文介绍如何将 Azure 数字孪生与 [Azure 时序见解 (TSI)](../time-series-insights/overview-what-is-tsi.md) 相集成。

使用本文中所述的解决方案可以收集和分析有关你的 IoT 解决方案的历史数据。 Azure 数字孪生非常适合用于将数据馈送到时序见解中，因为它支持关联多个数据流，并可在将信息发送到时序见解之前将信息标准化。 

## <a name="prerequisites"></a>先决条件

在设置与时序见解的关系之前，需有一个 **Azure 数字孪生实例**。 所设置的这个实例应该能够基于数据更新数字孪生信息，因为你需要更新孪生信息多次以查看时序见解中跟踪的数据。 

如果尚未设置此实例，可以按照 Azure 数字孪生[教程：连接端到端解决方案](./tutorial-end-to-end.md)创建一个实例。 本教程将逐步介绍如何设置一个与虚拟 IoT 设备配合工作的、用于触发数字孪生更新的 Azure 数字孪生实例。

## <a name="solution-architecture"></a>解决方案体系结构

你将通过以下路径将时序见解附加到 Azure 数字孪生。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="端到端方案中的 Azure 服务视图，其中突出显示了“时序见解”" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>对孪生体更新通知创建路由和筛选器

每当孪生的状态更新后，Azure 数字孪生实例都可以发出[孪生更新事件](how-to-interpret-event-data.md)。 在本部分，你将创建一个 Azure 数字孪生 [**事件路由**](concepts-route-events.md)，用于将这些更新事件定向到 Azure [事件中心](../event-hubs/event-hubs-about.md)以供进一步处理。

Azure 数字孪生[教程：连接端到端解决方案](./tutorial-end-to-end.md)演练了以下方案：使用一个温度计来更新代表房间的数字孪生上的温度特性。 此模式依赖于孪生更新，而不是转发来自 IoT 设备的遥测数据，这样，你就可以灵活地更改底层数据源，且无需更新时序见解逻辑。

1. 首先，创建一个可从 Azure 数字孪生实例接收事件的事件中心命名空间。 可以参考以下 Azure CLI 说明，也可以使用 Azure 门户：[快速入门：使用 Azure 门户创建事件中心](../event-hubs/event-hubs-create.md)。 若要查看哪些区域支持事件中心，请访问[可用的 Azure 产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)。

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. 在命名空间中创建一个事件中心用于接收孪生更改事件。 指定事件中心的名称。

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. 创建具有发送和接收权限的[授权规则](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create)。 指定规则的名称。

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. 创建一个 Azure 数字孪生[终结点](concepts-route-events.md#create-an-endpoint)，用于将事件中心链接到 Azure 数字孪生实例。

    ```azurecli-interactive
    az dt endpoint create eventhub -n <your Azure Digital Twins instance name> --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above>
    ```

5. 在 Azure 数字孪生中创建[路由](concepts-route-events.md#create-an-event-route)，将孪生体更新事件发送到终结点。 此路由中的筛选器只允许将孪生更新消息传递到你的终结点。

    >[!NOTE]
    >目前，Cloud Shell 中存在一个已知问题，该问题会影响以下命令组：`az dt route`、`az dt model` 和 `az dt twin`。
    >
    >若要解决此问题，请在运行命令之前在 Cloud Shell 中运行 `az login`，或者使用[本地 CLI](/cli/azure/install-azure-cli) 而不使用 Cloud Shell。 有关此操作的详细信息，请参阅 [*故障排除：Azure 数字孪生中的已知问题*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)。

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

在继续操作之前，请记下你的事件中心命名空间和资源组，因为稍后会在本文中再次使用它们来创建另一个事件中心。 

## <a name="create-a-function-in-azure"></a>在 Azure 中创建一个函数

接下来，你将使用 Azure Functions 在函数应用中创建一个 **事件中心触发的函数**。 可以使用端到端教程（[教程：连接端到端解决方案](./tutorial-end-to-end.md)）中创建的函数应用，或使用你自己的函数应用。 

此函数会将这些孪生更新事件从它们原始的 JSON 补丁文档形式转换为 JSON 对象，其中仅包含来自孪生的已更新值和添加的值。

有关将事件中心与 Azure Functions 配合使用的详细信息，请参阅[适用于 Azure Functions 的 Azure 事件中心触发器](../azure-functions/functions-bindings-event-hubs-trigger.md)。

在发布的函数应用中，使用以下代码添加名为 **ProcessDTUpdatetoTSI** 的新函数。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

>[!NOTE]
>可能需要使用 `dotnet add package` 命令或 Visual Studio NuGet 包管理器将包添加到项目。

接下来，**发布** 新的 Azure 函数。 有关如何执行此操作的说明，请参阅[操作指南：设置用于处理数据的 Azure 函数](how-to-create-azure-function.md#publish-the-function-app-to-azure)。

到时，此函数会将它创建的 JSON 对象发送至要连接到时序见解的另一个事件中心。 你将在下一部分创建该事件中心。

稍后，你还要设置一些环境变量，供此函数用来连接到你自己的事件中心。

## <a name="send-telemetry-to-an-event-hub"></a>将遥测数据发送到事件中心

现在，你要创建另一个事件中心，并将你的函数配置为将其输出流式传输到该事件中心。 此事件中心随后会连接到时序见解。

### <a name="create-an-event-hub"></a>创建事件中心

若要创建第二个事件中心，可以参考以下 Azure CLI 说明，也可以使用 Azure 门户：[快速入门：使用 Azure 门户创建事件中心](../event-hubs/event-hubs-create.md)。

1. 准备好在本文前面部分记下的事件中心命名空间和资源组名称 

2. 创建新的事件中心。 指定事件中心的名称。

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. 创建具有发送和接收权限的[授权规则](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create)。 指定规则的名称。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>配置函数

接下来，需要在前面创建的函数应用中设置环境变量，并在其中包含已创建的事件中心的连接字符串。

### <a name="set-the-twins-event-hub-connection-string"></a>设置孪生体事件中心连接字符串

1. 使用前面为孪生中心创建的授权规则获取孪生[事件中心连接字符串](../event-hubs/event-hubs-get-connection-string.md)。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. 使用结果中的 *primaryConnectionString* 值在函数应用中创建包含该连接字符串的应用设置：

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>设置时序见解事件中心连接字符串

1. 使用前面为时序见解中心创建的授权规则获取 TSI [事件中心连接字符串](../event-hubs/event-hubs-get-connection-string.md)：

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. 使用结果中的 *primaryConnectionString* 值在函数应用中创建包含该连接字符串的应用设置：

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>创建和连接时序见解实例

接下来，设置一个时序见解实例，以用于接收来自第二个 (TSI) 事件中心的数据。 请按照以下步骤操作，有关此过程的更多详细信息，请参阅[教程：设置 Azure 时序见解 Gen2 PAYG 环境](../time-series-insights/tutorial-set-up-environment.md)。

1. 在 Azure 门户中，开始创建时序见解环境。 
    1. 选择“Gen2(L1)”定价层。
    2. 需要为此环境选择一个 **时序 ID**。 时序 ID 最多可以是要用来在时序见解中搜索数据的三个值。 在本教程中，可以使用 **$dtId**。 在[有关选择时序 ID 值的最佳做法](../time-series-insights/how-to-select-tsid.md)中详细了解如何选择 ID 值。
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="时序见解环境的“创建”门户 UX。从相应的下拉列表中选择你的订阅、资源组和位置，然后为环境选择一个名称。" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="时序见解环境的“创建”门户 UX。其中已选择“Gen2(L1)”定价层，时序 ID 属性名称为 $dtId" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. 选择“下一步: 事件源”，然后选择前面提供的 TSI 事件中心信息。 此外，还需要创建一个新的事件中心使用者组。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="时序见解环境事件源的“创建”门户 UX。你将使用前面提供的事件中心信息创建一个事件源。你还将创建一个新的使用者组。" lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>开始将 IoT 数据发送到 Azure 数字孪生

若要开始将数据发送到时序见解，需要开始更新 Azure 数字孪生中的数字孪生属性并更改数据值。 使用 [az dt twin update](/cli/azure/ext/azure-iot/dt/twin#ext-azure-iot-az-dt-twin-update) 命令。

如果使用端到端教程（[教程：连接端到端解决方案](tutorial-end-to-end.md)）来帮助进行环境设置，则可以通过运行示例中的 *DeviceSimulator* 项目开始发送模拟的 IoT 数据。 该教程的[配置并运行模拟](tutorial-end-to-end.md#configure-and-run-the-simulation)部分提供了相关说明。

## <a name="visualize-your-data-in-time-series-insights"></a>在时序见解中可视化数据

数据现在应流向时序见解实例并且可供分析。 按照以下步骤浏览传入的数据。

1. 在 [Azure 门户](https://portal.azure.com)中打开你的时序见解环境（可以在门户搜索栏中搜索你的环境名称）。 访问实例概述中所示的“时序见解资源管理器 URL”。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="在时序见解环境的概述选项卡中选择时序见解资源管理器 URL":::

2. 在资源管理器的左侧，你会看到显示了 Azure 数字孪生的三个孪生。 依次选择“thermostat67”、“temperature”，然后点击“添加”。 

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="依次选择“thermostat67”、“temperature”，然后点击“添加”":::

3. 现在应会看到温度计的初始温度读数，如下所示。 *room21* 和 *floor1* 的温度读数也已更新为相同的值，并且你可以将这些数据流一起可视化。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="TSI 资源管理器中绘制了初始温度数据图形。它们是一系列介于 68 到 85 之间的随机值":::

4. 如果运行模拟很长一段时间，则可视化效果将如下所示：
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="将以三条不同颜色的平行线绘制每个孪生的温度数据图形。":::

## <a name="next-steps"></a>后续步骤

默认情况下，数字孪生以平面层次结构的形式存储在时序见解中，但可以使用模型信息和多级别组织层次结构对其进行扩充。 若要详细了解此过程，请阅读： 

* [教程：定义并应用模型](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

你可以编写自定义逻辑，以使用已存储在 Azure 数字孪生中的模型和图数据自动提供此信息。 若要详细了解如何管理、升级和检索孪生图中的信息，请参阅以下参考资料：

* [操作指南：管理数字孪生](./how-to-manage-twin.md)
* [操作指南：查询孪生图](./how-to-query-graph.md)