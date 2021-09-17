---
title: 事件传递、托管服务标识和专用链接
description: 本文介绍如何为 Azure 事件网格主题启用托管服务标识。 用于将事件转发到受支持的目标。
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: f1f80f23fe108415daa6e0526b651c7269d6b1b3
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634082"
---
# <a name="event-delivery-with-a-managed-identity"></a>使用托管标识进行事件传递
本文介绍如何为 Azure 事件网格系统主题、自定义主题或域使用[托管服务标识](../active-directory/managed-identities-azure-resources/overview.md)。 使用它将事件转发到受支持的目标，如服务总线队列和主题、事件中心和存储帐户。



## <a name="prerequisites"></a>必备条件
1. 向系统主题、自定义主题或域分配系统分配标识或用户分配标识。 
    - 对于自定义主题和域，请参阅[为自定义主题和域启用托管标识](enable-identity-custom-topics-domains.md)。 
    - 对于系统主题，请参阅[为系统主题启用托管标识](enable-identity-system-topics.md)
1. 在目标（例如，服务总线队列）上将标识添加到相应角色（例如，服务总线数据发送方）。 有关详细步骤，请参阅[向目标上的 Azure 角色添加标识](add-identity-roles.md)

    > [!NOTE]
    > 目前，无法使用[专用终结点](../private-link/private-endpoint-overview.md)传递事件。 有关详细信息，请参阅本文末尾的[专用终结点](#private-endpoints)部分。 

## <a name="create-event-subscriptions-that-use-an-identity"></a>创建使用标识的事件订阅
当你的事件网格自定义主题、系统主题或域已分配有系统托管标识并且你已向目标上的相应角色添加该标识之后，就可以创建使用该标识的订阅了。 

### <a name="use-the-azure-portal"></a>使用 Azure 门户
创建事件订阅时，你将在“终结点详细信息”部分看到一个选项，用于启用该终结点的系统分配标识或用户分配标识。 

以下示例说明如何在通过服务总线队列创建事件订阅目标时启用系统分配的标识。 

![在为服务总线队列创建事件订阅时启用标识](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

还可以在“附加功能”选项卡上启用将系统分配的标识用于死信的功能。 

![为死信启用系统分配的标识](./media/managed-service-identity/enable-deadletter-identity.png)

你还可以在创建事件订阅后对其启用托管标识。 在事件订阅的“事件订阅”页面，切换到“其他功能”选项卡以查看相关选项。  

![在现有事件订阅上启用系统分配标识](./media/managed-service-identity/event-subscription-additional-features.png)

如果已为主题启用用户分配标识，你会在“托管标识类型”的下拉列表中看到“用户分配标识”选项已启用。 如果你为“托管标识类型”选择“已分配用户”，随后便可选择向其传送事件的用户分配标识。  

![在事件订阅上启用用户分配标识](./media/managed-service-identity/event-subscription-user-identity.png)


### <a name="use-the-azure-cli---service-bus-queue"></a>使用 Azure CLI - 服务总线队列 
本部分介绍如何使用 Azure CLI 启用系统分配的标识，以将事件传送到服务总线队列。 标识必须是“Azure 服务总线数据发送方”角色的成员。 它还必须是用于死信的存储帐户上的“存储 Blob 数据参与者”角色的成员。 

#### <a name="define-variables"></a>定义变量
首先，指定要在 CLI 命令中使用的以下变量的值。 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>使用托管标识创建用于传送的事件订阅 
此示例命令为终结点类型设置为“服务总线队列”的事件网格自定义主题创建事件订阅。 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>使用托管标识创建用于传送和死信的事件订阅
此示例命令为终结点类型设置为“服务总线队列”的事件网格自定义主题创建事件订阅。 它还指定将系统托管标识用于死信。 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>使用 Azure CLI - 事件中心 
本部分介绍如何使用 Azure CLI 启用系统分配的标识，以将事件传送到事件中心。 该标识必须是“Azure 事件中心数据发送方”角色的成员。 它还必须是用于死信的存储帐户上的“存储 Blob 数据参与者”角色的成员。 

#### <a name="define-variables"></a>定义变量
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>使用托管标识创建用于传送的事件订阅 
此示例命令为终结点类型设置为“事件中心”的事件网格自定义主题创建事件订阅。 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>使用托管标识创建用于传送和死信的事件订阅 
此示例命令为终结点类型设置为“事件中心”的事件网格自定义主题创建事件订阅。 它还指定将系统托管标识用于死信。 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>使用 Azure CLI - Azure 存储队列 
本部分介绍如何使用 Azure CLI 启用系统分配的标识，以将事件传送到 Azure 存储队列。 该标识必须是存储帐户上“存储队列数据消息发送方”角色的成员。 它还必须是用于死信的存储帐户上的“存储 Blob 数据参与者”角色的成员。

#### <a name="define-variables"></a>定义变量  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>使用托管标识创建用于传送的事件订阅 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>使用托管标识创建用于传送和死信的事件订阅 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>专用终结点
目前，无法使用[专用终结点](../private-link/private-endpoint-overview.md)传递事件。 也就是说，如果有严格的网络隔离要求，即已传递事件流量不得离开专用 IP 空间，则不支持。 

但是，如果要求以安全方式使用加密通道发送事件并要求使用公共 IP 空间的发送方（在本例中，事件网格）的已知标识，则可以使用 Azure 事件网格自定义主题或配置了系统托管标识的域将事件传递到事件中心、服务总线或 Azure 存储服务，如本文中所示。 然后，可以使用部署在虚拟网络上的 Azure Functions 或 webhook 中配置的专用链接来请求事件。 请参阅示例：[使用 Azure Functions 连接到专用终结点](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/)。

在此配置下，流量通过公共 IP/internet 从事件网格传递到事件中心、服务总线或 Azure 存储，但可以对通道进行加密，并使用事件网格的托管标识。 如果将部署到虚拟网络的 Azure Functions 或 webhook 配置为通过专用链接使用事件中心、服务总线或 Azure 存储，则流量的那一部分显然将留在 Azure 内。


## <a name="next-steps"></a>后续步骤
若要了解托管标识，请参阅[什么是 Azure 资源托管标识](../active-directory/managed-identities-azure-resources/overview.md)。
