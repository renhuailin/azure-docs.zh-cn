---
title: Azure Front Door - 路由体系结构 | Microsoft Docs
description: 本文以宏观视角对 Front Door 体系结构进行整体综述和梳理。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: f4e493b0cbc52c7f8f6165a8198dd19acf352378
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110494328"
---
# <a name="routing-architecture-overview"></a>路由体系结构概述

Azure Front Door 在收到客户端请求时，会执行以下两项操作之一。 应答这些请求（如果你启用缓存），或者将这些请求转发到作为反向代理的相应应用程序后端。

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>选择 Front Door 环境来实现流量路由（任意广播）

路由到 Azure Front Door 环境的流量会对 DNS（域名系统）和 HTTP（超文本传输协议）流量都使用[任意播](https://en.wikipedia.org/wiki/Anycast)，这样，用户请求就可以通过最少的网络跃点到达最近的环境。 此体系结构通过最大程度地发挥拆分 TCP 的优势为最终用户提供更佳的往返时间。 Front Door 将其环境组织为主“环”和回退“环”。 外环提供更接近用户的环境，延迟较低。  内环中的环境可以在外环环境发生任何问题时为其处理故障转移。 外环是所有流量的首选目标，而内环要处理来自外环的流量溢出。 由 Front Door 提供服务的每个前端主机或域都会被分配主 VIP（虚拟 Internet 协议地址），内环和外环中的环境都会公布这个分配结果。 回退 VIP 只由内环中的环境公布。 

此体系结构确保来自最终用户的请求始终访问最近的 Front Door 环境。 即使首选 Front Door 环境运行不正常，所有流量也都会自动移动到下一个最近的环境。

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>连接到 Front Door 环境（拆分 TCP）

[拆分 TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) 是一种用于减少延迟和 TCP 问题的技术，其原理是将会产生较长往返时间的连接拆分成较小片段。 通过使用离最终用户更近的 Front Door 环境，TCP 连接会在 Front Door 环境内终止。 到应用程序后端往返时间 (RTT) 较长的 TCP 连接会被拆分为两个单独的连接。 最终用户和 Front Door 环境之间的“短连接”意味着通过三个短往返行程而非三个长往返行程建立连接，从而减少了延迟。 Front Door 环境和后端之间的“长连接”可以预先建立，然后在其他最终用户请求中重复使用，从而节省连接时间。 在建立 SSL/TLS（传输层安全性）连接时，拆分 TCP 的效果会倍增，因为会有更多的往返来保护连接。

## <a name="processing-request-to-match-a-routing-rule"></a>处理请求时将其与路由规则进行匹配
在建立连接并完成 TLS 握手后，某个请求在登陆 Front Door 环境后的第一步是将其与传递规则进行匹配。 该匹配取决于 Front Door 上的配置：哪个特定的传递规则与该请求匹配。 阅读和详细了解 Front Door 如何执行[路由匹配](front-door-route-matching.md)。

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>确定后端池中可用于路由规则的后端
当 Front Door 满足了传入请求的传递规则后，下一步是获取与传递规则关联的后端池的运行状况探测状态（如果没有缓存）。 阅读和详细了解 Front Door 如何使用[运行状况探测](front-door-health-probes.md)监视后端运行状况。

## <a name="forwarding-the-request-to-your-application-backend"></a>将请求转发到应用程序后端
最后，假定未配置缓存，系统会基于[路由方法](front-door-routing-methods.md)配置将用户请求转发到“最佳”后端。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
