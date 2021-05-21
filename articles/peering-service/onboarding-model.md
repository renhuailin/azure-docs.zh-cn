---
title: Azure 对等互连服务载入模型
description: 了解如何载入 Azure 对等互连服务
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "84871397"
---
# <a name="onboarding-peering-service-model"></a>载入对等互连服务模型

对等互连服务的载入过程由下列两种模型组成：

 - 载入对等互连服务连接

 - 载入对等互连服务连接遥测

上面列出的模型的操作计划如下所示：

| **步骤** | **操作**| **提供的服务**|
|-----------|---------|---------|---------|
|1|客户从连接合作伙伴预配连接（与 Microsoft 没有交互）。 |与 Microsoft 连接良好并满足与 Microsoft 建立高性能可靠连接的技术要求的 Internet 提供商。  |
|2（可选）|客户将位置注册到 Azure门户。 位置由以下各项定义：ISP/IXP 名称、客户站点的物理位置（州级别）、服务提供商或企业为位置提供的 IP 前缀。  |遥测：Internet 路由监视、从 Microsoft 到用户最近边缘 POP 位置的流量优先级确定。 |



## <a name="onboarding-peering-service-connection"></a>载入对等互连服务连接

若要载入对等互连服务连接，请执行以下操作：

与 Internet 服务提供商 (ISP) 或 Internet 交换 (IX) 合作伙伴合作，以获取对等互连服务，从而将你的网络与 Microsoft 网络连接。

请确保[连接服务提供商](location-partners.md)与 Microsoft 合作提供对等互连服务。 

## <a name="onboarding-peering-service-connection-telemetry"></a>载入对等互连服务连接遥测

客户可以选择其遥测（如 BGP 路由分析）在访问 Microsoft 网络时监视网络延迟和性能。 这可以通过将连接注册到 Azure 门户来实现。

若要载入对等互连服务连接遥测，客户必须将对等互连服务连接注册到 Azure 门户。 若要了解过程，请参阅[注册对等互连服务 - Azure 门户](azure-portal.md)。

接下来，可以通过参照[此处](measure-connection-telemetry.md)来度量遥测。

## <a name="next-steps"></a>后续步骤

若要了解注册对等互连服务连接的分步过程，请参阅[注册对等互连服务 - Azure 门户](azure-portal.md)。

若要了解如何度量连接遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。
