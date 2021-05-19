---
title: Azure 对等互连服务常见问题解答
description: 了解 Microsoft Azure 对等互连服务常见问题解答
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 95ce90dbbf47ffe527fe6f25704d9cd28b834ea9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026689"
---
# <a name="peering-service-faq"></a>对等互连服务常见问题解答

本文介绍有关 Azure 对等互连服务连接的常见问题解答。


**问：目标客户是谁？**

A. 目标客户是使用 Internet 作为传输来连接到 Microsoft 云的企业。

**问：客户是否可通过多家提供商注册对等互连服务？** 

A. 是，客户可在同一区域或不同区域中通过多家提供商注册对等互连服务，但不可注册同一前缀。

**问：客户是否可在每个地理区域为其站点选择唯一 ISP？**

A. 是，客户可这样做。 在每个区域选择适合你的业务需求和运营需求的合作伙伴 ISP。

**问：什么是 Microsoft Edge PoP？**

A. 它是 Microsoft 与其他网络互连的物理位置。 在 Microsoft Edge PoP 位置中，会托管 Azure Front Door 和 Azure CDN 等服务。 有关详细信息，请参阅 [Azure CDN](../cdn/cdn-features.md)。

## <a name="peering-service-unique-characteristics"></a>对等互连服务：独有特征

**问：对等互连服务与普通的 Internet 访问之间有何区别？**

A. 已注册 Microsoft 对等互连服务的合作伙伴正在与 Microsoft 合作来提供到 Microsoft 服务的更优路由和可靠连接。  

**问：对等互连服务与 ExpressRoute 之间有何区别？**

A. Azure ExpressRoute 是来自一个或多个客户位置的私密专用连接。 虽然对等互连服务提供更优的公共连接，不支持任何私密连接，但它也为本地 Internet 接入点提供更佳的连接。

## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务，请参阅[对等互连服务概述](about.md)。
- 若要查找服务提供商，请参阅[对等互连服务合作伙伴和位置](location-partners.md)。
- 若要加入对等互连服务连接，请参阅[加入对等互连服务](onboarding-model.md)。
- 若要注册对等互连服务连接，请参阅[注册对等互连服务连接 - Azure 门户](azure-portal.md)。
- 若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。