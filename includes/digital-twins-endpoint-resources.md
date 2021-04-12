---
author: baanders
description: 包括创建 Azure 数字孪生终结点所需资源的文件
ms.service: digital-twins
ms.topic: include
ms.date: 1/26/2021
ms.author: baanders
ms.openlocfilehash: 58c90bae3dea0f3a47489ea7d8de6a79f823dcab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99054495"
---
### <a name="prerequisite-create-endpoint-resources"></a>必备条件：创建终结点资源

若要将终结点链接到 Azure 数字孪生，需具备将用于终结点的事件网格主题、事件中心或服务总线主题。

使用下图查看在创建终结点之前应该设置的资源。

| 终结点类型 | 所需的资源（已链接到创建说明） |
| --- | --- |
| 事件网格终结点 | [事件网格主题](../articles/event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) |
| 事件中心终结点 | [事件&nbsp;中心&nbsp;命名空间](../articles/event-hubs/event-hubs-create.md)<br/><br/>[事件中心](../articles/event-hubs/event-hubs-create.md)<br/><br/>（可选）基于密钥的身份验证的[授权规则](../articles/event-hubs/authorize-access-shared-access-signature.md) | 
| Service Bus 终结点 | [服务总线命名空间](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/>[服务总线主题](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md)<br/><br/> （可选）基于密钥的身份验证的[授权规则](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature)|
