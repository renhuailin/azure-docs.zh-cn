---
title: 将事件从 Azure API 管理发送到事件网格
description: 在本快速入门中，你将为 Azure API 管理实例启用事件网格事件，然后将事件发送到示例应用程序。
author: dlepow
ms.topic: how-to
ms.service: api-management
ms.author: danlep
ms.date: 07/12/2021
ms.custom: ''
ms.openlocfilehash: 9e81fbe26c8b98a0694789567cef9126d5ca02fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747798"
---
# <a name="send-events-from-api-management-to-event-grid-preview"></a>将事件从 API 管理发送到事件网格（预览版）

API 管理与 [Azure 事件网格](../event-grid/overview.md)集成，使你可以向其他服务发送事件通知，并触发下游流程。 事件网格是一个完全托管的事件路由服务，使用发布-订阅模型。 事件网格包含对 Azure 服务（如 [Azure Functions](../azure-functions/functions-overview.md) 和 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)）的内置支持，还可使用 Webhook 向非 Azure 服务传递事件警报。

例如，使用与事件网格的集成，可以生成一个用于更新数据库、创建计费帐户，并在每次将用户添加到 API 管理实例时发送电子邮件通知的应用程序。

在本文中，你将在 API 管理实例中订阅事件网格事件，触发事件，然后将事件发送到用于处理数据的终结点。 为简单起见，你会将事件发送到一个可收集并显示消息的示例 Web 应用：

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-intro.png" alt-text="事件网格查看器中的 API 管理事件":::

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
- 如果你还没有 API 管理服务，请完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)
- 在 API 管理实例中启用[系统分配的托管标识](api-management-howto-use-managed-service-identity.md#create-a-system-assigned-managed-identity)。
- 如果没有用于部署示例终结点的[资源组](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)，请创建一个。

## <a name="create-an-event-endpoint"></a>创建事件终结点

在本部分，你将使用资源管理器模板将一个预生成的示例 Web 应用程序部署到 Azure 应用服务。 稍后，你将订阅 API 管理实例的事件网格事件，并将此应用指定为事件要发送到的终结点。

若要部署示例应用，可以使用 Azure CLI、Azure PowerShell 或 Azure 门户。 以下示例在 Azure CLI 中使用 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 命令。

* 将 `RESOURCE_GROUP_NAME` 设置为现有资源组的名称
* 将 `SITE_NAME` 设置为你的 Web 应用的唯一名称

  站点名称在 Azure 中必须唯一，因为它是 Web 应用的完全限定域名 (FQDN) 的一部分。 在稍后的部分，你将在 Web 浏览器中导航到该应用的 FQDN 以查看事件。

```azurecli-interactive
RESOURCE_GROUP_NAME=<your-resource-group-name>
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

部署成功后（可能需要几分钟时间），打开浏览器并导航到 Web 应用，以确保它正在运行：

`https://<your-site-name>.azurewebsites.net`

此时你应会看到示例应用，但其中未显示事件消息。

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-api-management-events"></a>订阅 API 管理事件

在事件网格中订阅一个主题，以告知你要跟踪哪些事件，以及要将事件发送到何处。 在此处创建对 API 管理实例中事件的订阅。

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。
1. 选择“事件(预览版)”>“+ 事件订阅”。 
1. 在“基本信息”选项卡上：
    * 输入事件订阅的描述性名称。
    * 在“事件类型”中，选择要发送到事件网格的一种或多种 API 管理事件类型。 对于本文中的示例，请至少选择“Microsoft.APIManagement.ProductCreated” 
    * 在“终结点详细信息”中选择“Web Hook”事件类型，单击“选择终结点”，然后输入 Web 应用 URL 再加上 `api/updates`  。 示例：`https://myapp.azurewebsites.net/api/updates`。
    * 选择“确认所选内容”。
1. 将其余选项卡上的设置保留默认值，然后选择“创建”。

    :::image type="content" source="media/how-to-event-grid/create-event-subscription.png" alt-text="在 Azure 门户中创建事件订阅":::

## <a name="trigger-and-view-events"></a>触发和查看事件

正常运行示例应用并使用事件网格订阅 API 管理实例后，接下来可以生成事件。

例如，在 API 管理实例中[创建一个产品](./api-management-howto-add-products.md)。 如果事件订阅包括 Microsoft.APIManagement.ProductCreated 事件，则创建产品会触发一个推送到 Web 应用终结点的事件。 

导航到你的事件网格查看器 Web 应用，应会看到 `ProductCreated` 事件。 选择该事件旁边的按钮以显示详细信息。 

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-product-created.png" alt-text="事件网格查看器中的“已创建产品”事件":::

## <a name="event-grid-event-schema"></a>事件网格事件架构

API 管理事件数据包括 `resourceUri`（用于标识触发了事件的 API 管理资源）。 有关 API 管理事件消息架构的详细信息，请参阅事件网格文档：

[API 管理的 Azure 事件网格事件架构](../event-grid/event-schema-api-management.md)

## <a name="next-steps"></a>后续步骤

* [在 Azure 消息传递服务之间进行选择 - 事件网格、事件中心和服务总线](../event-grid/compare-messaging-services.md)
* 详细了解如何[订阅事件](../event-grid/subscribe-through-portal.md)。