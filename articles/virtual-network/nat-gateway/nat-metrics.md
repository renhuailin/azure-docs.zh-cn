---
title: Azure 虚拟网络 NAT 的指标和警报
titleSuffix: Azure Virtual Network
description: 了解 Azure 虚拟网络 NAT 可用的 Azure Monitor 指标和警报。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 83c701ce38ac148c442efc33a60dae042335971a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297013"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure 虚拟网络 NAT 指标

Azure 虚拟网络 NAT 网关资源提供多维指标。 可以使用这些指标来观察操作以及进行[故障排除](troubleshoot-nat.md)。  可以针对严重问题（例如 SNAT 耗尽）配置警报。

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="用于出站 Internet 连接的虚拟网络 NAT">
</p>

*图：用于出站 Internet 连接的虚拟网络 NAT*

## <a name="metrics"></a>指标

NAT 网关资源在 Azure Monitor 中提供以下多维指标：

| 指标 | 说明 | 建议的聚合 | 维度 |
|---|---|---|---|
| 字节 | 处理的入站和出站字节数 | Sum | 方向（传入；传出），协议（6 TCP；17 UDP） |
| 数据包数 | 处理的入站和出站数据包数 | Sum | 方向（传入；传出），协议（6 TCP；17 UDP） |
| 丢弃的数据包数 | NAT 网关丢弃的数据包数 | Sum | / |
| SNAT 连接计数 | 每个间隔的状态转换次数 | Sum | 连接状态，协议（6 TCP；17 UDP） |
| SNAT 连接总数 | 当前的活动 SNAT 连接数（使用的大约 SNAT 端口数） | Sum | 协议（6 TCP；17 UDP） |


## <a name="alerts"></a>警报

可以在 Azure Monitor 中针对上述每个[指标](#metrics)配置警报。

## <a name="limitations"></a>限制

不支持资源运行状况。

## <a name="next-steps"></a>后续步骤

* 了解[虚拟网络 NAT](nat-overview.md)。
* 了解 [NAT 网关资源](nat-gateway-resource.md)
* 了解 [Azure Monitor](../../azure-monitor/overview.md)
* 了解如何[排查 NAT 网关资源问题](troubleshoot-nat.md)。
* [在 UserVoice 中告诉我们接下来想要为虚拟网络 NAT 开发什么功能](https://aka.ms/natuservoice)。