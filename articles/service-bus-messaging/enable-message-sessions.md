---
title: 启用 Azure 服务总线消息会话 | Microsoft Docs
description: 本文介绍如何通过使用 Azure 门户、PowerShell、CLI 和编程语言（C#、Java、Python 和 JavaScript）启用消息会话
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0a39e601d675225feb7e82e1de81eee9d3887aeb
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671420"
---
# <a name="enable-message-sessions-for-an-azure-service-bus-queue-or-a-subscription"></a>为 Azure 服务总线队列或订阅启用消息会话
使用 Azure 服务总线会话，可以连贯有序的方式处理一系列无限多的相关消息。 可以在“先进先出 (FIFO)”和“请求-响应”模式下使用会话。 有关详细信息，请参阅[消息会话](message-sessions.md)。 本文介绍为服务总线队列或订阅启用会话的不同方式。 

> [!IMPORTANT]
> - 服务总线的基本层不支持会话。 标准层和高级层支持会话。 有关这些层之间的差异，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。
> - 在创建队列或订阅后，不能启用或禁用消息会话。 只能在创建队列或订阅时执行此操作。 

## <a name="using-azure-portal"></a>使用 Azure 门户
在 Azure 门户中创建“队列”时，请选择“启用会话”，如下图所示。 

:::image type="content" source="./media/message-sessions/queue-sessions.png" alt-text="在创建队列时启用会话":::

在 Azure 门户中为主题创建订阅时，请选择“启用会话”，如下图所示。 

:::image type="content" source="./media/message-sessions/subscription-sessions.png" alt-text="在创建订阅时启用会话":::

## <a name="using-azure-cli"></a>使用 Azure CLI
若要创建已启用消息会话的队列，请使用 `--enable-session` 设置为 `true` 的 [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) 命令。

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-session true
```

若要为已启用消息会话的主题创建订阅，请使用 `--enable-session` 设置为 `true` 的 [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) 命令。

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --enable-session true
```

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
若要创建已启用消息会话的队列，请使用 `-RequiresSession` 设置为 `$True` 的 [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) 命令。 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -RequiresSession $True
```

若要为已启用消息会话的主题创建订阅，请使用 `-RequiresSession` 设置为 `true` 的 [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) 命令。 

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -RequiresSession $True
```

## <a name="using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
若要创建已启用消息会话的队列，请在“队列属性”部分中将 `requiresSession` 设置为 `true`。 有关详细信息，请参阅 [Microsoft.ServiceBus 命名空间/队列模板参考](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)。 

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
            "requiresSession": true
          }
        }
      ]
    }
  ]
}

```

若要为已启用消息会话的主题创建订阅，请在“订阅属性”部分中将 `requiresSession` 设置为 `true`。 有关详细信息，请参阅 [Microsoft.ServiceBus 命名空间/主题/订阅模板参考](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)。 

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
                "requiresSession": true
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
