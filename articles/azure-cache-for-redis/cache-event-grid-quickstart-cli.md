---
title: 快速入门：使用 Azure CLI 将 Azure Cache for Redis 事件路由到 Web 终结点
description: 使用 Azure 事件网格订阅 Azure Cache for Redis 事件、触发事件并查看结果。
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 7f33ca0043400962054fabb1aadb1da612fe5426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806420"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-azure-cli"></a>快速入门：使用 Azure CLI 将 Azure Cache for Redis 事件路由到 Web 终结点

Azure 事件网格是针对云的事件处理服务。 在本快速入门中，你将使用 Azure CLI 订阅 Azure Cache for Redis 事件、触发事件并查看结果。

通常，你会将事件发送到处理事件数据并执行操作的终结点。 但是，为了简化本快速入门，你需要将事件发送到要收集和显示消息的 Web 应用。 完成本快速入门所述步骤后，即可看到事件数据已发送到 Web 应用。

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，则本快速入门要求你运行最新版的 Azure CLI（2.0.70 或更高版本）。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

如果不使用 Cloud Shell，则必须先使用 `az login` 登录。

## <a name="create-a-resource-group"></a>创建资源组

事件网格主题部署为单个 Azure 资源，且必须在 Azure 资源组下进行预配。 资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用“[az group create](/cli/azure/group)”命令创建资源组。 

下面的示例在 westcentralus 位置创建了一个名为 `<resource_group_name>` 的资源组。  将 `<resource_group_name>` 替换为资源组的唯一名称。

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-cache-instance"></a>创建缓存实例

```azurecli-interactive
#/bin/bash

# Create a Basic C0 (256 MB) Azure Cache for Redis instance
az redis create --name <cache_name> --resource-group <resource_group_name> --location westcentralus --sku Basic --vm-size C0
```


## <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅主题之前，让我们创建事件消息的终结点。 通常情况下，终结点基于事件数据执行操作。 为了简化此快速入门，将部署用于显示事件消息的[预建的 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 所部署的解决方案包括应用服务计划、应用服务 Web 应用和 GitHub 中的源代码。

将 `<your-site-name>` 替换为 Web 应用的唯一名称。 Web 应用名称必须唯一，因为它是 DNS 条目的一部分。

```azurecli-interactive
sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

部署可能需要几分钟才能完成。 部署成功后，请查看 Web 应用以确保它正在运行。 在 Web 浏览器中导航到 `https://<your-site-name>.azurewebsites.net`

应会看到站点上当前未显示任何消息。

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-azure-cache-for-redis-instance"></a>订阅 Azure Cache for Redis 实例

在此步骤中，你将订阅主题，使事件网格知道你想要跟踪哪些事件，以及要将事件发送到哪里。 以下示例将订阅你创建的 Azure Cache for Redis 实例，并将 Web 应用中的 URL 作为事件通知的终结点进行传递。 将 `<event_subscription_name>` 替换为事件订阅的名称。 对于 `<resource_group_name>` 和 `<cache_name>`，请使用此前创建的值。

Web 应用的终结点必须包括后缀 `/api/updates/`。

```azurecli-interactive
cacheId=$(az redis show --name <cache_name> --resource-group <resource_group_name> --query id --subscription <subscription_id>)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --source-resource-id $cacheId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

再次查看 Web 应用，并注意现已向该应用发送了订阅验证事件。 选择眼睛图标以展开事件数据。 事件网格发送验证事件，以便终结点可以验证它是否想要接收事件数据。 Web 应用包含用于验证订阅的代码。

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure 事件网格查看器。":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>从 Azure Cache for Redis 触发事件

现在，让我们触发一个事件，看事件网格如何将消息分发到终结点。 导出 Azure Cache for Redis 实例中存储的数据。 同样，对于 `{cache_name}` 和 `{resource_group_name}`，请使用此前创建的值。

```azurecli-interactive
az redis export  --ids '/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}' \
    --prefix '<prefix_for_exported_files>' \
    --container '<SAS_url>'  
```

现已触发事件，并且事件网格已将消息发送到订阅时配置的终结点。 查看 Web 应用以查看刚刚发送的事件。


```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ExportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ExportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ExportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>清理资源
如果计划继续使用此 Azure Cache for Redis 实例和事件订阅，请不要清理在本快速入门中创建的资源。 如果不打算继续学习，请使用以下命令删除这些资源。

将 `<resource_group_name>` 替换为上面创建的资源组。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>后续步骤

你现已了解如何创建主题和事件订阅后，接下来请详细了解 Azure Cache for Redis 事件以及事件网格的功能：

- [对 Azure Cache for Redis 事件作出反应](cache-event-grid.md)
- [关于事件网格](../event-grid/overview.md)
