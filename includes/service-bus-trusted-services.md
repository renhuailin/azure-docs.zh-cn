---
title: include 文件
description: 包含文件
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd4d5de5d93e4aea862aaabd10fb5d3c6d45cb1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102510730"
---
## <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服务
启用“允许受信任的 Microsoft 服务绕过此防火墙”设置时，将授权以下服务访问你的服务总线资源。

| 受信服务 | 支持的使用方案 | 
| --------------- | ------------------------- | 
| Azure 事件网格 | 允许 Azure 事件网格将事件发送到服务总线命名空间中的队列或主题。 还需要执行以下步骤： <ul><li>为主题或域启用系统分配的标识</li><li>为服务总线命名空间中的“Azure 服务总线数据发送方”角色添加标识</li><li>然后，将使用服务总线队列或主题的事件订阅配置为终结点，以使用系统分配的标识。</li></ul> <p>有关详细信息，请参阅[使用托管标识进行事件传递](../articles/event-grid/managed-service-identity.md)</p>|
| Azure API 管理 | <p>API 管理服务允许你向服务总线命名空间中的服务总线队列/主题发送消息。</p><ul><li>使用 [send-request 策略](../articles/api-management/api-management-sample-send-request.md)调用 API 时，可以通过向服务总线队列/主题发送消息来触发自定义工作流。</li><li>还可以将服务总线队列/主题视为 API 中的后端。 有关示例策略，请参阅[使用托管标识进行身份验证以访问服务总线队列或主题](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml)。 还需要执行以下步骤：<ol><li>在 API 管理实例上启用系统分配的标识。 有关说明，请参阅[在 Azure API 管理中使用托管标识](../articles/api-management/api-management-howto-use-managed-service-identity.md)。</li><li>为服务总线命名空间中的“Azure 服务总线数据发送方”角色添加标识</li></ol></li></ul> | 