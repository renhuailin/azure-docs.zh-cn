---
title: include 文件
description: include 文件
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/29/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 02def8321ce5df33fb89ca8d9f6c24167c15bbb6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733855"
---
### <a name="what-is-expressroute-direct"></a>什么是 ExpressRoute Direct？

借助 ExpressRoute Direct，用户可直接连接到巧妙分布在全球对等互连位置的 Microsoft 的全球网络。 ExpressRoute Direct 提供双 100 或 10 Gbps 连接，支持大规模的主动/主动连接。 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>客户如何连接到 ExpressRoute Direct？ 

客户需要联系他们的本地运营商和主机托管提供商以连接到 ExpressRoute 路由器才能利用 ExpressRoute Direct。

### <a name="what-locations-currently-support-expressroute-direct"></a>目前，哪些位置支持 ExpressRoute Direct？ 

请检查[位置页](../articles/expressroute/expressroute-locations-providers.md)上的可用性。 

### <a name="what-is-the-sla-for-expressroute-direct"></a>ExpressRoute Direct 的 SLA 是什么？

ExpressRoute Direct 将使用与 [ExpressRoute 企业级](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)相同的 SLA。 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>在哪些方案中客户应考虑使用 ExpressRoute Direct？  

ExpressRoute Direct 为客户提供到 Microsoft 全球主干线中的直接 100 或 10 Gbps 端口对。 将为客户带来最大效益的方案包括：大量数据引入、受管制市场的物理隔离和适用于突发方案的专用容量，例如呈现。 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>ExpressRoute Direct 的计费模型是什么？ 

ExpressRoute Direct 将针对端口对按固定金额计费。 标准线路将包含在内（不含额外的小时），高级线路将稍加一些附加费用。 流出量将基于对等互连位置的区域按每条线路进行计费。

### <a name="when-does-billing-start-and-stop-for-the-expressroute-direct-port-pairs"></a>ExpressRoute Direct 端口对的计费何时开始和停止？

创建 ExpressRoute Direct 资源 45 天后，或启用了一个或两个链接时，ExpressRoute Direct 端口对开始计费，以先到者为准。 我们提供 45 天的宽限期，让客户能够完成与主机托管提供程序的交叉连接过程。

删除直接端口并删除交叉连接后，你将不再为 ExpressRoute Direct 的端口对付费。 
