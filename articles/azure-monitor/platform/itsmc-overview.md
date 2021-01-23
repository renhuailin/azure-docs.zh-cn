---
title: IT 服务管理连接器概述
description: 本文概述了 IT 服务管理连接器 (ITSMC) 。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 6d9ad775f91778f95380a19fbe253e2cbbebd3fc
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736450"
---
# <a name="it-service-management-connector-overview"></a>IT 服务管理连接器概述

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT 服务管理连接器 (ITSMC) 允许将 Azure 连接到受支持的 IT 服务管理 (ITSM) 产品或服务。

Azure 服务（例如 Azure Log Analytics 和 Azure Monitor 提供用于检测、分析和解决 Azure 和非 Azure 资源的问题的工具。 但与问题相关的工作项通常位于 ITSM 的产品或服务中。 ITSMC 在 Azure 与 ITSM 工具之间提供双向连接，有助于更快地解决问题。

## <a name="configuration-steps"></a>配置步骤

ITSMC 支持使用以下 ITSM 工具建立的连接：

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> 我们建议 Cherwell 和 Provance 客户使用 [Webhook 操作](./action-groups.md#webhook) 来 Cherwell 和 Provance 终结点作为集成的另一个解决方案。

通过 ITSMC，你可以：

-  基于 Azure 警报在 ITSM 工具中创建工作项， (指标警报、活动日志警报和 Log Analytics 警报) 。
-  可以选择将 ITSM 工具中的事件和更改请求数据同步到 Azure Log Analytics 工作区。

有关法律条款和隐私策略的信息，请参阅 [Microsoft 隐私声明](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)。

可以通过完成以下步骤开始使用 ITSMC：

1. [设置 ITSM 环境以接受来自 Azure 的警报。](./itsmc-connections.md)
1. [配置 Azure ITSM 解决方案](./itsmc-definition.md#add-it-service-management-connector)
1. [为 ITSM 环境配置 Azure ITSM 连接器。](./itsmc-definition.md#create-an-itsm-connection)
1. [配置操作组以利用 ITSM 连接器。](./itsmc-definition.md#use-itsmc)

## <a name="next-steps"></a>后续步骤

* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)