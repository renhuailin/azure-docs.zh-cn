---
title: 教程：使用 Azure CLI 将策略状态更改事件路由到事件网格
description: 在本教程中，你将配置事件网格以侦听策略状态更改事件并调用 Webhook。
ms.date: 08/17/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: bbbc1468c1a50835ea79efcd11e468c70002769c
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322317"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>教程：使用 Azure CLI 将策略状态更改事件路由到事件网格

本文介绍如何设置 Azure Policy 事件订阅，以将策略状态更改事件发送到 Web 终结点。 Azure Policy 用户可以订阅当资源上发生策略状态更改时发出的事件。 这些事件可以触发 Webhook、[Azure Functions](../../../azure-functions/index.yml)、[Azure 存储队列](../../../storage/queues/index.yml)，或 [Azure 事件网格](../../../event-grid/index.yml)支持的任何其他事件处理程序。 通常，你会将事件发送到处理事件数据并执行操作的终结点。 但是，为了简化本教程，你会将事件发送到收集并显示消息的 Web 应用。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

- 本快速入门需要运行 Azure CLI 2.0.76 版本或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

- 即使你以前使用过 Azure Policy 或事件网格，也需要重新注册其各自的资源提供程序：

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>创建资源组

事件网格主题是 Azure 资源，必须放置在 Azure 资源组中。 该资源组是在其中部署和管理 Azure 资源的逻辑集合。

使用“[az group create](/cli/azure/group)”命令创建资源组。

以下示例在 _westus_ 位置创建名为 `<resource_group_name>` 的资源组。 将 `<resource_group_name>` 替换为资源组的唯一名称。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>创建事件网格系统主题

现在我们有了一个资源组，接下来创建一个[系统主题](../../../event-grid/system-topics.md)。 事件网格中的系统主题表示 Azure Policy 和 Azure 事件中心等 Azure 服务发布的一个或多个事件。 此系统主题对 Azure Policy 状态更改使用 `Microsoft.PolicyInsights.PolicyStates` 主题类型。 请将 **scope** 参数中的 `<SubscriptionID>` 替换为你的订阅 ID，将 **resource-group** 参数中的 `<resource_group_name>` 替换为前面创建的资源组。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>创建消息终结点

在订阅主题之前，让我们创建事件消息的终结点。 通常情况下，终结点基于事件数据执行操作。 为了简化此快速入门，将部署用于显示事件消息的[预建的 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 所部署的解决方案包括应用服务计划、应用服务 Web 应用和 GitHub 中的源代码。

将 `<your-site-name>` 替换为 Web 应用的唯一名称。 Web 应用名称必须唯一，因为它是 DNS 条目的一部分。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

部署可能需要几分钟才能完成。 部署成功后，请查看 Web 应用以确保它正在运行。 在 Web 浏览器中导航到 `https://<your-site-name>.azurewebsites.net`

应会看到站点上当前未显示任何消息。

## <a name="subscribe-to-the-system-topic"></a>订阅系统主题

订阅主题，以告知事件网格要跟踪哪些事件以及要将这些事件发送到哪个位置。 以下示例将订阅你创建的系统主题，并将 Web 应用中的 URL 作为终结点传递，以接收事件通知。 将 `<event_subscription_name>` 替换为事件订阅的名称。 对于 `<resource_group_name>` 和 `<your-site-name>`，请使用此前创建的值。

Web 应用的终结点必须包括后缀 `/api/updates/`。

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

再次查看 Web 应用，并注意现已向该应用发送了订阅验证事件。 选择眼睛图标以展开事件数据。 事件网格发送验证事件，以便终结点可以验证它是否想要接收事件数据。 Web 应用包含用于验证订阅的代码。

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="预生成的 Web 应用中事件网格订阅验证事件的屏幕截图。":::

## <a name="create-a-policy-assignment"></a>创建策略分配

在本快速入门中，你将创建一个策略分配，并分配“需要资源组上的标记”定义。 此策略定义将会标识缺少策略分配过程中所配置的标记的资源组。

运行以下命令创建一个策略分配，该分配的范围限定为你创建的用于保存事件网格主题的资源组：

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

上述命令使用以下信息：

- **名称** - 分配的实际名称。 此示例使用了 _requiredtags-events_。
- **显示名称** - 策略分配的显示名称。 在本例中，使用的是“需要资源组上的标记”。
- **范围** - 范围确定在其中实施策略分配的资源或资源组。 它可以从订阅延伸至资源组。 请务必将 &lt;scope&gt; 替换为资源组的名称。 资源组范围的格式为 `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>`。
- **策略** - 策略定义 ID，用作创建分配的依据。 在本例中，它是策略定义“需要资源组上的标记”的 ID。 若要获取策略定义 ID，请运行以下命令：`az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

创建策略分配后，等待 **Microsoft.PolicyInsights.PolicyStateCreated** 事件通知出现在 Web 应用中。 我们创建的资源组最初显示的 `data.complianceState` 值为 _NonCompliant_。

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="预生成的 Web 应用中资源组的事件网格订阅 Policy State Created 事件的屏幕截图。":::

> [!NOTE]
> 如果资源组继承订阅或管理组层次结构中的其他策略分配，则还会显示每个分配的事件。 通过评估 `data.policyDefinitionId` 属性来确认事件是否对应于本教程中的分配。

## <a name="trigger-a-change-on-the-resource-group"></a>触发资源组的更改

若要使资源组合规，需要添加一个名为 **EventTest** 的标记。 使用以下命令将标记添加到资源组（请将 `<SubscriptionID>` 替换为你的订阅 ID，将 `<ResourceGroup>` 替换为资源组的名称）：

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

将所需标记添加到资源组后，等待 **Microsoft.PolicyInsights.PolicyStateChanged** 事件通知出现在 Web 应用中。 展开该事件，`data.complianceState` 值现在显示为 _Compliant_。

## <a name="clean-up-resources"></a>清理资源

如果你打算继续使用此 Web 应用和 Azure Policy 事件订阅，请不要清理在本文中创建的资源。 如果不打算继续学习，请使用以下命令删除本文中创建的资源。

将 `<resource_group_name>` 替换为上面创建的资源组。

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>后续步骤

现在你已了解如何为 Azure Policy 创建主题和事件订阅，接下来请详细了解策略状态更改事件以及事件网格的功能：

- [对 Azure Policy 状态更改事件做出反应](../concepts/event-overview.md)
- [事件网格的 Azure Policy 架构详细信息](../../../event-grid/event-schema-policy.md)
- [关于事件网格](../../../event-grid/overview.md)
