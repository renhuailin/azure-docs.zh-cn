---
title: Azure Monitor 中的 IT 服务管理连接器
description: 本文提供了有关如何将 ITSM 产品/服务与 Azure Monitor 中的 IT 服务管理连接器 (ITSMC) 相连接，以集中监视和管理 ITSM 工作项的信息。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 40e737a1ec5fb34cd22a08925143a100d36cdc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009311"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>将 ITSM 产品/服务与 IT 服务管理连接器相连接
本文介绍如何配置 ITSM 产品/服务与 Log Analytics 中的 IT 服务管理连接器 (ITSMC) 之间的连接，以便集中管理工作项。 有关 ITSMC 的详细信息，请参阅[概述](./itsmc-overview.md)。

支持以下 ITSM 产品/服务。 选择产品可查看有关如何将该产品连接到 ITSMC 的详细信息。

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

> [!NOTE]
> 我们建议 Cherwell 和 Provance 客户将 [Webhook 操作](./action-groups.md#webhook)用于 Cherwell 和 Provance 终结点作为集成的另一个解决方案。

## <a name="ip-ranges-for-itsm-partners-connections"></a>ITSM 合作伙伴连接的 IP 范围
为了列出 ITSM IP 地址以便允许来自合作伙伴 ITSM 工具的 ITSM 连接，建议列出他们 LogAnalytics 工作区所属的 Azure 区域的整个公共 IP 范围。 [此处的详细信息](https://www.microsoft.com/en-us/download/details.aspx?id=56519) 对于 EUS/WEU/EUS2/WUS2/美国中南部区域，客户只能列出 ActionGroup 网络标记。

## <a name="next-steps"></a>后续步骤

* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)
