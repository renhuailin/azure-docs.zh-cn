---
author: baanders
description: 包含描述如何设置事件网格终结点和路由的文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/21/2021
ms.author: baanders
ms.openlocfilehash: 0311f53845a4ee18bf595d9efe2593d486818fb8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748556"
---
### <a name="create-the-event-grid-topic"></a>创建事件网格主题

[事件网格](../articles/event-grid/overview.md)是一项 Azure 服务，可帮助将来自 Azure 服务的事件路由和传递到 Azure 中的其他位置。 你可以创建一个[事件网格主题](../articles/event-grid/concepts.md)从源收集特定事件，然后订阅者可以侦听该主题，以便在事件送达时接收事件。

在 Azure Cloud Shell 中运行以下命令，以创建事件网格主题：

```azurecli-interactive
az eventgrid topic create --resource-group <your-resource-group> --name <name-for-your-event-grid-topic> --location <region>
```

此命令的输出是已创建的事件网格主题的相关信息。 保存为事件网格主题提供的名称，稍后需要使用。

### <a name="create-the-endpoint"></a>创建终结点

接下来，在 Azure 数字孪生中创建事件网格终结点，该终结点将实例连接到事件网格主题。 使用下方的命令，填写事件网格主题的名称并根据需要填写其他占位符字段。

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

此命令的输出是已创建的终结点的相关信息。

在输出中查找 `provisioningState` 字段，检查其值是否为“Succeeded”。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/output-endpoints.png" alt-text="Azure 门户的 Cloud Shell 中终结点查询结果的屏幕截图，其中显示了 provisioningState 为“成功”的终结点。":::

它也可能显示“Provisioning”，这意味着仍在创建终结点。 如果是这样，请等待几秒钟，并运行以下命令来检查终结点的状态。 重复此操作，直到 `provisioningState` 显示“Succeeded”。

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

保存终结点的名称，稍后需要使用。

### <a name="create-the-route"></a>创建路由

接下来，创建 Azure 数字孪生路由，将事件发送到刚刚创建的事件网格终结点。 

可以使用以下 CLI 命令来完成此操作（填写终结点的名称并根据需要填写其他占位符字段）。 此命令将转发孪生体图中发生的所有事件。 如果需要，可以使用[筛选器](../articles/digital-twins/how-to-manage-routes.md?tabs=portal%2Cportal2%2Cportal3#filter-events)将事件仅筛选为特定事件。

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

此命令的输出是一些有关已创建的路由的信息。

>[!NOTE]
>必须先完成终结点（来自上一步）的预配工作，然后才能设置使用这些终结点的事件路由。 如果由于终结点未就绪而导致路由创建失败，请等待几分钟，然后重试。