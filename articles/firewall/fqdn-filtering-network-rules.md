---
title: 网络规则中的 Azure 防火墙 FQDN 筛选
description: 如何使用网络规则中的 Azure 防火墙 FQDN 筛选
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: f7196c7715ad5d2c02759040b780b96218e1655e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94695939"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>在网络规则中使用 FQDN 筛选

完全限定的域名 (FQDN) 表示主机或 IP 地址的域名。 你可以基于 Azure 防火墙和防火墙策略中的 DNS 解析在网络规则中使用 FQDN。 此功能允许你筛选采用任何 TCP/UDP 协议（包括 NTP、SSH、RDP 等）的出站流量。 若要在网络规则中使用 FQDN，你必须启用 DNS 代理。 有关详细信息，请参阅 [Azure 防火墙 DNS 设置](dns-settings.md)。

> [!NOTE]
> 按照设计，FQDN 筛选不支持通配符。

## <a name="how-it-works"></a>工作原理

定义你的组织需要的 DNS 服务器（Azure DNS 或你自己的自定义 DNS）后，Azure 防火墙将基于所选 DNS 服务器将 FQDN 转换为 IP 地址。 将针对应用程序和网络规则处理进行此转换。

进行新 DNS 解析时，会将新的 IP 地址添加到防火墙规则。 DNS 服务器不再返回的旧 IP 地址会在 15 分钟后过期。 对网络规则中的 FQDN 进行 DNS 解析时，Azure 防火墙规则每隔 15 秒更新一次。

### <a name="differences-in-application-rules-vs-network-rules"></a>应用程序规则与网络规则的差异

- 应用程序规则中针对 HTTP/S 和 MSSQL 的 FQDN 筛选基于应用程序级透明代理和 SNI 标头。 因此，它可以区分解析为同一 IP 地址的两个 FQDN。 对于网络规则中的 FQDN 筛选，情况并非如此。 

   如果可能，请始终使用应用程序规则：
     - 如果协议为 HTTP/S 或 MSSQL，请使用应用程序规则进行 FQDN 筛选。
   - 对于 HTTP/S 或 MSSQL 以外的其他任何协议，可以使用应用程序或网络规则进行 FQDN 筛选。

## <a name="next-steps"></a>后续步骤

[Azure 防火墙 DNS 设置](dns-settings.md)
