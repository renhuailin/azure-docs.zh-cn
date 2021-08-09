---
title: Azure 防火墙 DNS 代理详细信息
description: 了解 Azure 防火墙 DNS 代理的工作原理
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: victorh
ms.openlocfilehash: f6f17823b51200c21ee0975f50487ba1e3f58ced
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110547549"
---
# <a name="azure-firewall-dns-proxy-details"></a>Azure 防火墙 DNS 代理详细信息

可以对 Azure 防火墙进行配置来充当 DNS 代理。 DNS 代理是从客户端虚拟机到 DNS 服务器的 DNS 请求的中介。

以下信息介绍了 Azure 防火墙 DNS 代理的一些实现详细信息。

## <a name="fqdns-with-multiple-a-records"></a>具有多个 A 记录的 FQDN

Azure 防火墙充当标准 DNS 客户端。 如果响应中有多条 A 记录，则防火墙会将所有记录存储在缓存中。 如果每个响应有一条记录，则防火墙只存储一条记录。 客户端无法提前知道它是否应该预期响应中有一条或多条 A 记录。

## <a name="fqdn-time-to-live-ttl"></a>FQDN 生存时间 (TTL)

当 FQDN TTL（生存时间）即将过期时，记录将缓存并根据其 TTL 过期。 不使用预提取，因此在 TTL 过期之前，防火墙不会执行查找来刷新记录。

## <a name="clients-not-configured-to-use-the-firewall-dns-proxy"></a>客户端未配置为使用防火墙 DNS 代理

如果客户端计算机配置使用的 DNS 服务器不是防火墙 DNS 代理，则结果可能不可预知。

例如，假设客户端工作负载位于美国东部，并使用托管在美国东部的主 DNS 服务器。 为美国西部托管的辅助 DNS 服务器配置 Azure 防火墙 DNS 服务器设置。 美国西部托管的防火墙 DNS 服务器生成的响应不同于美国东部客户端的响应。

这是一种常见情况，正因如此，客户端应该使用防火墙的 DNS 代理功能。 如果你在网络规则中使用 FQDN，则客户端应使用防火墙作为解析程序。 可以确保客户端和防火墙本身的 IP 地址解析一致性。

在此示例中，如果在网络规则中配置了 FQDN，则防火墙会将 FQDN 解析为 IP1（IP 地址 1），并更新网络规则以允许访问 IP1。 如果客户端由于 DNS 响应的差异而将同一 FQDN 解析为 IP2，则其连接尝试将与防火墙上的规则不匹配，因此会被拒绝。 

对于应用程序规则中的 HTTP/S FQDN，防火墙将从主机或 SNI 头分析 FQDN，解析它，然后连接到该 IP 地址。 将忽略客户端尝试连接到的目标 IP 地址。

## <a name="next-steps"></a>后续步骤

- [Azure 防火墙 DNS 设置](dns-settings.md)