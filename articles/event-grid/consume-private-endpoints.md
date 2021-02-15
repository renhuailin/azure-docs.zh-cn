---
title: 使用专用链接服务传递事件
description: 本文介绍如何解决不能使用专用链接服务传递事件的限制。
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: ad6f500830383f60e0350a297d2650bfbeae2f6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417080"
---
# <a name="deliver-events-using-private-link-service"></a>使用专用链接服务传递事件
目前不能使用 [专用终结点](../private-link/private-endpoint-overview.md)传递事件。 也就是说，如果你的已交付事件流量不得离开专用 IP 空间，则不支持严格的网络隔离要求。 

## <a name="use-managed-identity"></a>使用托管标识
但是，如果你的要求以安全方式使用加密通道和发送方的已知标识发送事件 (在本例中，事件网格) 使用公共 IP 空间，则可以使用 Azure 事件网格自定义主题或包含系统托管标识的域将事件传送到事件中心、服务总线或 Azure 存储服务。 有关使用托管标识传递事件的详细信息，请参阅 [使用托管标识的事件传递](managed-service-identity.md)。 

然后，可以使用在虚拟网络上部署的 Azure Functions 或 webhook 中配置的私有链接来请求事件。 请参阅示例： [连接到专用终结点，Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/)。


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.png" alt-text="通过专用链接服务交付":::


在此配置下，流量通过从事件网格到事件中心、服务总线或 Azure 存储的公共 IP/internet 进入，但可以对通道进行加密，并使用事件网格的托管标识。 如果将部署到虚拟网络的 Azure Functions 或 webhook 配置为通过专用链接使用事件中心、服务总线或 Azure 存储，则流量的该部分将显然在 Azure 中。

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>使用托管标识将事件传送到事件中心
若要使用托管标识将事件传递到事件中心命名空间中的事件中心，请执行以下步骤：

1. [为主题或域启用系统分配的标识](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity)。 
1. [将该标识添加到事件中心命名空间上的 **Azure 事件中心数据发送方** 角色](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal)。
1. [在事件中心命名空间上启用 " **允许受信任的 Microsoft 服务跳过此防火墙** " 设置](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services)。 
1. 将使用事件中心作为终结点的[事件订阅配置](managed-service-identity.md#create-event-subscriptions-that-use-an-identity)为使用系统分配的标识。

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>使用托管标识将事件传送到服务总线
若要使用托管标识将事件传递到服务总线命名空间中的服务总线队列或主题，请执行以下步骤：

1. [为主题或域启用系统分配的标识](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity)。 
1. 将标识添加到服务总线命名空间上的 [Azure 服务总线数据发送方](/service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus) 角色
1. 在 [服务总线命名空间上启用 "**允许受信任的 Microsoft 服务跳过此防火墙**" 设置](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services)。 
1. 将使用服务总线队列或主题的[事件订阅配置](managed-service-identity.md#create-event-subscriptions-that-use-an-identity)为终结点，以使用系统分配的标识。

## <a name="deliver-events-to-storage"></a>将事件传递到存储 
若要使用托管标识将事件传递到存储队列，请遵循以下步骤：

1. [为主题或域启用系统分配的标识](managed-service-identity.md#create-a-custom-topic-or-domain-with-an-identity)。
1. 将标识添加到 Azure 存储队列中的 [存储队列数据消息发送方](../storage/common/storage-auth-aad-rbac-portal.md) 角色。
1. 将使用服务总线队列或主题的[事件订阅配置](managed-service-identity.md#create-event-subscriptions-that-use-an-identity)为终结点，以使用系统分配的标识。


## <a name="next-steps"></a>后续步骤
有关使用托管标识传递事件的详细信息，请参阅 [使用托管标识的事件传递](managed-service-identity.md)。 
