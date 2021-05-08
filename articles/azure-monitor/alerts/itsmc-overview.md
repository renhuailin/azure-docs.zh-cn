---
title: IT 服务管理连接器概述
description: 本文提供对 IT 服务管理连接器 (ITSMC) 的概述。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 3f6131bc585b91676f29ed34dfedd49a5ca92201
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041784"
---
# <a name="it-service-management-connector-overview"></a>IT 服务管理连接器概述

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

使用 IT 服务管理连接器 (ITSMC) 可以将 Azure 连接到支持的 IT 服务管理 (ITSM) 产品或服务。

Azure Log Analytics 和 Azure Monitor 等 Azure 服务提供所需的工具用于检测、分析和排查 Azure 与非 Azure 资源的问题。 但是，与问题相关的工作项通常驻留在 ITSM 产品或服务中。 ITSMC 在 Azure 与 ITSM 工具之间提供双向连接，有助于更快解决问题。

## <a name="configuration-steps"></a>配置步骤

ITSMC 支持使用以下 ITSM 工具建立的连接：

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> 自 2020 年 10 月 1 日起，将不再为新客户启用 Cherwell 和 Provance ITSM 与 Azure 警报的集成。 将不支持新的 ITSM 连接。
> 将支持现有的 ITSM 连接。

通过 ITSMC，你可以：

-  在你的 ITSM 工具中，根据 Azure 警报（指标警报、活动日志警报和 Log Analytics 警报）创建工作项。
-  可以选择将 ITSM 工具中的事件和更改请求数据同步到 Azure Log Analytics 工作区。

有关法律条款和隐私策略的信息，请参阅 [Microsoft 隐私声明](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)。

你可以通过完成以下步骤开始使用 ITSMC：

1. [设置 ITSM 环境以接受 Azure 警报。](./itsmc-connections.md)
1. [配置 Azure ITSM 解决方案](./itsmc-definition.md#add-it-service-management-connector)
1. [为 ITSM 环境配置 Azure ITSM 连接器。](./itsmc-definition.md#create-an-itsm-connection)
1. [配置操作组以利用 ITSM 连接器。](./itsmc-definition.md#define-a-template)

## <a name="next-steps"></a>后续步骤

* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)
