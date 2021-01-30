---
title: " (门户中管理终结点和路由) "
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure 门户设置和管理 Azure 数字孪生数据的终结点和事件路由。
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0f705aa61f1fe627dc0c8227242538e01ffce1d5
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070767"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>在 Azure 数字孪生 (门户中管理终结点和路由) 

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

在 Azure 数字孪生中，可以将 [事件通知](how-to-interpret-event-data.md) 路由到下游服务或连接的计算资源。 首先需要设置可接收事件的终结点。 然后，可以创建 [**事件路由**](concepts-route-events.md) ，用于指定由 Azure 数字孪生生成的哪些事件将传递到哪些终结点。

本文将指导你完成使用 [Azure 门户](https://portal.azure.com)创建终结点和路由的过程。

此外，还可以通过 [事件路由 api](/rest/api/digital-twins/dataplane/eventroutes)、 [Sdk](how-to-use-apis-sdks.md#overview-data-plane-apis)或 [Azure 数字孪生 CLI](how-to-use-cli.md)来管理终结点和路由。 有关使用这些机制（而不是门户）的本文版本，请参阅 [*如何：管理终结点和路由 (api 和 CLI)*](how-to-manage-routes-apis-cli.md)。

## <a name="prerequisites"></a>先决条件

* 你将需要一个 **Azure 帐户** (你可以在 [此处](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 免费设置一个帐户) 
* 你将需要 Azure 订阅中的 **Azure 数字孪生实例** 。 如果尚未安装实例，则可以使用 [*操作方法：设置实例和身份验证*](how-to-set-up-instance-portal.md)中的步骤创建一个实例。 将安装程序中的以下值用于本文后面的内容：
    - 实例名称
    - 资源组

设置实例后，可以在 [Azure 门户](https://portal.azure.com) 中找到这些详细信息。 登录到门户，并在门户搜索栏中搜索实例的名称。
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Azure 门户搜索栏的屏幕截图。" lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

从结果中选择实例，以在实例的概述中查看这些详细信息：

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Azure 数字孪生实例的 &quot;概述&quot; 页的屏幕截图。将突出显示名称和资源组。":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>为 Azure 数字孪生创建终结点

这些是可为实例创建的支持的终结点类型：
* [事件网格](../event-grid/overview.md) 
* [事件中心](../event-hubs/event-hubs-about.md)
* [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)

有关不同终结点类型的详细信息，请参阅在 [*Azure 消息服务之间选择*](../event-grid/compare-messaging-services.md)。

本部分介绍如何在 [Azure 门户](https://portal.azure.com)中创建其中一个终结点。

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>创建终结点 

创建终结点资源后，可将其用于 Azure 数字孪生终结点。 若要创建新的终结点，请在 [Azure 门户](https://portal.azure.com) 中中转到实例的页面 (可以通过在门户搜索栏中输入名称) 来查找实例。

1. 从 "实例" 菜单中选择 " _终结点_"。 然后，在下面的 " *终结点* " 页中，选择 " *+ 创建终结点*"。 这将打开 " *创建终结点* " 页，在该页面中，你将在以下步骤中填写字段。

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="创建事件网格类型终结点的屏幕截图。" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. 输入终结点的 **名称** ，并选择 **终结点类型**。

1. 完成终结点类型所需的其他详细信息，包括你的订阅和 [上面](#prerequisite-create-endpoint-resources)所述的终结点资源。
    1. 仅适用于事件中心和服务总线终结点，你必须选择 **身份验证类型**。 你可以将基于密钥的身份验证与预先创建的授权规则或基于身份的身份验证配合使用，前提是你要将该终结点用于 Azure 数字孪生实例的 [托管标识](concepts-security.md#managed-identity-for-accessing-other-resources-preview) 。 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="创建事件中心类型终结点的屏幕截图。" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. 通过选择 " _保存_" 完成终结点的创建。

>[!IMPORTANT]
> 为了成功地对终结点使用基于身份的身份验证，需要按照 [*如何：为路由事件启用托管标识 (预览)*](how-to-enable-managed-identities.md)为实例创建托管标识。

创建终结点后，可以通过选中顶部 Azure 门户栏中的通知图标来验证是否已成功创建终结点： 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="通知的屏幕截图，用于验证终结点的创建。选中门户顶部栏中的 &quot;钟形&quot; 图标，出现 &quot;Endpoint ADT 已成功创建&quot; 的通知。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

如果终结点创建失败，请在几分钟后观察错误消息并重试。

你还可以在 Azure 数字孪生实例的 " *终结点* " 页上查看已创建的终结点。

现在，"事件网格"、"事件中心" 或 "服务总线" 主题在 Azure 数字孪生内作为终结点提供，其名称位于为终结点选择的名称下。 通常将该名称用作 **事件路由** 的目标， [稍后将在本文中](#create-an-event-route)创建。

### <a name="create-an-endpoint-with-dead-lettering"></a>创建具有死信的端点

当终结点无法在某个时间段内传递事件时，或者尝试将事件传递到一定次数后，它可以将未送达的事件发送到存储帐户。 此过程称为“死信处理”。

为了创建启用了死信的端点，必须使用 [CLI 命令](how-to-use-cli.md) 或 [控制平面 api](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) 来创建终结点，而不是 Azure 门户。

有关如何在这些工具中执行此操作的说明，请参阅本文的 [*api 和 CLI*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) 版本。

## <a name="create-an-event-route"></a>创建事件路由

若要将数据从 Azure 数字孪生实际发送到终结点，需要定义 **事件路由**。 通过这些路由，开发人员可以将事件流连接到整个系统和下游服务。 有关事件路由的详细信息，请参阅 [*概念：路由 Azure 数字孪生事件*](concepts-route-events.md)。

**必备组件**：你需要先按本文前面所述创建终结点，然后才能继续创建路由。 终结点完成设置后，可以继续创建事件路由。

>[!NOTE]
>如果你最近部署了终结点，请验证它们是否已完成部署， **然后再** 尝试将它们用于新的事件路由。 如果无法设置路由，因为终结点尚未准备就绪，请等待几分钟，然后重试。

### <a name="creation-steps-with-the-azure-portal"></a>Azure 门户的创建步骤

事件路由定义包含以下元素：
* 要使用的路由名称
* 要使用的终结点的名称
* 用于定义发送到终结点的事件的筛选器
    - 若要禁用路由，以便不发送事件，请使用筛选器值 `false`
    - 若要启用没有特定筛选的路由，请使用筛选器值 `true`
    - 有关任何其他类型的筛选器的详细信息，请参阅下面的 " [*筛选器事件*](#filter-events) " 一节。

单个路由可以允许选择多个通知和事件类型。

若要创建事件路由，请在 [Azure 门户](https://portal.azure.com) 中的 Azure 数字孪生实例中转到详细信息页 (可以通过在门户搜索栏中输入其名称) 找到该实例。

在 "实例" 菜单中，选择 " _事件路由_"。 然后，在随后的 *事件路由* 页面中，选择 " *+ 创建事件路由*"。 

在打开的 " *创建事件路由* " 页上，选择 "最小值"：
* " _名称_ " 字段中的路由名称
* 要用于创建路由的 _终结点_ 

若要启用路由，还必须至少添加的 **事件路由筛选器** `true` 。  (保留的默认值 `false` 将创建路由，但不会向其发送任何事件。 ) 为此，切换 " _高级编辑器_ " 的开关以启用它，然后 `true` 在 " *筛选器* " 框中写入。

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="为实例创建事件路由的屏幕截图。" lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

完成后，单击 " _保存_ " 按钮创建事件路由。

## <a name="filter-events"></a>筛选事件

如上所述，路由具有 **筛选** 字段。 如果路由上的筛选器值为 `false` ，则不会将任何事件发送到终结点。 

启用的最小筛选器后 `true` ，终结点将从 Azure 数字孪生收到各种事件：
* [数字孪生](concepts-twins-graph.md)使用 Azure 数字孪生服务 API 触发的遥测数据
* 克隆属性更改通知，对 Azure 数字孪生实例中任何克隆的属性更改触发
* 生命周期事件，在创建或删除孪生或关系时触发

可以通过定义更具体的筛选器来限制正在发送的事件类型。

若要在创建事件路由时添加事件筛选器，请使用 "*创建事件路由*" 页的 "_添加事件路由筛选器_" 部分。 

你可以从某些基本的常见筛选器选项中进行选择，也可以使用高级筛选器选项编写你自己的自定义筛选器。

#### <a name="use-the-basic-filters"></a>使用基本筛选器

若要使用基本筛选器，请展开 " _事件类型_ " 选项，并选择与要发送到终结点的事件对应的复选框。 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="使用基本筛选器创建事件路由的屏幕截图。选中事件的复选框。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

这将用您选择的筛选器的文本自动填充 "筛选器" 文本框：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="使用基本筛选器创建事件路由的屏幕截图。选择事件后显示自动填充的筛选器文本。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>使用高级筛选器

或者，您可以使用 "高级筛选器" 选项来编写您自己的自定义筛选器。

若要使用高级筛选器选项创建事件路由，请切换 _高级编辑器_ 的开关以启用它。 然后，你可以在 " *筛选器* " 框中编写自己的事件筛选器：

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="使用高级筛选器创建事件路由的屏幕截图。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

下面是受支持的路由筛选器。 *筛选器文本架构* 列中的详细信息是可以输入到 "筛选器" 框中的文本。

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>后续步骤

阅读可接收的不同类型的事件消息：
* [*操作说明：解释事件数据*](how-to-interpret-event-data.md)
