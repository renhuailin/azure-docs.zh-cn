---
title: 事件路由
titleSuffix: Azure Digital Twins
description: 了解如何将 Azure 数字孪生中的事件路由到其他 Azure 服务。
author: baanders
ms.author: baanders
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6559de2b94879ac8643f4945fd5adcf4fc7e1045
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350817"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>在 Azure 数字孪生内部和外部路由事件

Azure 数字孪生使用 **事件路由** 将数据发送到服务外的使用者。 

发送 Azure 数字孪生数据的主要情况有两种：
* 将 Azure 数字孪生图中的一个单元中的数据发送到另一个。 例如，当一个数字上的属性发生变化时，你可能需要相应地通知和更新其他数字输出。
* 向下游数据服务发送数据以进行额外的存储或处理 (也称为 *数据传出*) 。 例如，
  - 医院可能想要将 Azure 数字孪生事件数据发送到 [时序见解 (TSI) ](../time-series-insights/overview-what-is-tsi.md)，为大容量分析记录 handwashing 相关事件的时序数据。
  - 已使用 [Azure Maps](../azure-maps/about-azure-maps.md) 的企业可能想要使用 Azure 数字孪生来增强其解决方案。 在设置 Azure 数字孪生后，它们可以快速启用 Azure 映射，将 Azure 地图实体作为单元中的 [数字孪生](concepts-twins-graph.md) 引入 Azure 数字孪生，或运行功能强大的查询，利用它们 Azure Maps 和 Azure 数字孪生数据。

事件路由用于这两种情况。

## <a name="about-event-routes"></a>关于事件路由

使用事件路由可以将 Azure 数字孪生中的数字孪生的事件数据发送到订阅中的自定义终结点。 目前有三个终结点支持的 Azure 服务： [事件中心](../event-hubs/event-hubs-about.md)、 [事件网格](../event-grid/overview.md)和 [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)。 每个 Azure 服务都可以连接到其他服务，并作为中转站，将数据发送到最终目标（Azure Maps 如

下图说明了通过 Azure 数字孪生方面的更大 IoT 解决方案的事件数据流：

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Azure 数字孪生通过终结点将数据路由到多个下游服务" border="false":::

事件路由的典型下游目标是诸如 TSI、Azure Maps、存储和分析解决方案等资源。

### <a name="event-routes-for-internal-digital-twin-events"></a>内部数字克隆事件的事件路由

事件路由还用于处理克隆图形中的事件，并将数据从数字双子发送到数字源。 这是通过将事件路由通过事件网格连接来计算资源（如 [Azure Functions](../azure-functions/functions-overview.md)）完成的。 然后，这些函数定义孪生应如何接收和响应事件。 

当计算资源基于通过事件路由收到的事件来修改非整数图形时，它可以知道它要提前修改的是哪一个。 

另外，事件消息还包含发送消息的源克隆的 ID，因此计算资源可以使用查询或遍历关系来查找所需操作的目标克隆。 

计算资源还需要单独建立安全和访问权限。

若要演练设置 Azure 函数以处理数字非整数事件的过程，请参阅 [*如何：设置用于处理数据的 azure 函数*](how-to-create-azure-function.md)。

## <a name="create-an-endpoint"></a>创建终结点

若要定义事件路由，开发人员必须首先定义终结点。 **终结点** 是支持路由连接的 Azure 数字孪生外部的目标。 支持的目标包括：
* 事件网格自定义主题
* 事件中心
* 服务总线

若要创建终结点，可以使用 Azure 数字孪生 [**控制平面 api**](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)、 [**CLI 命令**](how-to-manage-routes-apis-cli.md#manage-endpoints-and-routes-with-cli)或 [**Azure 门户**](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)。 

定义终结点时，需要提供：
* 终结点的名称
* 终结点类型 (事件网格、事件中心或服务总线) 
* 要进行身份验证的主连接字符串和辅助连接字符串 
* 终结点的主题路径，例如 *your-topic.westus2.eventgrid.azure.net*

控制平面中提供的终结点 Api 包括：
* 创建终结点
* 获取终结点列表
* 按名称获取终结点
* 按名称删除终结点

## <a name="create-an-event-route"></a>创建事件路由
 
若要创建事件路由，可以使用 Azure 数字孪生 [**数据平面 api**](how-to-manage-routes-apis-cli.md#create-an-event-route)、 [**CLI 命令**](how-to-manage-routes-apis-cli.md#manage-endpoints-and-routes-with-cli)或 [**Azure 门户**](how-to-manage-routes-portal.md#create-an-event-route)。 

下面是使用 `CreateOrReplaceEventRouteAsync` [.Net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 调用在客户端应用程序中创建事件路由的示例： 

```csharp
string eventFilter = "$eventType = 'DigitalTwinTelemetryMessages' or $eventType = 'DigitalTwinLifecycleNotification'";
var er = new DigitalTwinsEventRoute("endpointName", eventFilter);
await client.CreateOrReplaceEventRouteAsync("routeName", er);
```

1. 首先，将 `DigitalTwinsEventRoute` 创建一个对象，并且构造函数将使用终结点的名称。 此 `endpointName` 字段标识一个终结点，如事件中心、事件网格或服务总线。 在进行此注册调用之前，必须在订阅中创建这些终结点，并使用控制平面 Api 将这些终结点附加到 Azure 数字孪生。

2. 事件路由对象还具有一个 [**筛选器**](how-to-manage-routes-apis-cli.md#filter-events) 字段，该字段可用于限制跟随此路由的事件类型。 的筛选器将 `true` 启用无额外筛选的路由 (筛选器 `false` 禁用路由) 。 

3. 然后，将此事件路由对象传递到 `CreateOrReplaceEventRouteAsync` ，同时传递给路由的名称。

> [!TIP]
> 所有 SDK 函数都提供同步和异步版本。

还可以使用 [Azure 数字孪生 CLI](how-to-use-cli.md)创建路由。

## <a name="dead-letter-events"></a>死信事件

当终结点无法在某个时间段内传递事件时，或者尝试将事件传递到一定次数后，它可以将未送达的事件发送到存储帐户。 此过程称为“死信处理”。 当满足 **以下条件之一** 时，Azure 数字孪生会将事件死信。 

* 事件未在生存期内传递
* 尝试传递事件的次数已超出限制

如果满足上述任一条件，则会将该事件删除或视为死信。 默认情况下，每个终结点 **不会** 启用死信。 若要启用它，您必须指定一个存储帐户，以便在创建终结点时保存未传递的事件。 然后，你可以从此存储帐户拉取事件以解析传递。

在设置死信位置之前，必须有一个包含容器的存储帐户。 创建终结点时，提供此容器的 URL。 死信作为带有 SAS 令牌的容器 URL 提供。 该令牌只需要 `write` 对存储帐户中目标容器的权限。 完整的格式 URL 将采用以下格式： `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

若要了解有关 SAS 令牌的详细信息，请参阅： [*使用共享访问签名授予对 Azure 存储资源的有限访问权限 (SAS)*](../storage/common/storage-sas-overview.md)

若要了解如何使用死信设置终结点，请参阅 [*如何：在 Azure 数字孪生中管理终结点和路由 (api 和 CLI)*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering)。

### <a name="types-of-event-messages"></a>事件消息的类型

IoT 中心和 Azure 数字孪生中的不同类型的事件会生成不同类型的通知消息，如下所述。

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>后续步骤

请参阅如何设置和管理事件路由：
* [*操作说明：管理终结点和路由*](how-to-manage-routes-apis-cli.md)

或者，请参阅如何使用 Azure Functions 在 Azure 数字孪生中路由事件：
* [*如何：设置用于处理数据的 Azure 函数*](how-to-create-azure-function.md)