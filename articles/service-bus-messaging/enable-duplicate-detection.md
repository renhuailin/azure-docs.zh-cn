---
title: 启用重复消息检测 - Azure 服务总线
description: 本文介绍如何通过使用 Azure 门户、PowerShell、CLI 和编程语言（C#、Java、Python 和 JavaScript）启用重复消息检测
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 708009fcf2479660316b38ac0b7d545d450de28c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755037"
---
# <a name="enable-duplicate-message-detection-for-an-azure-service-bus-queue-or-a-topic"></a>为 Azure 服务总线队列或主题启用重复消息检测
为队列或主题启用重复检测时，Azure 服务总线将保留在配置的时间内发送到队列或主题的所有消息的历史记录。 在此间隔期间，队列或主题将不会存储任何重复的消息。 启用此属性可保证在用户定义的时间段内准确地传递一次。 有关详细信息，请参阅[重复检测](duplicate-detection.md)。 本文介绍为服务总线队列或主题启用重复消息检测的不同方式。 


> [!NOTE]
> - 基本层的服务总线不支持重复检测。 标准层和高级层支持重复检测。 有关这些层之间的差异，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。
> - 创建队列或主题后，无法启用或禁用重复检测。 只能在创建队列或主题时执行此操作。 

## <a name="using-azure-portal"></a>使用 Azure 门户
在 Azure 门户中创建“队列”时，请选择“启用重复检测”，如下图所示。 你可以在创建队列或主题时配置重复检测时间窗口的大小。 

:::image type="content" source="./media/enable-duplicate-detection/create-queue.png" alt-text="在创建队列时启用重复检测":::

在 Azure 门户中创建主题时，请选择“启用重复检测”，如下图所示。 

:::image type="content" source="./media/enable-duplicate-detection/create-topic.png" alt-text="在创建主题时启用重复检测":::

如果你在创建时启用了重复检测，也可以为现有队列或主题配置此设置。 

### <a name="update-duplicate-detection-window-size-for-an-existing-queue-or-a-topic"></a>为现有队列或主题更新重复检测时间窗口大小
若要为现有队列或主题更改重复检测时间窗口大小，请在“概述”页上，为“重复检测时间窗口”选择“更改” 。  

#### <a name="queue"></a>队列
:::image type="content" source="./media/enable-duplicate-detection/window-size.png" alt-text="为队列设置重复检测时间窗口大小":::

#### <a name="topic"></a>主题
:::image type="content" source="./media/enable-duplicate-detection/window-size-topic.png" alt-text="为主题设置重复检测时间窗口大小":::


## <a name="using-azure-cli"></a>使用 Azure CLI
若要创建已启用重复检测的队列，请使用 `--enable-duplicate-detection` 设置为 `true` 的 [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) 命令。 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

若要创建已启用重复检测的主题，请使用 `--enable-duplicate-detection` 设置为 `true` 的 [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) 命令。

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-duplicate-detection true \
    --duplicate-detection-history-time-window P1D
```

上述示例还使用 `--duplicate-detection-history-time-window` 参数设置重复检测时间窗口的大小。 时间窗口大小设置为 1 天。 默认值为 10 分钟，允许的最大值为 7 天。

若要使用新的检测时间窗口大小更新队列，请使用带有 `--duplicate-detection-history-time-window` 参数的 [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) 命令。 在本示例中，时间窗口大小更新为 7 天。 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```

同样，若要使用新的检测时间窗口大小更新主题，请使用带有 `--duplicate-detection-history-time-window` 参数的 [`az servicebus topic update`](/cli/azure/servicebus/topic#az_servicebus_topic_update) 命令。 在本示例中，时间窗口大小更新为 7 天。

```azurecli-interactive
az servicebus topic update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --duplicate-detection-history-time-window P7D
```
 
## <a name="using-azure-powershell"></a>使用 Azure PowerShell
若要创建已启用重复检测的队列，请使用 `-RequiresDuplicateDetection` 设置为 `$True` 的 [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) 命令。 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresDuplicateDetection $True `
    -DuplicateDetectionHistoryTimeWindow P1D
```

若要创建已启用重复检测的主题，请使用 `-RequiresDuplicateDetection` 设置为 `true` 的 [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) 命令。 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -RequiresDuplicateDetection $True
    -DuplicateDetectionHistoryTimeWindow P1D
```

上述示例还使用 `-DuplicateDetectionHistoryTimeWindow` 参数设置重复检测时间窗口的大小。 时间窗口大小设置为 1 天。 默认值为 10 分钟，允许的最大值为 7 天。

若要使用新的检测时间窗口大小更新队列，请参阅以下示例。  在本示例中，时间窗口大小更新为 7 天。

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
```

若要使用新的检测时间窗口大小更新主题，请参阅以下示例。 在本示例中，时间窗口大小更新为 7 天。

```azurepowershell-interactive
$topic=Get-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic

$topic.DuplicateDetectionHistoryTimeWindow='P7D'

Set-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -TopicObj $topic
```

## <a name="using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
若要创建已启用重复检测的队列，请在“队列属性”部分中将 `requiresDuplicateDetection` 设置为 `true`。 有关详细信息，请参阅 [Microsoft.ServiceBus 命名空间/队列模板参考](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)。 为 `duplicateDetectionHistoryTimeWindow` 属性指定一个值，以设置重复检测时间窗口的大小。 在以下示例中，大小设置为 1 天。  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.ServiceBus/namespaces",
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('serviceBusNamespaceName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "type": "Queues",
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}

```

若要创建已启用重复检测的主题，请在“主题属性”部分中将 `requiresDuplicateDetection` 设置为 `true`。 有关详细信息，请参阅 [Microsoft.ServiceBus 命名空间/主题模板参考](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json)。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "service_BusNamespace_Name": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusTopicName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Topic"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-01-01-preview",
      "name": "[parameters('service_BusNamespace_Name')]",
      "type": "Microsoft.ServiceBus/namespaces",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusTopicName')]",
          "type": "topics",
          "dependsOn": [
            "[resourceId('Microsoft.ServiceBus/namespaces/', parameters('service_BusNamespace_Name'))]"
          ],
          "properties": {
            "requiresDuplicateDetection": true,
            "duplicateDetectionHistoryTimeWindow": "P1D"
          }
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>后续步骤
尝试采用所选语言的示例，了解 Azure 服务总线功能。 

- [适用于 Java 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [适用于 Python 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [适用于 JavaScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [适用于 TypeScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [适用于 .NET 的 Azure.Messaging.ServiceBus 示例](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

在下面查找早期 .NET 和 Java 客户端库示例：
- [适用于 .NET 的 Microsoft.Azure.ServiceBus 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [适用于 Java 的 azure-servicebus 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)