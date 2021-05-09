---
title: 为 Azure 服务总线队列和订阅启用死信
description: 本文介绍如何通过使用 Azure 门户、PowerShell、CLI 和编程语言（C#、Java、Python 和 JavaScript）为队列和订阅启用死信
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 789f9221c224a70225849d05b736276b95ebfc11
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107989263"
---
# <a name="enable-dead-lettering-on-message-expiration-for-azure-service-bus-queues-and-subscriptions"></a>为 Azure 服务总线队列和订阅启用消息过期死信
Azure 服务总线队列和主题订阅提供名为“死信队列”(DLQ) 的辅助子队列。 死信队列不需要显式创建，并且不能删除或独立于主实体进行管理。 死信队列的用途是存放无法传递给任何接收方的消息或无法处理的消息。 有关详细信息，请参阅[服务总线死信队列概述](service-bus-dead-letter-queues.md)。 本文介绍为服务总线队列和订阅启用死信的不同方式。 

## <a name="using-azure-portal"></a>使用 Azure 门户
在 Azure 门户中创建队列或主题订阅时，请选择“为消息过期启用死信”，如以下示例中所示。 

### <a name="create-a-queue-with-dead-lettering-enabled"></a>创建启用死信的队列
:::image type="content" source="./media/enable-dead-letter/create-queue.png" alt-text="在创建队列时启用死信":::

### <a name="create-a-subscription-with-dead-lettering-enabled"></a>创建启用死信的订阅
:::image type="content" source="./media/enable-dead-letter/create-subscription.png" alt-text="在创建订阅时启用死信":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-queue"></a>更新现有队列消息过期的死信设置
在服务总线队列的“概述”页面上，选择消息过期死信设置的当前值。 以下示例返回的当前值为“禁用”。 你可以在弹出窗口中启用或禁用消息过期死信。 

:::image type="content" source="./media/enable-dead-letter/queue-configuration.png" alt-text="为现有队列启用消息过期死信":::

### <a name="update-the-dead-lettering-on-message-expiration-setting-for-an-existing-subscription"></a>更新现有订阅消息过期的死信设置
在服务总线订阅的“概述”页面上，选择消息过期死信设置的当前值。 以下示例返回的当前值为“禁用”。 你可以在弹出窗口中启用或禁用消息过期死信。 

:::image type="content" source="./media/enable-dead-letter/subscription-configuration.png" alt-text="为现有订阅启用消息过期死信":::

## <a name="using-azure-cli"></a>使用 Azure CLI
若要创建已启用消息过期死信的队列，请使用 [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) 命令将 `--enable-dead-lettering-on-message-expiration` 设置为 `true`。 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```

若要为现有队列启用消息过期死信设置，请使用 [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) 命令将 `--enable-dead-lettering-on-message-expiration` 设置为 `true`。 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-dead-lettering-on-message-expiration true
```


若要创建已启用消息过期死信的主题订阅，请使用 [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) 命令将 `--enable-dead-lettering-on-message-expiration` 设置为 `true`。

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

若要为主题订阅启用消息过期死信设置，请使用 [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) 命令将 `--enable-dead-lettering-on-message-expiration` 设置为 `true`。

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-dead-lettering-on-message-expiration true
```

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
若要创建已启用消息过期死信的队列，请使用 [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) 命令将 `-DeadLetteringOnMessageExpiration` 设置为 `$True`。 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -DeadLetteringOnMessageExpiration $True
```

若要为现有队列启用消息过期死信设置，请使用 [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) 命令，如下方示例中所示。

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

若要创建已启用消息过期死信的主题订阅，请使用 [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) 命令将 `-DeadLetteringOnMessageExpiration` 设置为 `$True`。 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -DeadLetteringOnMessageExpiration $True
```

若要为现有订阅启用消息过期死信设置，请参阅下方示例。

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.DeadLetteringOnMessageExpiration=$True

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
若要创建已启用消息过期死信的队列，请在“队列属性”部分将 `deadLetteringOnMessageExpiration` 设置为 `true`。 有关详细信息，请参阅 [Microsoft.ServiceBus 命名空间/队列模板参考](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)。 

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
            "deadLetteringOnMessageExpiration": true
          }
        }
      ]
    }
  ]
}

```

若要创建已启用消息过期死信的主题订阅，请在“队列属性”部分将 `deadLetteringOnMessageExpiration` 设置为 `true`。 有关详细信息，请参阅 [Microsoft.ServiceBus 命名空间/主题/订阅模板参考](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)。 

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
    "serviceBusSubscriptionName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Subscription"
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
            "maxSizeInMegabytes": 1024
          },
          "resources": [
            {
              "apiVersion": "2017-04-01",
              "name": "[parameters('serviceBusSubscriptionName')]",
              "type": "Subscriptions",
              "dependsOn": [
                "[parameters('serviceBusTopicName')]"
              ],
              "properties": {
                "deadLetteringOnMessageExpiration": true
              }
            }
          ]
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>后续步骤
尝试采用所选语言的示例，了解 Azure 服务总线功能。 

- [适用于 .NET（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) 
- [适用于 Java（最新）的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [适用于 Python 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [适用于 JavaScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [适用于 TypeScript 的 Azure 服务总线客户端库示例](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

在下面查找早期 .NET 和 Java 客户端库示例：
- [适用于 .NET（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [适用于 Java（旧版）的 Azure 服务总线客户端库示例](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus)