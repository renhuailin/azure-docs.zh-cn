---
title: 在 Azure 服务总线队列和主题中启用分区
description: 本文介绍如何在 Azure 服务总线队列和主题中通过使用 Azure 门户、PowerShell、CLI 和编程语言（C#、Java、Python 和 JavaScript）启用分区
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: fb704f784d490cb73c14fc73b1a6c4368d16acbc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107755034"
---
# <a name="enable-partitioning-for-an-azure-service-bus-queue-or-a-topic"></a>为 Azure 服务总线队列或主题启用分区
服务总线分区允许跨多个消息中转站和消息存储对队列和主题或消息实体进行分区。 分区意味着分区实体的总吞吐量不再受单个消息中转站或消息存储的性能所限制。 此外，消息传送存储的临时中断不会导致分区的队列或主题不可用。 分区队列和主题可以包含所有先进的服务总线功能，如事务和会话支持。 有关详细信息，请参阅[分区队列和主题](service-bus-partitioning.md)。 本文介绍为服务总线队列或主题启用重复消息检测的不同方式。 

> [!IMPORTANT]
> - 分区在为基本或标准 SKU 中的所有队列和主题创建实体时可用。 它不可用于高级消息传送 SKU，但高级命名空间中任何先前已存在的分区实体将继续按预期方式工作。
> - 不能更改任何现有队列或主题中的分区选项。 只能在创建队列或主题时设置选项。 

## <a name="using-azure-portal"></a>使用 Azure 门户
在 Azure 门户中创建“队列”时，请选择“启用分区”，如下图所示。 

:::image type="content" source="./media/enable-partitions/create-queue.png" alt-text="在创建队列时启用分区":::

在 Azure 门户中创建主题时，请选择“启用分区”，如下图所示。 

:::image type="content" source="./media/enable-partitions/create-topic.png" alt-text="在创建主题时启用分区":::

## <a name="using-azure-cli"></a>使用 Azure CLI
若要创建已启用分区的队列，请使用 `--enable-partitioning` 设置为 `true` 的 [`az servicebus queue create`](/cli/azure/servicebus/queue#az_servicebus_queue_create) 命令。

```azurecli-interactive
az servicebus queue create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name myqueue \
    --enable-partitioning true
```

若要创建已启用分区的主题，请使用 `--enable-partitioning` 设置为 `true` 的 [`az servicebus topic create`](/cli/azure/servicebus/topic#az_servicebus_topic_create) 命令。

```azurecli-interactive
az servicebus topic create \
    --resource-group myresourcegroup \
    --namespace-name mynamespace \
    --name mytopic \
    --enable-partitioning true
```

## <a name="using-azure-powershell"></a>使用 Azure PowerShell
若要创建已启用分区的队列，请使用 `-EnablePartitioning` 设置为 `$True` 的 [`New-AzServiceBusQueue`](/powershell/module/az.servicebus/new-azservicebusqueue) 命令。 

```azurepowershell-interactive
New-AzServiceBusQueue -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -QueueName myqueue `
    -EnablePartitioning $True
```

若要创建已启用分区的主题，请使用 `-EnablePartitioning` 设置为 `true` 的 [`New-AzServiceBusTopic`](/powershell/module/az.servicebus/new-azservicebustopic) 命令。 

```azurepowershell-interactive
New-AzServiceBusTopic -ResourceGroup myresourcegroup `
    -NamespaceName mynamespace `
    -Name mytopic `
    -EnablePartitioning $True
```

## <a name="using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
若要创建已启用分区的队列，请在“队列属性”部分中将 `enablePartitioning` 设置为 `true`。 有关详细信息，请参阅 [Microsoft.ServiceBus 命名空间/队列模板参考](/azure/templates/microsoft.servicebus/namespaces/queues?tabs=json)。 

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
            "enablePartitioning": true
          }
        }
      ]
    }
  ]
}

```

若要创建已启用重复检测的主题，请在“主题属性”部分中将 `enablePartitioning` 设置为 `true`。 有关详细信息，请参阅 [Microsoft.ServiceBus 命名空间/主题模板参考](/azure/templates/microsoft.servicebus/namespaces/topics?tabs=json)。 

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
            "enablePartitioning": true
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