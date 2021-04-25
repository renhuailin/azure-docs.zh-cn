---
title: 将托管标识添加到 Azure 事件网格目标上的角色
description: 本文介绍如何将托管标识添加到 Azure 服务总线和 Azure 事件中心等目标上的 Azure 角色。
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1578e4c24201614ce89351b3c3cee52a09cadc30
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280473"
---
# <a name="grant-managed-identity-the-access-to-event-grid-destination"></a>向托管标识授予对事件网格目标的访问权限
本部分介绍如何将系统主题、自定义主题或域的标识添加到 Azure 角色。 

## <a name="prerequisites"></a>先决条件
按照下文说明分配系统所分配的托管标识：

- [自定义主题或域](enable-identity-custom-topics-domains.md)
- [系统主题](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>支持的目标和 Azure 角色
为事件网格自定义主题或域启用标识后，Azure 会自动在 Azure Active Directory 中创建标识。 将此标识添加到适当的 Azure 角色，使自定义主题或域可以将事件转发到受支持的目标。 例如，将标识添加到 Azure 事件中心命名空间的“Azure 事件中心数据发送方”角色，使事件网格自定义主题可以将事件转发到该命名空间中的事件中心。 

当前，Azure 事件网格支持配置有系统分配的托管标识的自定义主题或域，允许将事件转发到以下目标。 此表还为你提供标识应位于的角色，以便自定义主题可以转发事件。

| 目标 | Azure 角色 | 
| ----------- | --------- | 
| 服务总线队列和主题 | [Azure 服务总线数据发送方](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure 事件中心 | [Azure 事件中心数据发送方](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob 存储 | [存储 Blob 数据参与者](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure 队列存储 |[存储队列数据消息发送方](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>使用 Azure 门户
你可以使用 Azure 门户将自定义主题或域标识分配给相应的角色，让自定义主题或域可以将事件转发到目标。 

以下示例将名为“msitesttopic”的事件网格自定义主题的托管标识添加到包含队列或主题资源的服务总线命名空间的“Azure 服务总线数据发送方”角色 。 在命名空间级别添加到角色时，事件网格自定义主题可以将事件转发到命名空间中的所有实体。 

1. 在 [Azure 门户](https://portal.azure.com)中，转到“服务总线命名空间”。 
1. 在左窗格中选择“访问控制”。 
1. 在“添加角色分配”部分中选择“添加” 。 
1. 在“添加角色分配”页上执行以下步骤：
    1. 选择角色。 在本例中，它是“Azure 服务总线数据发送方”。 
    1. 为事件网格自定义主题或域选择“标识”。 
    1. 选择“保存”以保存配置。

这些步骤与向表中提到的其他角色添加标识相似。 

## <a name="use-the-azure-cli"></a>使用 Azure CLI
本部分中的示例展示了如何使用 Azure CLI 向 Azure 角色添加标识。 示例命令适用于事件网格自定义主题。 适用于事件网格域的命令也是类似的。 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>获取自定义主题的系统标识的主体 ID 
首先，获取自定义主题的系统托管标识的主体 ID，并将该标识分配给相应的角色。

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>在不同范围内创建事件中心的角色分配 
以下 CLI 示例演示如何在命名空间级别或事件中心级别将自定义主题的标识添加到“Azure 事件中心数据发送方”角色。 如果在命名空间级别创建角色分配，则自定义主题可以将事件转发到该命名空间中的所有事件中心。 如果在事件中心级别创建角色分配，则自定义主题只能将事件转发到该特定事件中心。 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>在不同的范围内创建服务总线主题的角色分配 
以下 CLI 示例演示如何在命名空间级别或服务总线主题级别将事件网格自定义主题的标识添加到“Azure 服务总线数据发送方”角色。 如果在命名空间级别创建角色分配，则事件网格主题可以将事件转发到该命名空间中的所有实体（服务总线队列或主题）。 如果在服务总线队列或主题级别创建角色分配，则事件网格自定义主题只能将事件转发到该特定的服务总线队列或主题。 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>后续步骤
由于你已经将系统分配的标识分配给相应系统主题、自定义主题或域，并将该标识添加到目标上的对应角色中，请参阅[使用托管标识传递事件](managed-service-identity.md)中有关使用标识传递事件到目标的对应内容。


