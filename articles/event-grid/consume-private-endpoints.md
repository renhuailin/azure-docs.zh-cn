---
title: 使用专用链接服务来传递事件
description: 本文介绍如何解决无法使用专用链接服务传递事件的限制。
ms.topic: how-to
ms.date: 07/01/2021
ms.openlocfilehash: 0672b2b93cf7413ac9a3d46e8d824354276ba89f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286111"
---
# <a name="deliver-events-using-private-link-service"></a>使用专用链接服务来传递事件
目前，无法使用[专用终结点](../private-link/private-endpoint-overview.md)传递事件。 也就是说，如果有严格的网络隔离要求，即已传递事件流量不得离开专用 IP 空间，则不支持使用专用终结点传递事件的功能。 

## <a name="use-managed-identity"></a>使用托管标识
但是，如果要求以安全方式使用加密通道发送事件并要求使用公共 IP 空间的发送方（在本例中为事件网格）的已知标识，则可以使用 Azure 事件网格自定义主题或具有系统托管标识的域将事件传递到事件中心、服务总线或 Azure 存储服务。 有关使用托管标识传递事件的详细信息，请参阅[使用托管标识的事件传递](managed-service-identity.md)。 

然后，可以使用部署在虚拟网络上的 Azure Functions 或 Webhook 中配置的专用链接来拉取事件。 请参阅示例：[使用 Azure Functions 连接到专用终结点](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/)。


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="通过专用链接服务进行传递":::


在此配置下，流量通过公共 IP/Internet 从事件网格传递到事件中心、服务总线或 Azure 存储，但你可以对通道进行加密，并使用事件网格的托管标识。 如果将部署到虚拟网络的 Azure Functions 或 Webhook 配置为通过专用链接使用事件中心、服务总线或 Azure 存储，则流量的那一部分显然将留在 Azure 内。

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>使用托管标识将事件传递到事件中心
若要使用托管标识将事件传递到事件中心命名空间中的事件中心，请执行以下步骤：

1. 启用系统分配的标识：[系统主题](enable-identity-system-topics.md)、[自定义主题和域](enable-identity-custom-topics-domains.md)。  
1. [将标识添加到事件中心命名空间中的“Azure 事件中心数据发送方”角色](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal)。
1. [在事件中心命名空间中启用“允许受信任的 Microsoft 服务绕过此防火墙”设置](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services)。 
1. [将使用事件中心的事件订阅配置为终结点](managed-service-identity.md#create-event-subscriptions-that-use-an-identity)，以使用系统分配的标识。

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>使用托管标识将事件传递到服务总线
若要使用托管标识将事件传递到服务总线命名空间中的服务总线队列或主题，请执行以下步骤：

1. 启用系统分配的标识：[系统主题](enable-identity-system-topics.md)、[自定义主题和域](enable-identity-custom-topics-domains.md)。 
1. [为服务总线命名空间中的“Azure 服务总线数据发送方”角色添加标识](../service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus)
1. [在服务总线命名空间中启用“允许受信任的 Microsoft 服务绕过此防火墙”设置](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services)。 
1. [将使用服务总线队列或主题的事件订阅配置为终结点](managed-service-identity.md)，以使用系统分配的标识。

## <a name="deliver-events-to-storage"></a>将事件传递到存储 
若要使用托管标识将事件传递到存储队列，请执行以下步骤：

1. 启用系统分配的标识：[系统主题](enable-identity-system-topics.md)、[自定义主题和域](enable-identity-custom-topics-domains.md)。 
1. [为 Azure 存储队列上的“存储队列数据消息发送方”角色添加标识](../storage/blobs/assign-azure-role-data-access.md)。
1. [将使用存储队列的事件订阅配置为终结点](managed-service-identity.md#create-event-subscriptions-that-use-an-identity)，以使用系统分配的标识。


## <a name="next-steps"></a>后续步骤
若要详细了解如何使用托管标识来传递事件，请参阅[使用托管标识的事件传递](managed-service-identity.md)。