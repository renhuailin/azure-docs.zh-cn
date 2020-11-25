---
title: 包含文件
description: 包含文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d828a0c3eb2582a833ee8ad07bdf4f18002c9dca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015565"
---
## <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服务
启用“允许受信任的 Microsoft 服务绕过此防火墙”设置时，将授权以下服务访问你的事件中心资源。

| 受信服务 | 支持的使用方案 | 
| --------------- | ------------------------- | 
| Azure 事件网格 | 允许 Azure 事件网格将事件发送到事件中心命名空间中的事件中心。 还需要执行以下步骤： <ul><li>为主题或域启用系统分配的标识</li><li>将标识添加到事件中心命名空间上的 Azure 事件中心数据发送方角色</li><li>然后，将使用事件中心作为终结点的事件订阅配置为使用系统分配的标识。</li></ul> <p>有关详细信息，请参阅 [使用托管标识的事件传递](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor（诊断设置） | 允许 Azure Monitor 将诊断信息发送到事件中心命名空间中的事件中心。 |
| Azure 流分析 | 允许 Azure 流分析作业从 ([输入](../articles/stream-analytics/stream-analytics-add-inputs.md) 中读取数据) 或将数据写入到事件中心命名空间) 事件中心 ([输出](../articles/stream-analytics/event-hubs-output.md) 。 |
| Azure IoT 中心 | 允许 IoT 中心将消息发送到事件中心命名空间中的事件中心。 还需要执行以下步骤： <ul><li>为 IoT 中心启用系统分配的标识</li><li>将该标识添加到事件中心命名空间上的 Azure 事件中心数据发送方角色。</li><li>然后，将使用事件中心作为自定义终结点的 IoT 中心配置为使用基于身份的身份验证。</li></ul>
