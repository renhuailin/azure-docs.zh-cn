---
title: 事件路由
titleSuffix: Azure Digital Twins
description: 了解如何将事件路由到 Azure 数字孪生中或路由到其他 Azure 服务。
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b9038840142be64918b22f1aefc32d505252d71d
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254238"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>将事件路由到 Azure 数字孪生内部和外部

Azure 数字孪生使用事件路由向服务外的使用者发送数据。 

发送 Azure 数字孪生数据的主要情况有两种：
* 将 Azure 数字孪生图中的一个孪生体的数据发送给另一个孪生体。 例如，当一个数字孪生体上的属性发生变化时，可能需要相应地通知并更新另一个数字孪生体。
* 向下游数据服务发送数据以进行额外的存储或处理（也称为数据传出）。 例如，
  - 一家医院可能希望将 Azure 数字孪生事件数据发送到[时序见解 (TSI)](../time-series-insights/overview-what-is-tsi.md)，记录洗手相关事件的时序数据以进行批量分析。
  - 正在使用 [Azure Maps](../azure-maps/about-azure-maps.md) 的企业可能希望使用 Azure 数字孪生来强化其解决方案。 在设置 Azure 数字孪生后，他们可以快速启用 Azure Maps，将 Azure Maps 实体作为孪生图中的[数字孪生体](concepts-twins-graph.md)引入 Azure 数字孪生，或同时利用 Azure Maps 和 Azure 数字孪生数据来运行功能强大的查询。

这两种情况都使用事件路由。

## <a name="about-event-routes"></a>关于事件路由

使用事件路由可将 Azure 数字孪生中的数字孪生体的事件数据发送到订阅中的自定义终结点。 终结点目前支持三项 Azure 服务：[事件中心](../event-hubs/event-hubs-about.md)、[事件网格](../event-grid/overview.md)和[服务总线](../service-bus-messaging/service-bus-messaging-overview.md)。 每项 Azure 服务都可以连接到其他服务，并充当中转站，将数据发送到最终目标（如 TSI 或 Azure Maps）以进行所需的任何处理。

下图说明了通过具有 Azure 数字孪生特性的大型 IoT 解决方案的事件数据流：

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Azure 数字孪生通过终结点将数据路由到多个下游服务的图示。" border="false":::

事件路由的典型下游目标是 TSI、Azure Maps、存储和分析解决方案等资源。

### <a name="event-routes-for-internal-digital-twin-events"></a>内部数字孪生事件的事件路由

事件路由还用于处理孪生图中的事件，并将一个数字孪生体中的数据发送到另一个数字孪生体。 此操作是通过事件网格将事件路由连接到计算资源（如 [Azure Functions](../azure-functions/functions-overview.md)）来完成的。 然后，这些函数会定义孪生体应如何接收和响应事件。 

当计算资源希望根据通过事件路由收到的事件修改孪生图时，它可以提前知道要修改哪一个孪生体。 

另外，事件消息还包含发送消息的源孪生体的 ID，因此计算资源可使用查询或遍历关系来查找所需操作的目标孪生体。 

计算资源还需要单独建立安全和访问权限。

若要演练如何设置用于处理数字孪生体事件的 Azure 函数，请参阅[设置孪生体到孪生体的事件处理](how-to-send-twin-to-twin-events.md)。

## <a name="create-an-endpoint"></a>创建终结点

若要定义事件路由，开发人员必须首先定义终结点。 终结点是支持路由连接的 Azure 数字孪生外部的目标。 支持的目标包括：
* 事件网格自定义主题
* 事件中心
* 服务总线

若要[创建终结点](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins)，可使用 Azure 数字孪生 REST API、CLI 命令或 Azure 门户。

定义终结点时，需要提供：
* 终结点名称
* 终结点类型（事件网格、事件中心或服务总线）
* 要进行身份验证的主要连接字符串和辅助连接字符串 
* 终结点的主题路径，例如 your-topic.westus2.eventgrid.azure.net

控制平面中可用的终结点 API 包括：
* 创建终结点
* 获取终结点列表
* 按名称获取终结点
* 按名称删除终结点

## <a name="create-an-event-route"></a>创建事件路由
 
若要[创建事件路由](how-to-manage-routes.md#create-an-event-route)，可使用 Azure 数字孪生 REST API、CLI 命令或 Azure 门户。

下面是使用 `CreateOrReplaceEventRouteAsync` [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 调用在客户端应用程序中创建事件路由的示例： 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::

1. 首先创建一个 `DigitalTwinsEventRoute` 对象，构造函数将使用终结点的名称。 此 `endpointName` 字段标识一个终结点，如事件中心、事件网格或服务总线。 在进行此注册调用之前，必须在订阅中创建这些终结点，并使用控制平面 API 将这些终结点附加到 Azure 数字孪生。

2. 事件路由对象还有一个 Filter 字段，该字段可用于限制流经此路由的事件类型。 筛选器 `true` 启用无额外筛选的路由（筛选器 `false` 禁用路由）。 

3. 然后，此事件路由对象将与路由名称一起传递到 `CreateOrReplaceEventRouteAsync`。

> [!TIP]
> 所有 SDK 函数都提供同步和异步版本。

## <a name="dead-letter-events"></a>死信事件

当终结点无法在特定时间段内传递事件，或在尝试传递事件一定次数后仍无法传递，它可将未传递的事件发送到存储帐户。 此过程称为“死信处理”。 如果满足以下任一条件，Azure 数字孪生会将事件列入死信队列。 

* 事件未在生存期内传递
* 尝试传递事件的次数已超出限制

如果满足上述任一条件，则会将该事件删除或视为死信。 默认情况下，每个终结点不会启用死信功能。 若要启用该功能，在创建终结点时必须指定一个存储帐户来存放未传递的事件。 然后，你可从此存储帐户中拉取事件来解决传递问题。

在设置死信位置之前，必须有一个包含容器的存储帐户。 在创建终结点时，需要提供此容器的 URL。 死信作为带有 SAS 令牌的容器 URL 提供。 该令牌只需要存储帐户中目标容器的 `write` 权限。 完整格式的 URL 将为 `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>`

若要详细了解 SAS 令牌，请参阅使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限。

若要了解如何设置具有死信功能的终结点，请参阅[管理 Azure 数字孪生中的终结点和路由](how-to-manage-routes.md#create-an-endpoint-with-dead-lettering)。

### <a name="types-of-event-messages"></a>事件消息的类型

IoT 中心和 Azure 数字孪生中的不同类型的事件会生成不同类型的通知消息，如下所述。

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>后续步骤

请参阅如何设置和管理事件路由：
* [管理终结点和路由](how-to-manage-routes.md)

或者，请参阅如何使用 Azure Functions 在 Azure 数字孪生中路由事件：
* [设置孪生体到孪生体的事件处理](how-to-send-twin-to-twin-events.md)。