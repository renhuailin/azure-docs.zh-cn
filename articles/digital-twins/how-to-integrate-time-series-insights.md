---
title: 与 Azure 时序见解集成
titleSuffix: Azure Digital Twins
description: 了解如何设置从 Azure 数字孪生到 Azure 时序见解的事件路由。
author: baanders
ms.author: baanders
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3dc79c2307365a81c6994a1e692f86ec1faa0511
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722343"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>将 Azure 数字孪生与 Azure 时序见解相集成

本文介绍如何将 Azure 数字孪生与 [Azure 时序见解 (TSI)](../time-series-insights/overview-what-is-tsi.md) 相集成。

使用本文中所述的解决方案可以收集和分析有关你的 IoT 解决方案的历史数据。 Azure 数字孪生非常适合用于将数据馈送到时序见解中，因为它支持关联多个数据流，并可在将信息发送到时序见解之前将信息标准化。

## <a name="prerequisites"></a>先决条件

需要设置以下资源，才能设置与时序见解的关系：
* Azure 数字孪生实例。 有关说明，请参阅[设置 Azure 数字孪生实例和身份验证](./how-to-set-up-instance-portal.md)。
* Azure 数字孪生实例中的模型和孪生体。 需要多次更新孪生体信息，才能看到在时序见解中跟踪的数据。 有关说明，请参阅《从 IoT 中心引入遥测》一文中的[添加模型和孪生体](how-to-ingest-iot-hub-data.md#add-a-model-and-twin)部分。

> [!TIP]
> 在本文中，为简单起见，手动更新在时序见解中查看的不断更改的数字孪生体值。 但是，如果要使用实时模拟数据完成本文，可以设置一个 Azure 函数，用于根据来自模拟设备的 IoT 遥测事件更新数字孪生体。 有关说明，请遵循[引入 IoT 中心数据](how-to-ingest-iot-hub-data.md)操作，包括运行设备模拟器并验证数据流是否正常工作的最终步骤。
>
> 稍后，查找另一个提示以显示开始运行设备模拟器的位置，并让 Azure Functions 自动更新孪生体，而不是发送手动数字孪生体更新命令。


## <a name="solution-architecture"></a>解决方案体系结构

你将通过以下路径将时序见解附加到 Azure 数字孪生。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="端到端方案中的 Azure 服务图，其中突出显示了“时序见解”。" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>创建事件中心命名空间

创建事件中心之前，请先创建一个可从 Azure 数字孪生实例接收事件的事件中心命名空间。 可以参考以下 Azure CLI 说明，也可以按照[使用 Azure 门户创建事件中心](../event-hubs/event-hubs-create.md)使用 Azure 门户。 若要查看哪些区域支持事件中心，请访问可用的 Azure 产品（按区域）。

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-Event-Hubs-namespace> --resource-group <your-resource-group> --location <region>
```

> [!TIP]
> 如果收到错误消息，其中声明 `BadRequest: The specified service namespace is invalid.`，请确保为命名空间选择的名称满足此参考文档中所述的命名要求：[创建命名空间](/rest/api/servicebus/create-namespace)。

你会使用此事件中心命名空间来容纳本文所需的两个事件中心：

  1. 孪生体中心 - 用于接收孪生体更改事件的事件中心
  2. 时序中心 - 用于将事件流式传输到时序见解的事件中心

后续各部分会指导你在事件中心命名空间中创建和配置这些中心。

## <a name="create-twins-hub"></a>创建孪生体中心

你在本文中创建的第一个事件中心是孪生体中心。 此事件中心会从 Azure 数字孪生接收孪生体更改事件。
若要设置孪生体中心，请完成本部分中的以下步骤：

1. 创建孪生体中心
2. 创建一个授权规则以控制对中心的权限
3. 在 Azure 数字孪生中创建一个终结点，它使用授权规则访问中心
4. 在 Azure 数字孪生中创建一个路由，它将孪生体更新事件发送到终结点和所连接的孪生体中心
5. 获取孪生体中心连接字符串

使用以下 CLI 命令创建孪生体中心。 为孪生体中心指定名称。

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>创建孪生体中心授权规则

创建具有发送和接收权限的[授权规则](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az_eventhubs_eventhub_authorization_rule_create)。 指定规则的名称。

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>创建孪生体中心终结点

创建一个 Azure 数字孪生[终结点](concepts-route-events.md#create-an-endpoint)，用于将事件中心链接到 Azure 数字孪生实例。 为孪生体中心终结点指定名称。

```azurecli-interactive
az dt endpoint create eventhub --dt-name <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-Event-Hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>创建孪生体中心事件路由

每当孪生的状态更新后，Azure 数字孪生实例都可以发出[孪生更新事件](./concepts-event-notifications.md)。 在本部分，你将创建一个 Azure 数字孪生事件路由，它会将这些更新事件定向到孪生体中心以供进一步处理。

在 Azure 数字孪生中创建[路由](concepts-route-events.md#create-an-event-route)，将孪生体更新事件发送到前面的终结点。 此路由中的筛选器只允许将孪生更新消息传递到你的终结点。 为孪生体中心事件路由指定名称。

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>获取孪生体中心连接字符串

使用前面为孪生体中心创建的授权规则获取[孪生体事件中心连接字符串](../event-hubs/event-hubs-get-connection-string.md)。

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-Event-Hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
记下结果中的 primaryConnectionString 值，以在本文后面配置孪生体中心应用设置。

## <a name="create-time-series-hub"></a>创建时序中心

你在本文中创建的第二个事件中心是时序中心。 这是一个事件中心，会将 Azure 数字孪生事件流式传输到时序见解。
若要设置时序中心，请完成以下步骤：

1. 创建时序中心
2. 创建一个授权规则以控制对中心的权限
3. 获取时序中心连接字符串

以后，当你创建时序见解实例时，会将此时序中心作为时序见解实例的事件源进行连接。

使用以下命令创建时序中心。 为时序中心指定名称。

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>创建时序中心授权规则

创建具有发送和接收权限的[授权规则](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az_eventhubs_eventhub_authorization_rule_create)。 为时序中心授权规则指定名称。

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>获取时序中心连接字符串

使用前面为时序见解中心创建的授权规则获取[时序中心连接字符串](../event-hubs/event-hubs-get-connection-string.md)：

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-Event-Hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
记下结果中的 primaryConnectionString 值，以在本文后面配置时序中心应用设置。

另外请记下以下值，以便以后使用它们创建时序见解实例。
* 事件中心命名空间
* 时序中心名称
* 时序中心授权规则

## <a name="create-a-function"></a>创建函数

在本部分，你会创建一个 Azure 函数，它会将孪生体更新事件从其原始的 JSON 补丁文档形式转换为 JSON 对象，其中仅包含来自孪生体的已更新值和添加的值。

1. 首先，在 Visual Studio 中创建新的函数应用项目。 有关如何执行此操作的说明，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project)。

2. 创建名为 ProcessDTUpdatetoTSI.cs 的新 Azure 函数，以将设备遥测事件更新到时序见解。 函数类型是“事件中心触发器”。

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="创建类型为事件中心触发器的新 Azure 函数的 Visual Studio 的屏幕截图。":::

3. 将以下包添加到项目：
    * [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
    * [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
    * [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

4. 将 ProcessDTUpdatetoTSI.cs 文件中的代码替换为以下代码：

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

    保存函数代码。

5. 将包含 ProcessDTUpdatetoTSI.cs 函数的项目发布到 Azure 中的函数应用。 有关如何执行此操作的说明，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#publish-to-azure)。

保存函数应用名称，以便以后用于为两个事件中心配置应用设置。

### <a name="configure-the-function-app"></a>配置函数应用

接下来，为函数分配访问角色并配置应用程序设置，使之能够访问你的资源 。

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

接下来在函数应用的设置中添加环境变量，使它可以访问孪生体中心和时序中心 。

使用先前保存的孪生体中心 primaryConnectionString 值在函数应用中创建包含孪生体中心连接字符串的应用设置：

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" --resource-group <your-resource-group> --name <your-function-app-name>
```

使用先前保存的时序中心 primaryConnectionString 值在函数应用中创建包含时序中心连接字符串的应用设置：

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" --resource-group <your-resource-group> --name <your-function-app-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>创建和连接时序见解实例

在本部分中，你会设置时序见解实例以从时序中心接收数据。 有关此过程的更多详细信息，请参阅[设置 Azure 时序见解 Gen2 PAYG 环境](../time-series-insights/tutorial-set-up-environment.md)。 按照以下步骤创建时序见解环境。

1. 在 [Azure 门户](https://portal.azure.com)中，搜索“时序见解环境”，然后选择“创建”按钮。 选择以下选项以创建时序环境。

    * 订阅 - 选择订阅。
        - 资源组 - 选择资源组。
    * 环境名称 - 为时序环境指定名称。
    * 位置 - 选择位置。
    * 层 - 选择“Gen2(L1)”定价层。
    * 属性名称 - 输入“$dtId”（在选择时序 ID 的最佳做法中详细了解如何选择 ID 值）。
    * 存储帐户名称 - 指定存储帐户名称。
    * 启用热存储 - 将此字段设置为“是”。

    可以在此页面上保留其他属性的默认值。 选择“下一步: 事件源 >”按钮。

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Azure 门户的屏幕截图，显示了如何创建时序见解环境（第 1/3 部分）。" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Azure 门户的屏幕截图，显示了如何创建时序见解环境（第 2/3 部分）。" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. 在“事件源”选项卡上，选择以下字段：

   * **创建事件源?** - 选择“是”。
   * 源类型 - 选择“事件中心”。
   * 名称 - 为事件源指定名称。
   * 订阅 - 选择自己的 Azure 订阅。
   * 事件中心命名空间 - 选择在本文前面部分创建的命名空间。
   * 事件中心名称 - 选择在本文前面部分创建的时序中心名称。
   * 事件中心访问策略名称 - 选择在本文前面部分创建的时序中心授权规则。
   * 事件中心使用者组 - 选择“新建”，并为事件中心使用者组指定名称。 然后选择“添加”。
   * 属性名称将此字段留空。
    
    选择“查看 + 创建”按钮以查看所有详细信息。 然后，再次选择“查看 + 创建”按钮以创建时序环境。

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Azure 门户的屏幕截图，显示了如何创建时序见解环境（第 3/3 部分）。" lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>将 IoT 数据发送到 Azure 数字孪生

若要开始将数据发送到时序见解，需要开始更新 Azure 数字孪生中的数字孪生体属性并更改数据值。

使用 [az dt twin update](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update) CLI 命令更新在[先决条件](#prerequisites)部分中添加的孪生体的属性。 如果使用了[从 IoT 中心引入遥测数据](how-to-ingest-iot-hub-data.md)的孪生体创建说明，则可以在本地 CLI 或 Cloud Shell bash 终端中使用以下命令更新 thermostat67 孪生体上的温度属性。

```azurecli-interactive
az dt twin update --dt-name <your-Azure-Digital-Twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

使用不同属性值再重复该命令至少 4 次，以创建稍后可在时序见解环境中观察到的多个数据点。

> [!TIP]
> 如果要使用实时模拟数据完成本文，而不是手动更新数字孪生体值，请先确保已完成[先决条件](#prerequisites)部分中的提示，设置从模拟设备更新孪生体的 Azure 函数。
在此之后，可以立即运行设备，以开始发送模拟数据并通过该数据流更新数字孪生体。

## <a name="visualize-your-data-in-time-series-insights"></a>在时序见解中可视化数据

数据现在应流向时序见解实例并且可供分析。 按照以下步骤浏览传入的数据。

1. 在 [Azure 门户](https://portal.azure.com)中，搜索先前创建的时序环境名称。 在左侧菜单选项中，选择“概述”以查看“时序见解资源管理器 URL” 。 选择 URL 以查看在时序见解环境中反映的温度变化。

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Azure 门户的屏幕截图，其中在时序见解环境的概述选项卡中显示时序见解资源管理器 URL。" lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. 在资源管理器的左侧，你会看到显示了 Azure 数字孪生实例中的孪生体。 选择已为其编辑属性的孪生体，选择已更改的属性，然后选择“添加”。

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="时序见解资源管理器的屏幕截图，突出显示了选择 thermostat67、选择属性 temperature 和选择“添加”的步骤。" lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. 现在你应该看到所做的属性更改反映在图形中，如下所示。 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="带有初始温度数据的时序见解资源管理器的屏幕截图，显示了一系列介于 68 到 85 之间的随机值。" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

如果运行模拟很长一段时间，则可视化效果将如下所示：

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="时序见解资源管理器的屏幕截图，其中以三条不同颜色的平行线绘制每个孪生体的温度数据。" lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>后续步骤

默认情况下，数字孪生以平面层次结构的形式存储在时序见解中，但可以使用模型信息和多级别组织层次结构对其进行扩充。 若要详细了解此过程，请阅读： 

* [定义并应用模型](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

你可以编写自定义逻辑，以使用已存储在 Azure 数字孪生中的模型和图数据自动提供此信息。 若要详细了解如何管理、升级和检索孪生图中的信息，请参阅以下参考资料：

* [管理数字孪生](./how-to-manage-twin.md)
* [查询孪生图](./how-to-query-graph.md)