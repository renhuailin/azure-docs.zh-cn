---
title: 包含文件
description: 包含文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/05/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5c0585182fcd6899bdd123607f3b7d46a97253e9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956552"
---
## <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服务
启用“允许受信任的 Microsoft 服务绕过此防火墙”设置时，将授权以下服务访问你的事件中心资源。

| 受信服务 | 支持的使用方案 | 
| --------------- | ------------------------- | 
| Azure 事件网格 | 允许 Azure 事件网格将事件发送到事件中心命名空间中的事件中心。 还需要执行以下步骤： <ul><li>为主题或域启用系统分配的标识</li><li>将标识添加到事件中心命名空间上的 Azure 事件中心数据发送方角色</li><li>然后，将使用事件中心的事件订阅配置为终结点，以使用系统分配的标识。</li></ul> <p>有关详细信息，请参阅[使用托管标识进行事件传递](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor（诊断设置） | 允许 Azure Monitor 将诊断信息发送到事件中心命名空间中的事件中心。 |
| Azure 流分析 | 允许 Azure 流分析作业在事件中心命名空间中从（[输入](../articles/stream-analytics/stream-analytics-add-inputs.md)）读取数据或将数据写入（[输出](../articles/stream-analytics/event-hubs-output.md)）事件中心。 <p>**重要说明**：流分析作业应配置为使用 **托管标识** 来访问事件中心。 有关详细信息，请参阅 [使用托管标识从 Azure 流分析作业访问事件中心 (预览) ](../articles/stream-analytics/event-hubs-managed-identity.md)。 </p>|
| Azure IoT 中心 | 允许 IoT 中心将消息发送到事件中心命名空间中的事件中心。 还需要执行以下步骤： <ul><li>为 IoT 中心启用系统分配的标识</li><li>将标识添加到事件中心命名空间上的 Azure 事件中心数据发送方角色。</li><li>然后，将使用事件中心的 IoT 中心配置为自定义终结点，以使用基于标识的身份验证。</li></ul>
