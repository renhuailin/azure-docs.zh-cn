---
title: Azure 负载均衡器浮动 IP 配置
description: Azure 负载均衡器浮动 IP 概述
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: e103af26d62518dabb5314c79c61335a2791417a
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479861"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure 负载均衡器浮动 IP 配置

负载均衡器针对 UDP 和 TCP 应用程序提供了多种功能。

## <a name="floating-ip"></a>浮动 IP

在某些应用程序场景中，后端池中单个 VM 上的多个应用程序实例更需要或要求使用相同端口。 端口重用的常见示例包括 
- 进行群集来实现高可用性
- 网络虚拟设备
- 在不重新加密的情况下公开多个 TLS 终结点。 

如果想要在多个规则中重复使用后端端口，必须在规则定义中启用浮动 IP。

启用浮动 IP 时，Azure 会更改 IP 地址映射，使其映射到负载均衡器前端的前端 IP 地址，而不是后端实例的 IP。 

如果没有浮动 IP，Azure 将公开 VM 实例的 IP。 启用浮动 IP 会更改负载均衡器前端 IP 的 IP 地址映射，以实现更大的灵活性。 在[此处](load-balancer-multivip-overview.md)了解更多信息。

## <a name="limitations"></a><a name = "limitations"></a>限制

- 对于负载均衡方案，辅助 IP 配置当前不支持浮动 IP

## <a name="next-steps"></a>后续步骤

- 请参阅[创建公共标准负载均衡器](quickstart-load-balancer-standard-public-portal.md)，开始使用负载均衡器。
- 了解 [Azure 负载均衡器出站连接](load-balancer-outbound-connections.md)。
- 详细了解 [Azure 负载均衡器](load-balancer-overview.md)。
- 了解[运行状况探测](load-balancer-custom-probe-overview.md)。
- 了解有关[标准负载均衡器诊断](load-balancer-standard-diagnostics.md)的信息。
- 详细了解[网络安全组](../virtual-network/network-security-groups-overview.md)。
