---
title: 管理终结点和路由（门户）
titleSuffix: Azure Digital Twins
description: 了解如何使用 Azure 门户为 Azure 数字孪生数据设置和管理终结点与事件路由。
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 31f960b39e771e7bfbf67c6e52c5da8e1fc6e0ec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932448"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>在 Azure 数字孪生中管理终结点和路由（门户）

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

在 Azure 数字孪生中，可将[事件通知](how-to-interpret-event-data.md)路由到下游服务或连接的计算资源。 首先需要设置可接收事件的终结点。 然后，可创建[事件路由](concepts-route-events.md)来指定由 Azure 数字孪生生成的哪些事件传递到哪些终结点。

本文介绍使用 [Azure 门户](https://portal.azure.com)创建终结点和路由的过程。

此外，还可以通过[事件路由 API](/rest/api/digital-twins/dataplane/eventroutes)、[SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) 或 [Azure 数字孪生 CLI](how-to-use-cli.md) 来管理终结点和路由。 有关使用上述机制（而非门户）的本文版本，请参阅[如何：管理终结点和路由（API 和 CLI）](how-to-manage-routes-apis-cli.md)。

## <a name="prerequisites"></a>先决条件

* 你需要一个 Azure 帐户（你可以在[此处](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)免费设置一个）
* 在 Azure 订阅中需要一个“Azure 数字孪生实例”。 如果还没有实例，可以使用[如何：设置实例和身份验证](how-to-set-up-instance-portal.md)中的步骤创建一个实例。 准备好以下设置值，以便稍后在本文中使用：
    - 实例名称
    - 资源组

设置实例后，可以在 [Azure 门户](https://portal.azure.com)中找到这些详细信息。 登录到门户，并在门户搜索栏中搜索实例的名称。
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Azure 门户搜索栏的屏幕截图。" lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

从结果中选择实例，即可在实例概述中查看这些详细信息：

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Azure 数字孪生实例“概述”页的屏幕截图。名称和资源组突出显示。":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>为 Azure 数字孪生创建终结点

以下是可为实例创建的受支持的终结点类型：
* [事件网格](../event-grid/overview.md) 
* [事件中心](../event-hubs/event-hubs-about.md)
* [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)

有关不同终结点类型的更多信息，请参阅[在 Azure 消息服务之间进行选择](../event-grid/compare-messaging-services.md)。

此部分介绍如何在 [Azure 门户](https://portal.azure.com)中创建其中一个终结点。

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>创建终结点 

创建终结点资源后，就可以将其用于 Azure 数字孪生终结点。 要创建新的终结点，请在 [Azure 门户](https://portal.azure.com)中转到实例页面（可以通过在门户搜索栏中输入名称来查找实例）。

1. 从实例菜单中选择“终结点”。 然后，在显示的“终结点”页选择“+ 创建终结点”。 随后将打开“创建终结点”页，可在该页面中按以下步骤填写字段。

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="创建“事件网格”类型终结点的屏幕截图。" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. 输入终结点的“名称”，并选择“终结点类型”。

1. 完成终结点类型所需的其他详细信息，包括订阅和[上面](#prerequisite-create-endpoint-resources)所述的终结点资源。
    1. 只有“事件中心”和“服务总线”终结点必须选择“身份验证类型”。 可以使用已预先创建授权规则的基于密钥的身份验证，如果要对 Azure 数字孪生实例使用带[托管标识](concepts-security.md#managed-identity-for-accessing-other-resources-preview)的终结点，也可以使用基于标识的身份验证。 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="创建“事件中心”类型终结点的屏幕截图。" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. 选择“保存”，完成终结点创建。

>[!IMPORTANT]
> 为了成功对终结点使用基于标识的身份验证，需要按照[如何：为路由事件启用托管标识（预览）](./how-to-enable-managed-identities-portal.md)中的步骤为实例创建托管标识。

创建终结点后，可以通过查看 Azure 门户顶部栏中的通知图标来验证是否已成功创建终结点： 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="用于验证终结点创建情况的通知的屏幕截图。门户顶部栏中铃形图标已选定，显示一条通知，指示“终结点 ADT-eh-endpoint 已成功创建”。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

如果终结点创建失败，可查看错误消息并在几分钟后重试。

还可以返回 Azure 数字孪生实例的“终结点”页上查看已创建的终结点。

目前，根据为终结点所选的名称，可使用事件网格、事件中心或服务总线主题作为 Azure 数字孪生内的终结点。 通常使用该名称作为事件路由（将在[本文的后面部分](#create-an-event-route)创建）的目标。

### <a name="create-an-endpoint-with-dead-lettering"></a>创建使用死信的终结点

当终结点无法在特定时间段内传递事件，或在尝试传递事件一定次数后仍无法传递，它可将未传递的事件发送到存储帐户。 此过程称为“死信处理”。

要创建启用死信的终结点，必须使用 [CLI 命令](how-to-use-cli.md)或[控制平面 API](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) 来创建终结点，而不是 Azure 门户。

有关如何使用这些工具创建终结点的说明，请参阅本文的 [API 和 CLI](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) 版本。

## <a name="create-an-event-route"></a>创建事件路由

若要实际地将数据从 Azure 数字孪生发送到终结点，需要定义一个事件路由。 通过这些路由，开发人员可在整个系统中串联事件流并连接到下游服务。 有关事件路由的详细信息，请参阅[概念：路由 Azure 数字孪生事件](concepts-route-events.md)。

先决条件：在继续创建路由之前，需要按照本文前面所述创建终结点。 完成终结点设置后，可以继续创建事件路由。

>[!NOTE]
>如果最近部署了终结点，在尝试将它们用于新的事件路由之前，请验证它们是否已完成部署。 如果由于终结点未就绪而无法设置路由，请等待几分钟，然后重试。

### <a name="creation-steps-with-the-azure-portal"></a>使用 Azure 门户的创建步骤

事件路由定义包含以下元素：
* 要使用的路由名称
* 要使用的终结点名称
* 用于定义发送到终结点的事件的筛选器
    - 若要禁用路由而不发送事件，请使用筛选器值 `false`
    - 若要启用未指定筛选条件的路由，请使用筛选器值 `true`
    - 有关任何其他类型筛选器的详细信息，请参阅以下[筛选器事件](#filter-events)部分。

单一路由可允许选择多个通知和事件类型。

若要创建事件路由，请在 [Azure 门户](https://portal.azure.com)中转到 Azure 数字孪生实例的详细信息页（可以通过在门户搜索栏中输入名称来查找实例）。

从实例菜单中选择“事件路由”。 然后，在显示的“事件路由”页选择“+ 创建事件路由”。 

在打开的“创建事件路由”页上，至少选择以下项目：
* “名称”字段的路由名称
* 要用于创建路由的“终结点” 

要启用该路由，还必须“添加事件路由筛选器”，其值至少为 `true`。 （保留默认值 `false` 会创建路由，但不会向其发送事件。）为此，请切换“高级编辑器”的开关将其启用，并在“筛选器”框中写入 `true`。

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="为实例创建事件路由的屏幕截图。" lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

完成后，点击“保存”按钮创建事件路由。

## <a name="filter-events"></a>筛选事件

如上所述，路由包含“筛选器”字段。 如果路由上的筛选器值为 `false`，则不会向终结点发送任何事件。 

启用最小筛选器 `true` 后，终结点将从 Azure 数字孪生接收各种事件：
* 遥测，由[数字孪生](concepts-twins-graph.md)使用 Azure 数字孪生服务 API 触发
* 孪生属性更改通知，在 Azure 数字孪生实例中的任何孪生属性更改时触发
* 生命周期事件，在创建或删除孪生体或关系时触发

可以通过定义更具体的筛选器来限制将发送的事件类型。

要在创建事件路由时添加事件筛选器，请使用“创建事件路由”页的“添加事件路由筛选器”部分。 

可以从一些基本的常用筛选器选项中选择，也可以使用高级筛选器选项编写自己的自定义筛选器。

>[!NOTE]
> 筛选器区分大小写，并需要与有效负载大小写匹配。 
>
> 对于遥测筛选器，这意味着大小写需要与设备发送的遥测的大小写匹配，而不一定是在孪生体的模型中定义的大小写。

#### <a name="use-the-basic-filters"></a>使用基本筛选器

若要使用基本筛选器，请展开“事件类型”选项，并选择与要发送到终结点的事件对应的复选框。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="使用基本筛选器创建事件路由的屏幕截图。选中了事件的复选框。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

筛选器文本框中将自动填充所选筛选器的文本：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="使用基本筛选器创建事件路由的屏幕截图。显示了选择事件后自动填充的筛选器文本。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>使用高级筛选器

也可以使用高级筛选器选项来编写自己的自定义筛选器。

要使用高级筛选器选项创建事件路由，请切换“高级筛选器”的开关将其启用。 然后，则可以在“筛选器”框中编写自己的事件筛选器：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="使用高级筛选器创建事件路由的屏幕截图。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

下面是受支持的路由筛选器。 “筛选器文本架构”列中的详细信息就是可以输入到筛选器框中的文本。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>后续步骤

阅读可接收的不同类型的事件消息：
* [如何：解释事件数据](how-to-interpret-event-data.md)