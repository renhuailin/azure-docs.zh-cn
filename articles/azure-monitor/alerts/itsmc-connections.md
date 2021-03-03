---
title: Azure Monitor 中的 IT 服务管理连接器
description: 本文提供了有关如何将 ITSM 产品/服务与 Azure Monitor 中的 IT 服务管理连接器 (ITSMC) 相连接，以集中监视和管理 ITSM 工作项的信息。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: a372cdcd05267f3bdb093f676948a79c473ad955
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734768"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>将 ITSM 产品/服务与 IT 服务管理连接器相连接
本文介绍如何配置 ITSM 产品/服务与 Log Analytics 中的 IT 服务管理连接器 (ITSMC) 之间的连接，以便集中管理工作项。 有关 ITSMC 的详细信息，请参阅[概述](./itsmc-overview.md)。

支持以下 ITSM 产品/服务。 选择产品可查看有关如何将该产品连接到 ITSMC 的详细信息。

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> 我们建议 Cherwell 和 Provance 客户使用 [Webhook 操作](./action-groups.md#webhook) 来 Cherwell 和 Provance 终结点作为集成的另一个解决方案。

## <a name="next-steps"></a>后续步骤

* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)