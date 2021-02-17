---
title: Azure Monitor 中的 IT 服务管理连接器
description: 本文提供了有关如何将 ITSM 产品/服务与 Azure Monitor 中的 IT 服务管理连接器 (ITSMC) 相连接，以集中监视和管理 ITSM 工作项的信息。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 7ac842df17e80b49990d89d1623330c4e31ab566
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608296"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>将 ITSM 产品/服务与 IT 服务管理连接器相连接
本文介绍如何配置 ITSM 产品/服务与 Log Analytics 中的 IT 服务管理连接器 (ITSMC) 之间的连接，以便集中管理工作项。 有关 ITSMC 的详细信息，请参阅[概述](../platform/itsmc-overview.md)。

支持以下 ITSM 产品/服务。 选择产品可查看有关如何将该产品连接到 ITSMC 的详细信息。

- [ServiceNow](../platform/itsmc-connections-servicenow.md)
- [System Center Service Manager](../platform/itsmc-connections-scsm.md)
- [Cherwell](../platform/itsmc-connections-cherwell.md)
- [Provance](../platform/itsmc-connections-provance.md)

> [!NOTE]
> 我们建议 Cherwell 和 Provance 客户使用 [Webhook 操作](../platform/action-groups.md#webhook) 来 Cherwell 和 Provance 终结点作为集成的另一个解决方案。

## <a name="next-steps"></a>后续步骤

* [排查 ITSM 连接器中的问题](../platform/itsmc-resync-servicenow.md)