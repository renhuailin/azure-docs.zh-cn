---
title: 快速入门：使用 Azure 门户将 Azure Cache for Redis 事件路由到 Web 终结点
description: 使用 Azure 事件网格订阅 Azure Cache for Redis 事件，将事件发送到 Webhook，并处理 Web 应用程序中的事件
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.custom:
- mode-portal
ms.openlocfilehash: 72596521850ba3b3b10ef572aa768672869ec7ec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620913"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>快速入门：使用 Azure 门户将 Azure Cache for Redis 事件路由到 Web 终结点

Azure 事件网格是针对云的事件处理服务。 在本快速入门中，你将使用 Azure 门户创建一个 Azure Cache for Redis 实例、订阅该实例的事件、触发事件，并查看结果。 通常，你会将事件发送到处理事件数据并执行操作的终结点。 但是，为了简化本快速入门，你需要将事件发送到要收集和显示消息的 Web 应用。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

完成后即可看到事件数据已发送到 Web 应用。

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure 事件网格查看器按 JSON 格式进行缩放。":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>创建 Azure Cache for Redis 缓存实例

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅缓存实例的事件之前，先来创建事件消息的终结点。 通常情况下，终结点基于事件数据执行操作。 为了简化此快速入门，需要部署一个[预生成 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)，它用于显示事件消息。 所部署的解决方案包括应用服务计划、应用服务 Web 应用和 GitHub 中的源代码。

1. 选择 GitHub 自述文件中的“部署到 Azure”，将解决方案部署到你的订阅。

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="“部署到 Azure”按钮。":::

1. 在“自定义部署”页上执行以下步骤：
    1. 对于“资源组”，请选择你在创建缓存实例时创建的资源组。 这样就可以在完成本教程后，通过删除资源组来更轻松地清理资源。  
    2. 对于“站点名称”，请输入 Web 应用的名称。
    3. 对于“托管计划名称”，请输入用于托管 Web 应用的应用服务计划的名称。
    4. 选中“我同意上述条款和条件”对应的复选框。
    5. 选择“购买”。 

    | 设置      | 建议的值  | 说明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **订阅** | 单击下拉箭头并选择你的订阅。 | 在其下创建此 Web 应用的订阅。 |
    | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 |
    | **站点名称** | 输入 Web 应用的名称。 | 该值不能为空。 |
    | **托管计划名称** | 输入用于托管 Web 应用的应用服务计划的名称。 | 该值不能为空。 |

1. 在门户中选择“警报”（钟形图标），然后选择“转到资源组”。

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Azure 门户部署通知。":::

1. 在“资源组”页上，在资源列表中，选择你创建的 Web 应用。 在此列表中，还可看到应用服务计划和缓存实例。

1. 在 Web 应用的“应用服务”页上，选择相应的 URL 以导航到该网站。 URL 应采用以下格式：`https://<your-site-name>.azurewebsites.net`。

1. 确认你可以看到站点，但尚未有任何事件发布到站点。

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="空的事件网格查看器站点。":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>订阅 Azure Cache for Redis 实例

在此步骤中，你将订阅主题，使事件网格知道你想要跟踪哪些事件，以及要将事件发送到哪里。

1. 在门户中，导航到你之前创建的缓存实例。
1. 在“Azure Cache for Redis”页面上，选择左侧菜单中的“事件” 。
1. 选择“Webhook”。 你正在使用终结点的 Web Hook 将事件发送到查看器应用。

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Azure 门户的“事件”页面。":::

1. 在“创建事件订阅”页面中输入以下内容：

    | 设置      | 建议的值  | 说明 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名称** | 为事件订阅输入一个名称。 | 值长度必须为 3 到 64 个字符。 只能包含字母、数字和短划线。 |
    | **事件类型** | 下拉并选择要推送到目标的事件类型。 对于本快速入门，我们将缩放缓存实例。 | 可用选项包括修补、缩放、导入和导出。 |
    | **终结点类型** | 选择“Webhook”。 | 用来接收事件的事件处理程序。 |
    | **终结点** | 选择“选择终结点”，输入你的 Web 应用的 URL 并将 `api/updates` 添加到主页 URL（例如 `https://cache.azurewebsites.net/api/updates`），然后选择“确认选择”。 | 这是你之前创建的 Web 应用的 URL。 |

1. 现在，在“创建事件订阅”页上，选择“创建”以创建事件订阅。 

1. 再次查看 Web 应用，并注意现已向该应用发送了订阅验证事件。 选择眼睛图标以展开事件数据。 事件网格发送验证事件，以便终结点可以验证它是否想要接收事件数据。 Web 应用包含用于验证订阅的代码。

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure 事件网格查看器。":::

## <a name="send-an-event-to-your-endpoint"></a>向终结点发送事件

现在，让我们触发一个事件，看事件网格如何将消息分发到终结点。 我们将缩放 Azure Cache for Redis 实例。

1. 在 Azure 门户中，导航到 Azure Cache for Redis 实例，并在左侧菜单中选择“缩放”。

1. 从“缩放”页中选择所需的定价层，然后选择“选择”。

    可以扩展到不同定价层，但有以下限制：

    * 不能从较高的定价层缩放到较低的定价层。
      * 不能从 **高级** 缓存向下缩放到 **标准** 或 **基本** 缓存。
      * 不能从 **标准** 缓存向下缩放到 **基本** 缓存。
    * 可从 **基本** 缓存缩放到 **标准** 缓存，但不能同时更改大小。 如果需要不同大小，则可以执行后续缩放操作以缩放为所需大小。
    * 不能从 **基本** 缓存直接缩放到 **高级** 缓存。 首先在一个缩放操作中从 **基本** 缩放到 **标准**，然后在后续的缩放操作中从 **标准** 缩放到 **高级**。
    * 不能从较大的大小减小为 **C0 (250 MB)** 。

    当缓存缩放到新的定价层，会在左侧通过 Azure Cache for Redis 显示“缩放”状态。 缩放完成后，状态将从 **正在缩放** 更改为 **正在运行**。

1. 现已触发事件，并且事件网格已将消息发送到订阅时配置的终结点。 消息采用 JSON 格式，它包含一个或多个事件的数组。 在以下示例中，JSON 消息包含一个事件的数组。 查看 Web 应用，你将会看到已收到一个“ScalingCompleted”事件。

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure 事件网格查看器按 JSON 格式进行缩放。":::

## <a name="clean-up-resources"></a>清理资源

如果打算继续处理此事件，请不要清除在本快速入门中创建的资源。 否则，请删除在这些资源。

选择资源组，然后选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

了解如何创建自定义主题和事件订阅后，请详细了解事件网格的功能：

* [对 Azure Cache for Redis 事件作出反应](cache-event-grid.md)
* [关于事件网格](../event-grid/overview.md)
