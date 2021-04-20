---
title: 管理终结点和路由（API 和 CLI）
titleSuffix: Azure Digital Twins
description: 了解如何为 Azure 数字孪生数据设置和管理终结点和事件路由。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e6b35031d976a11bdac6f38d74f9e02a0fc83302
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936302"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>在 Azure 数字孪生（API 和 CLI）中管理终结点和路由

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

在 Azure 数字孪生中，可将[事件通知](how-to-interpret-event-data.md)路由到下游服务或连接的计算资源。 首先需要设置可接收事件的终结点。 然后，可创建[事件路由](concepts-route-events.md)来指定由 Azure 数字孪生生成的哪些事件传递到哪些终结点。

本文将介绍使用 [REST API](/rest/api/azure-digitaltwins/)、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) 和 [Azure 数字孪生 CLI](how-to-use-cli.md) 创建终结点和路由的过程。

此外，还可以使用 [Azure 门户](https://portal.azure.com)管理终结点和路由。 有关本文另一个改用门户的版本，请参阅[如何：管理终结点和路由（门户）](how-to-manage-routes-portal.md)。

## <a name="prerequisites"></a>先决条件

- 你需要一个 Azure 帐户（你可以在[此处](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免费设置一个）
- 在 Azure 订阅中需要一个“Azure 数字孪生实例”。 如果还没有实例，可以使用[如何：设置实例和身份验证](how-to-set-up-instance-cli.md)中的步骤创建一个实例。 准备好以下设置值，以便稍后在本文中使用：
    - 实例名称
    - 资源组

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>为 Azure 数字孪生创建终结点

以下是可为实例创建的受支持的终结点类型：
* [事件网格](../event-grid/overview.md)
* [事件中心](../event-hubs/event-hubs-about.md)
* [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)

有关不同终结点类型的更多信息，请参阅[在 Azure 消息服务之间进行选择](../event-grid/compare-messaging-services.md)。

本部分介绍如何使用 Azure CLI 创建这些终结点。 还可以使用 [DigitalTwinsEndpoint 控制平面 API](/rest/api/digital-twins/controlplane/endpoints) 管理终结点。

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>创建终结点

创建终结点资源后，就可以将其用于 Azure 数字孪生终结点。 以下示例演示如何使用 [Azure 数字孪生 CLI](how-to-use-cli.md) 的 [az dt endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create) 命令创建终结点。 将命令中的占位符替换为你自己的资源的详细信息。

创建事件网格终结点：

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

创建事件中心终结点（基于密钥的身份验证）：
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

创建服务总线主题终结点（基于密钥的身份验证）：
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

成功运行这些命令后，事件网格、事件中心或服务总线主题将作为 Azure 数字孪生内的终结点提供，以你通过 `--endpoint-name` 参数所提供的名称命名。 通常使用该名称作为事件路由（将在[本文的后面部分](#create-an-event-route)创建）的目标。

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>创建使用基于身份的身份验证的终结点

还可以创建具有基于身份的身份验证的终结点，以使用具有[托管标识](concepts-security.md#managed-identity-for-accessing-other-resources-preview)的终结点。 此选项仅适用于事件中心和服务总线类型终结点（事件网格不支持此选项）。

创建这种类型的终结点的 CLI 命令如下所示。 需要将以下值插入命令中的占位符：
* Azure 数字孪生实例的 Azure 资源 ID
* 终结点名称
* 终结点类型
* 终结点资源的命名空间
* 事件中心或服务总线主题的名称
* Azure 数字孪生实例的位置

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>创建使用死信的终结点

当终结点无法在特定时间段内传递事件，或在尝试传递事件一定次数后仍无法传递，它可将未传递的事件发送到存储帐户。 此过程称为“死信处理”。

启用了死信的终结点可以用 Azure 数字孪生 [CLI](how-to-use-cli.md) 或[控制平面 API](how-to-use-apis-sdks.md#overview-control-plane-apis) 进行设置。

若要了解有关死信的更多信息，请参阅[概念：事件路由](concepts-route-events.md#dead-letter-events)。 有关如何设置使用死信的终结点的说明，请继续阅读本部分的其余内容。

#### <a name="set-up-storage-resources"></a>设置存储资源

在设置死信位置之前，必须在 Azure 帐户中设置一个带有[容器](../storage/common/storage-account-create.md?tabs=azure-portal)的[存储帐户](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)。 

稍后创建终结点时，需要提供此容器的 URI。 死信位置将作为带有 [SAS 令牌](../storage/common/storage-sas-overview.md)的容器 URI 提供给终结点。 该令牌需要存储帐户中目标容器的 `write` 权限。 完全格式化的死信 SAS URI 的格式为：`https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>`。

按照以下步骤在 Azure 帐户中设置这些存储资源，为在下一部分中设置终结点连接做准备。

1. 按照[创建存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)中的步骤在 Azure 订阅中创建存储帐户。 记下存储帐户名以便以后使用。
2. 按照[创建容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)中的步骤在新存储帐户内创建容器。 记下容器名称以便以后使用。
3. 接下来，为存储帐户创建一个 SAS 令牌，终结点可以使用该令牌来访问它。 首先导航到 [Azure 门户](https://ms.portal.azure.com/#home)中的存储帐户（可以通过门户搜索栏按名称找到它）。
4. 在存储帐户页面中，选择左侧导航栏中的“共享访问签名”链接以开始设置 SAS 令牌。

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Azure 门户中的存储帐户页" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. 在“共享访问签名”页的“允许的服务”和“允许的资源类型”下，选择所需的任何设置  。 需要在每个类别中至少选择一个框。 在“允许的权限”下，选择“写入”（如果需要，还可以选择其他权限）。
1. 对于其余的设置，设置所需的任何值。
1. 完成后，选择“生成 SAS 和连接字符串”按钮来生成 SAS 令牌。 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Azure 门户中的存储帐户页面，显示生成 SAS 令牌的所有设置选择，并突出显示“生成 SAS 和连接字符串”按钮" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. 这将在同一个页面底部的各设置选项下生成几个 SAS 和连接字符串值。 向下滚动以查看这些值，并使用“复制到剪贴板”图标复制“SAS 令牌”值。 保存它以供以后使用。

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="复制 SAS 令牌以用于死信机密。" lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>创建死信终结点

若要创建启用死信的终结点，请将以下死信参数添加到 [Azure 数字孪生 CLI](how-to-use-cli.md) 的 [az dt endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create) 命令中。

参数的值是“死信 SAS URI”，由你在[上一部分](#set-up-storage-resources)中收集的存储帐户名、容器名和 SAS 令牌组成。 此参数创建使用基于密钥的身份验证的终结点。

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

将此参数添加到前面的[创建终结点](#create-the-endpoint)部分中的终结点创建命令的末尾，以创建启用了死信的所需类型的终结点。

或者，可以使用 [Azure 数字孪生控制平面 API](how-to-use-apis-sdks.md#overview-control-plane-apis) 而不是 CLI 创建死信终结点。 为此，请查看 [DigitalTwinsEndpoint 文档](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)，了解如何构造请求并添加死信参数。

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>创建使用基于身份的身份验证的死信终结点

还可以创建具有基于身份的身份验证的死信终结点，以使用具有[托管标识](concepts-security.md#managed-identity-for-accessing-other-resources-preview)的终结点。 此选项仅适用于事件中心和服务总线类型终结点（事件网格不支持此选项）。

若要创建这种类型的终结点，请使用与前面相同的 CLI 命令[创建使用基于身份的身份验证的终结点](#create-an-endpoint-with-identity-based-authentication)，并在 JSON 有效负载中为 `deadLetterUri` 添加一个额外字段。

以下是需要插入命令占位符的值：
* Azure 数字孪生实例的 Azure 资源 ID
* 终结点名称
* 终结点类型
* 终结点资源的命名空间
* 事件中心或服务总线主题的名称
* “死信 SAS URI”详细信息：存储帐户名、容器名
* Azure 数字孪生实例的位置

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>消息存储架构

设置了使用死信的终结点后，经过死信处理的消息将采用以下格式存储在存储帐户中：

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

经过死信处理的消息将与打算传递到原始终结点的原始事件的架构匹配。

下面是[孪生体创建通知](how-to-interpret-event-data.md#digital-twin-lifecycle-notifications)的死信消息示例：

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>创建事件路由

先决条件：在继续创建路由之前，需要按照本文前面所述创建终结点。 完成终结点设置后，可以继续创建事件路由。

> [!NOTE]
> 如果最近部署了终结点，在尝试将它们用于新的事件路由之前，请验证它们是否已完成部署。 如果由于终结点未就绪而导致路由部署失败，请等待几分钟，然后重试。
>
> 如果要编写此流的脚本，你可能希望为终结点服务完成部署留出 2-3 分钟的等待时间，然后再继续进行路由设置，从而解决此问题。

若要实际地将数据从 Azure 数字孪生发送到终结点，需要定义一个事件路由。 事件路由用于将事件流连接到整个系统并连接到下游服务。

路由定义可包含以下元素：
* 要使用的路由名称
* 要使用的终结点名称
* 用于定义发送到终结点的事件的筛选器 

如果没有路由名称，则不会在 Azure 数字孪生之外路由任何消息。 如果存在路由名称且筛选器为 `true`，则所有消息都将路由到终结点。 如果存在路由名称并添加了不同的筛选器，则将根据筛选器筛选消息。

一个路由应允许选择多个通知和事件类型。 

可以使用 Azure 数字孪生 [EventRoutes 数据平面 API](/rest/api/digital-twins/dataplane/eventroutes) 或 [CLI 命令 az dt route](/cli/azure/ext/azure-iot/dt/route) 来创建事件路由 。 本部分的其余内容将介绍创建过程。

### <a name="create-routes-with-the-apis-and-c-sdk"></a>使用 API 和 C# SDK 创建路由

定义事件路由的一种方法是使用[数据平面 API](how-to-use-apis-sdks.md#overview-data-plane-apis)。 本部分中的示例使用 [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)。

`CreateOrReplaceEventRouteAsync` 是用于添加事件路由的 SDK 调用。 下面是它的用法示例：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> 所有 SDK 函数都提供同步和异步版本。

#### <a name="event-route-sample-sdk-code"></a>事件路由示例 SDK 代码

下面的示例方法演示如何使用 C# SDK 创建、列出和删除事件路由：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>使用 CLI 创建路由

还可以使用 Azure 数字孪生 CLI 的 [az dt route](/cli/azure/ext/azure-iot/dt/route) 命令来管理路由。 

有关使用 CLI 和可用命令的更多信息，请参阅[如何：使用 Azure 数字孪生 CLI](how-to-use-cli.md)。

## <a name="filter-events"></a>筛选事件

如果不进行筛选，终结点将从 Azure 数字孪生接收各种事件：
* 遥测，由[数字孪生](concepts-twins-graph.md)使用 Azure 数字孪生服务 API 触发
* 孪生属性更改通知，在 Azure 数字孪生实例中的任何孪生属性更改时触发
* 生命周期事件，在创建或删除孪生体或关系时触发

可以通过向事件路由中添加一个终结点筛选器来限制要发送的事件。

>[!NOTE]
> 筛选器区分大小写，并需要与有效负载大小写匹配。 
>
> 对于遥测筛选器，这意味着大小写需要与设备发送的遥测的大小写匹配，而不一定是在孪生体的模型中定义的大小写。 

若要添加筛选器，可以针对 https://{Your-azure-digital-twins-hostname}/eventRoutes/{event-route-name}?api-version=2020-10-31 使用一个 PUT 请求，正文如下：

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

下面是受支持的路由筛选器。 使用“筛选器文本架构”列中的详细信息替换上面请求正文中的 `<filter-text>` 占位符。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>后续步骤

阅读可接收的不同类型的事件消息：
* [如何：解释事件数据](how-to-interpret-event-data.md)
