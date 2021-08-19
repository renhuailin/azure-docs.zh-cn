---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 07/28/2021
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 4145cc06148db0f12ff2a2288512069155d09a44
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014610"
---
| 资源 | 限制 |
| --- | --- |
| 数据吞吐量 |30 Gbps|
|规则限制|网络规则中 10,000 个唯一源/目标|
|最高 DNAT 规则数目|250 |
|最小 AzureFirewallSubnet 大小 |/26|
|网络和应用程序规则的端口范围|1 - 65535|
|公共 IP 地址|最多 250 个。 所有公共 IP 地址都可以在 DNAT 规则中使用，它们都对可用的 SNAT 端口有影响。|
|IP 组中的 IP 地址|每个防火墙最多 100 个 IP 组。<br>每个 IP 组最多 5000 个单独的 IP 地址或 IP 前缀。
|路由表|默认情况下，AzureFirewallSubnet 使用其 NextHopType 值设置为“Internet”的 0.0.0.0/0 路由。<br><br>Azure 防火墙必须具有直接的 Internet 连接。 如果 AzureFirewallSubnet 知道通过 BGP 的本地网络的默认路由，则必须将其替代为 0.0.0.0/0 UDR，将 NextHopType 值设置为 Internet 以保持 Internet 直接连接 。 默认情况下，Azure 防火墙不支持强制的安全加密链路连接到本地网络。<br><br>但是，如果你的配置要求强制的安全加密链路连接到本地网络，Microsoft 将基于具体的情况提供支持。 请联系支持人员，以便我们可以查看你的情况。 如果接受，我们将允许你的订阅，并确保保持所需的防火墙 Internet 连接。|
|网络规则中的 FQDN|为了获得良好的性能，每个防火墙的所有网络规则中的 FQDN 不要超过 1000 个。|