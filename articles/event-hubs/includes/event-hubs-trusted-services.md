---
title: include 文件
description: 包含文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4696b7ac01491a4656ebf8f02a7dd651c4016f0e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415543"
---
## <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服务
启用“允许受信任的 Microsoft 服务绕过此防火墙”设置时，将授权以下服务访问你的事件中心资源。

| 受信服务 | 支持的使用方案 | 
| --------------- | ------------------------- | 
| Azure 事件网格 | 允许 Azure 事件网格将事件发送到事件中心命名空间中的事件中心。 还需要执行以下步骤： <ul><li>为主题或域启用系统分配的标识</li><li>将标识添加到事件中心命名空间上的 Azure 事件中心数据发送方角色</li><li>然后，将使用事件中心的事件订阅配置为终结点，以使用系统分配的标识。</li></ul> <p>有关详细信息，请参阅[使用托管标识进行事件传递](../../event-grid/managed-service-identity.md)</p>|
| Azure Monitor（诊断设置） | 允许 Azure Monitor 将诊断信息发送到事件中心命名空间中的事件中心。 Azure Monitor 可以从事件中心读取数据，还可以将数据写入事件中心。 |
| Azure 流分析 | 允许 Azure 流分析作业在事件中心命名空间中从（[输入](../../stream-analytics/stream-analytics-add-inputs.md)）读取数据或将数据写入（[输出](../../stream-analytics/event-hubs-output.md)）事件中心。 <p>**重要说明**：流分析作业应配置为使用托管标识来访问事件中心。 有关详细信息，请参阅[使用托管标识通过 Azure 流分析作业访问事件中心（预览版）](../../stream-analytics/event-hubs-managed-identity.md)。 </p>|
| Azure IoT 中心 | 允许 IoT 中心将消息发送到事件中心命名空间中的事件中心。 还需要执行以下步骤： <ul><li>为 IoT 中心启用系统分配的标识</li><li>将标识添加到事件中心命名空间上的 Azure 事件中心数据发送方角色。</li><li>然后，将使用事件中心的 IoT 中心配置为自定义终结点，以使用基于标识的身份验证。</li></ul>
| Azure API 管理 | <p>使用 API 管理服务可将事件发送到你的事件中心命名空间中的事件中心。</p> <ul><li>使用 [send-request 策略](../../api-management/api-management-sample-send-request.md)调用 API 时，可以通过向事件中心发送事件来触发自定义工作流。</li><li>也可以将事件中心视为 API 中的后端。 有关示例策略，请参阅[使用托管标识进行身份验证以访问事件中心](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml)。 还需要执行以下步骤：<ol><li>在 API 管理实例上启用系统分配的标识。 有关说明，请参阅[在 Azure API 管理中使用托管标识](../../api-management/api-management-howto-use-managed-service-identity.md)。</li><li>将标识添加到事件中心命名空间中的“Azure 事件中心数据发送方”角色</li></ol></li></ul> | 
