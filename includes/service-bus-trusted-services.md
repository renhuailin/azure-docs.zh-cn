---
title: include 文件
description: 包含文件
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb6f2281cc115904db6babcf31f1af387b254d48
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426558"
---
## <a name="trusted-microsoft-services"></a>受信任的 Microsoft 服务
如果启用 " **允许受信任的 Microsoft 服务跳过此防火墙** " 设置，则将向以下服务授予对服务总线资源的访问权限。

| 受信服务 | 支持的使用方案 | 
| --------------- | ------------------------- | 
| Azure 事件网格 | 允许 Azure 事件网格将事件发送到服务总线命名空间中的队列或主题。 还需要执行以下步骤： <ul><li>为主题或域启用系统分配的标识</li><li>将标识添加到服务总线命名空间上的 Azure 服务总线数据发送方角色</li><li>然后，将使用服务总线队列或主题的事件订阅配置为终结点，以使用系统分配的标识。</li></ul> <p>有关详细信息，请参阅 [使用托管标识的事件传递](../articles/event-grid/managed-service-identity.md)</p>|
