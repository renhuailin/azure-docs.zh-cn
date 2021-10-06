---
title: 使用事件域向 Azure 事件网格发布事件
description: 展示了如何使用事件域管理 Azure 事件网格中的大型主题集并向其发布事件。
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 34109db5d39daed14fc008be12a8785d255a9838
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232018"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>使用事件域管理主题和发布事件

本文介绍以下操作：

* 创建事件网格域
* 订阅事件网格主题
* 列出密钥
* 将事件发布到域

若要了解事件域，请参阅[了解用于管理事件网格主题的事件域](event-domains.md)。

## <a name="create-an-event-domain"></a>创建事件域

若要管理大型主题集，请创建一个事件域。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```azurepowershell-interactive
New-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```
---

创建成功后会返回以下值：

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

请记下 `endpoint` 和 `id`，因为需要使用它们来管理域和发布事件。

## <a name="manage-access-to-topics"></a>管理对主题的访问

可通过[角色分配](../role-based-access-control/role-assignments-cli.md)来管理对主题的访问。 角色分配使用 Azure 基于角色的访问控制来限制对 Azure 资源的操作，仅允许经授权用户在特定范围内执行操作。

事件网格包含两个内置角色，可以使用这些角色为特定用户分配对域中不同主题的访问权限。 这些角色为 `EventGrid EventSubscription Contributor (Preview)` 和 `EventGrid EventSubscription Reader (Preview)`，分别用于创建/删除订阅，以及只允许列出事件订阅。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
以下 Azure CLI 命令将 `alice@contoso.com` 限制为只能在主题 `demotopic1` 上创建或删除事件订阅：

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
以下 PowerShell 命令将 `alice@contoso.com` 限制为只能在主题 `demotopic1` 上创建或删除事件订阅：

```azurepowershell-interactive
New-AzRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```
---

有关管理对事件网格操作的访问权限的详细信息，请参阅[事件网格安全性和身份验证](./security-authentication.md)。

## <a name="create-topics-and-subscriptions"></a>创建主题和订阅

事件网格服务根据创建域主题事件订阅的调用，在域中自动创建和管理相应的主题。 没有单独的步骤可在域中创建主题。 同样，删除某个主题的最后一个事件订阅时，也会删除该主题。

订阅域中主题的过程与订阅其他任何 Azure 资源相同。 对于源资源 ID，指定之前在创建域时返回的事件域 ID。 若要指定想要订阅的主题，请将 `/topics/<my-topic>` 添加到源资源 ID 的末尾。 若要创建接收域中的所有事件的域范围事件订阅，请指定事件域 ID 且不要指定任何主题。

通常情况下，将由你在前面的部分中向其授予了访问权限的用户创建订阅。 为了简化本文，将由你创建订阅。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```azurepowershell-interactive
New-AzEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

---

如果需要将事件订阅到某个测试终结点，始终可以部署能够显示传入事件的[预生成 Web 应用](https://github.com/Azure-Samples/azure-event-grid-viewer)。 可将事件发送到测试网站 (`https://<your-site-name>.azurewebsites.net/api/updates`)。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>

为主题设置的权限存储在 Azure Active Directory 中，必须显式删除。 如果用户对主题拥有写访问权限，则删除事件订阅不会撤销用户创建事件订阅的访问权限。


## <a name="publish-events-to-an-event-grid-domain"></a>将事件发布到事件网格域

将事件发布到域的过程与[发布到自定义主题](./post-to-custom-topic.md)相同。 但是，不是发布到自定义主题，而是将所有事件发布到域终结点。 在 JSON 事件数据中，可以指定要将事件发送到的主题。 例如，以下事件数组会导致将包含 `"id": "1111"` 的事件发送到主题 `demotopic1`，将包含 `"id": "2222"` 的事件发送到主题 `demotopic2`：

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)
若要使用 Azure CLI 获取域终结点，请使用：

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

若要获取域的密钥，请使用：

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)
若要使用 PowerShell 获取域终结点，请使用：

```azurepowershell-interactive
Get-AzEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

若要获取域的密钥，请使用：

```azurepowershell-interactive
Get-AzEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```
---

然后，使用偏好的方法发出 HTTP POST，将事件发布到事件网格域。

## <a name="search-lists-of-topics-or-subscriptions"></a>搜索主题或订阅的列表

若要搜索和管理大量主题或订阅，事件网格的 API 支持列表和分页。

### <a name="using-cli"></a>使用 CLI
例如，以下命令列出名称包含 `mytopic` 的所有主题。 

```azurecli-interactive
az eventgrid topic list --odata-query "contains(name, 'mytopic')"
```

有关此命令的详细信息，请参阅 [`az eventgrid topic list`](/cli/azure/eventgrid/topic?#az_eventgrid_topic_list)。 


## <a name="next-steps"></a>后续步骤

* 有关事件域中的高级概念及其作用的详细信息，请参阅[事件域的概念概述](event-domains.md)。
