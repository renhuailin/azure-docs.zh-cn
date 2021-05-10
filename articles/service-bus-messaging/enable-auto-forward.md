---
title: 为 Azure 服务总线队列和订阅启用自动转发
description: 本文介绍如何通过使用 Azure 门户、PowerShell、CLI 和编程语言（C#、Java、Python 和 JavaScript）为队列和订阅启用自动转发
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 0271ac825c192fcfe9db4a4b6378a47c62dc9903
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107988111"
---
# <a name="enable-auto-forwarding-for-azure-service-bus-queues-and-subscriptions"></a>为 Azure 服务总线队列和订阅启用自动转发
通过服务总线自动转发功能可将队列或订阅链接到同一命名空间中的另一个队列或主题。 启用自动转发时，服务总线会自动删除放置在第一个队列或订阅（源）中的消息，并将其放入第二个队列或主题（目标）中。 仍可将消息直接发送到目标实体。 有关详细信息，请参阅[使用自动转发链接服务总线实体](service-bus-auto-forwarding.md)。 本文介绍为服务总线队列和订阅启用自动转发的不同方式。 

> [!IMPORTANT]
> 服务总线的基本层不支持自动转发功能。 标准层和高级层支持该功能。 有关这些层之间的差异，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

## <a name="using-azure-portal"></a>使用 Azure 门户
在 Azure 门户中为主题创建队列或订阅时，请选择“将消息转发到队列/主题”，如以下示例中所示。 然后指定是否要将消息转发到队列或主题。 在本示例中，选择了“队列”选项，并选择了同一命名空间中的队列。

### <a name="create-a-queue-with-auto-forwarding-enabled"></a>创建已启用自动转发的队列
:::image type="content" source="./media/enable-auto-forward/create-queue.png" alt-text="在创建队列时启用自动转发":::

### <a name="create-a-subscription-for-a-topic-with-auto-forwarding-enabled"></a>为已启用自动转发的主题创建订阅
:::image type="content" source="./media/enable-auto-forward/create-subscription.png" alt-text="在创建订阅时启用自动转发":::

### <a name="update-the-auto-forward-setting-for-an-existing-queue"></a>更新现有队列的自动转发设置
在服务总线队列的“概述”页上，为“邮件转发对象”设置选择当前值 。 在以下示例中，当前值为“禁用”。 在“向队列/主题转发邮件”窗口中，可以选择要将邮件转发到的队列或主题。 

:::image type="content" source="./media/enable-auto-forward/queue-auto-forward.png" alt-text="为现有队列启用自动转发":::

### <a name="update-the-auto-forward-setting-for-an-existing-subscription"></a>更新现有订阅的自动转发设置
在服务总线订阅的“概述”页上，为“邮件转发对象”设置选择当前值 。 在以下示例中，当前值为“禁用”。 在“向队列/主题转发邮件”窗口中，可以选择要将邮件转发到的队列或主题。 

:::image type="content" source="./media/enable-auto-forward/subscription-auto-forward.png" alt-text="为现有订阅启用自动转发":::

## <a name="using-azure-cli"></a>使用 Azure CLI
若要创建已启用自动转发的队列，请使用 `--forward-to` 设置为要将消息转发到的队列或主题的名称的 [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) 命令。 

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```

若要更新现有队列的自动转发设置，请使用 [`az servicebus queue update`](/cli/azure/servicebus/queue#az_servicebus_queue_update) 命令，并将 `--forward-to` 设置为要将邮件转发到的队列或主题的名称。 

```azurecli-interactive
az servicebus queue update \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --forward-to myqueue2
```


若要为已启用自动转发的主题创建订阅，请使用 [`az servicebus topic subscription create`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_create) 命令，并将 `--forward-to` 设置为要将邮件转发到的队列或主题的名称。

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

若要更新主题订阅的自动转发设置，请使用 [`az servicebus topic subscription update`](/cli/azure/servicebus/topic/subscription#az_servicebus_topic_subscription_update) 命令，并将 `--forward-to` 设置为要将邮件转发到的队列或主题的名称。

```azurecli-interactive
az servicebus topic subscription create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --topic-name mytopic \
    --name mysubscription \
    --forward-to myqueue2
```

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
若要创建已启用自动转发的队列，请使用 `-ForwardTo` 设置为要将消息转发到的队列或主题的名称的 [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) 命令。 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -ForwardTo myqueue2
```

若要更新现有队列的自动转发设置，请使用 [`Set-AzServiceBusQueue`](/powershell/module/az.servicebus/set-azservicebusqueue) 命令，如以下示例所示。

```azurepowershell-interactive
$queue=Get-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue 

$queue.ForwardTo='myqueue2'

Set-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -QueueObj $queue
``` 

若要为已启用自动转发的主题创建订阅，请使用 `-ForwardTo` 设置为要将消息转发到的队列或主题的名称的 [`New-AzServiceBusSubscription`](/powershell/module/az.servicebus/new-azservicebussubscription) 命令。

```azurepowershell-interactive
New-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysubscription `
    -ForwardTo myqueue2
```

若要更新现有订阅的自动转发设置，请参阅以下示例。

```azurepowershell-interactive
$subscription=Get-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -TopicName mytopic `
    -SubscriptionName mysub

$subscription.ForwardTo='mytopic2'

Set-AzServiceBusSubscription -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -SubscriptionName mysub `
    -SubscriptionObj $subscription 
```

## <a name="using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
若要创建已启用自动转发的队列，请在“队列属性”部分中将 `forwardTo` 设置为要将消息转发到的队列或主题的名称。 有关详细信息，请参阅 [Microsoft.ServiceBus 命名空间/队列模板参考](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)。 

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
            "forwardTo": "myqueue2"
          }
        }
      ]
    }
  ]
}

```

若要为已启用自动转发的主题创建订阅，请在“队列属性”部分中将 `forwardTo` 设置为要将消息转发到的队列或主题的名称。 有关详细信息，请参阅 [Microsoft.ServiceBus 命名空间/主题/订阅模板参考](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions?tabs=json)。 

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
                "forwardTo": "myqueue2"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="net"></a>.NET 

### <a name="azuremessagingservicebus-latest"></a>Azure.Messaging.ServiceBus（最新版）
可以通过以下方法启用自动转发功能：设置 [CreateQueueOptions.ForwardTo](/dotnet/api/azure.messaging.servicebus.administration.createqueueoptions.forwardto) 或 [CreateSubscriptionOptions.ForwardTo](/dotnet/api/azure.messaging.servicebus.administration.createsubscriptionoptions.forwardto)，然后使用采用 `CreateQueueOptions` 或 `CreateSubscriptionOptions` 参数的 [CreateQueueAsync](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient.createqueueasync#Azure_Messaging_ServiceBus_Administration_ServiceBusAdministrationClient_CreateQueueAsync_Azure_Messaging_ServiceBus_Administration_CreateQueueOptions_System_Threading_CancellationToken_) 或 [CreateSubscriptionAsync](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient.createsubscriptionasync#Azure_Messaging_ServiceBus_Administration_ServiceBusAdministrationClient_CreateSubscriptionAsync_Azure_Messaging_ServiceBus_Administration_CreateSubscriptionOptions_System_Threading_CancellationToken_) 方法。 

### <a name="microsoftazureservicebus-legacy"></a>Microsoft.Azure.ServiceBus（旧版）
可以通过设置源的 [QueueDescription.ForwardTo](/dotnet/api/microsoft.servicebus.messaging.queuedescription) 或 [SubscriptionDescription.ForwardTo](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) 来启用自动转发，如以下示例所示：

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

## <a name="java"></a>Java

### <a name="azure-messaging-servicebus-latest"></a>azure-messaging-servicebus（最新）
可以通过以下方法启用自动转发功能：使用 [CreateQueueOptions.setForwardTo(String forwardTo)](/java/api/com.azure.messaging.servicebus.administration.models.createqueueoptions.setforwardto) 方法或 [CreateSubscriptionOptions.setForwardTo(String forwardTo)](/java/api/com.azure.messaging.servicebus.administration.models.createsubscriptionoptions.setforwardto)，然后使用采用 `CreateQueueOptions` 或 `CreateSubscriptionOptions` 参数的 [createQueue](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient.createqueue#com_azure_messaging_servicebus_administration_ServiceBusAdministrationClient_createQueue_java_lang_String_com_azure_messaging_servicebus_administration_models_CreateQueueOptions_) 方法或 [createSubscription](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient.createsubscription#com_azure_messaging_servicebus_administration_ServiceBusAdministrationClient_createSubscription_java_lang_String_java_lang_String_com_azure_messaging_servicebus_administration_models_CreateSubscriptionOptions_) 方法。 

### <a name="azure-servicebus-legacy"></a>azure-servicebus（旧版）
可以使用源的 [QueueDescription.setForwardTo(String forwardTo)](/java/api/com.microsoft.azure.servicebus.management.queuedescription.setforwardto#com_microsoft_azure_servicebus_management_QueueDescription_setForwardTo_java_lang_String_) 或 [SubscriptionDescription.setForwardTo(String forwardTo)](/java/api/com.microsoft.azure.servicebus.management.subscriptiondescription.setforwardto) 来启用自动转发。 


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