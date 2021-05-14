---
title: Azure 中的反向 DNS 概述 - Azure DNS
description: 在此学习路径中，开始学习反向 DNS 的工作原理以及如何在 Azure 中运用
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: 7f1cde9587615dbbecc450551a3b19fe02db85f5
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065252"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>反向 DNS 和 Azure 支持概述

本文概述性介绍了反向 DNS 的工作原理以及 Azure 中支持反向 DNS 的方案。

## <a name="what-is-reverse-dns"></a>什么是反向 DNS？

常规 DNS 记录将 DNS 名称映射到 IP 地址，例如 `www.contoso.com` 会解析为 64.4.6.100。 反向 DNS 执行相反的操作，会将 IP 地址转换为名称。 例如，对 64.4.6.100 的查找结果将解析为 `www.contoso.com`。

许多不同类型的情景中都会用到反向 DNS 记录。 例如，反向 DNS 记录可以验证电子邮件的发件人，因此广泛用于防御垃圾电子邮件。  接收邮件的服务器会检索发送服务器的 IP 地址的反向 DNS 记录。 然后接收邮件的服务器会验证相关主机是否有权从发起域发送电子邮件。

## <a name="how-reverse-dns-works"></a>反向 DNS 的工作原理

反向 DNS 记录托管在名为“ARPA”区域的特殊 DNS 区域中。  这些区域构成了一个独立的 DNS 层次结构，这种结构与托管类似于 `contoso.com` 的域的正常层次结构是并列关系。

例如，DNS 记录 `www.contoso.com` 是使用区域 `contoso.com` 中名称为“www”的 DNS“A”记录实现的。 此 A 记录指向相应的 IP 地址，在本例中为 64.4.6.100。  反向查找是通过单独使用区域“6.4.64.in-addr.arpa”中名为“100”的“PTR”记录实现的。 请注意 ARPA 区域中的 IP 地址是反向的。 此 PTR 记录在配置正确时指向名称 `www.contoso.com`。

如果组织被分配了 IP 地址块，则还有权管理相应的 ARPA 区域。 对应于 Azure 使用的 IP 地址块的 ARPA 区域由 Microsoft 托管和管理。 ISP 可能会为你拥有的 IP 地址托管 ARPA 区域。 它们还允许你在所选的 DNS 服务（如 Azure DNS）中托管 ARPA 区域。

> [!NOTE]
> 正向 DNS 查找和反向 DNS 查找是在独立的并行 DNS 层次结构中实现的。 “www.contoso.com”的反向查找 **不是** 托管在区域“contoso.com”中，而是托管在相应 IP 地址块的 ARPA 区域中。 独立区域用于 IPv4 和 IPv6 地址块。

### <a name="ipv4"></a>IPv4

IPv4 反向查找区域的名称应采用以下格式：`<IPv4 network prefix in reverse order>.in-addr.arpa`。

例如，为 IP 前缀为 192.0.2.0/24 的主机创建反向区域以托管其记录时，将创建一个区域名称，方法是隔离地址的网络前缀 (192.0.2)，然后反转其顺序 (2.0.192) 并添加后缀 `.in-addr.arpa`。

|子网类|网络前缀  |反转的网络前缀  |标准后缀  |反向区域名称 |
|-------|----------------|------------|-----------------|---------------------------|
|类 A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|类 B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|类 C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>无类别 IPv4 委托

在某些情况下，提供给组织的 IP 范围小于 C 类 (/24) 范围。 在这种情况下，IP 范围并不在 `.in-addr.arpa` 区域层次结构中的区域边界内，因此不可委托为子区域。

人们使用了另一种方法将每个反向查找记录传输到专用 DNS 区域。 此方法为每个 IP 范围委托一个子区域。 然后使用 CNAME 记录将范围内的每个 IP 地址单独映射到该子区域。

例如，假设你的 ISP 向你的组织授予 IP 范围 192.0.2.128/26。 此地址块表示 192.0.2.128 到 192.0.2.191 的 64 个 IP 地址。 此范围的反向 DNS 实现方式如下所示：
- 组织创建一个名为 128-26.2.0.192.in-addr.arpa 的反向查找区域。 前缀“128-26”表示在类 C (/24) 范围内分配给组织的网络段。
- ISP 创建 NS 记录，以便从类 C 父区域设置上述区域的 DNS 委托。 ISP 还在父级（C 类）反向查找区域中创建 CNAME 记录。 然后，它们将 IP 范围中的每个 IP 地址映射到组织创建的新区域：

    ```
    $ORIGIN 2.0.192.in-addr.arpa
    ; Delegate child zone
    128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
    128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
    ; CNAME records for each IP address
    129       CNAME    129.128-26.2.0.192.in-addr.arpa
    130       CNAME    130.128-26.2.0.192.in-addr.arpa
    131       CNAME    131.128-26.2.0.192.in-addr.arpa
    ; etc
    ```

- 然后组织在 PTR 记录的子区域内管理各个记录。

    ```
    $ORIGIN 128-26.2.0.192.in-addr.arpa
    ; PTR records for each UIP address. Names match CNAME targets in parent zone
    129      PTR    www.contoso.com
    130      PTR    mail.contoso.com
    131      PTR    partners.contoso.com
    ; etc
    ```

IP 地址“192.0.2.129”的反向查找查询名为“129.2.0.192.in-addr.arpa”的 PTR 记录。 此查询通过父区域中的 CNAME 解析为子区域中的 PTR 记录。

### <a name="ipv6"></a>IPv6

IPv6 反向查找区域的名称应采用以下格式：`<IPv6 network prefix in reverse order>.ip6.arpa`

例如，当创建一个用于承载某些主机记录的反向区域且这些主机的 IP 带有 2001:db8:1000:abdc::/64 前缀时。 将通过隔离该地址 (2001:db8:abdc::) 的网络前缀来创建区域名称。 接下来，如果使用了[零压缩](/previous-versions/windows/it-pro/windows-server-2003/cc781672(v=ws.10))缩短 IPv6 地址前缀 (2001:0db8:abdc:0000::)，则展开 IPv6 网络前缀将其删除。 反转顺序并使用句点作为前缀中每个十六进制数之间的分隔符，构建反向网络前缀 (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`)，然后添加后缀 `.ip6.arpa`。


|网络前缀  |展开的反向网络前缀 |标准后缀 |反向区域名称  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Azure 对反向 DNS 的支持

在反向 DNS 方面，Azure 支持两种不同的方案：

**承载与 IP 地址块对应的反向查找区域** - 可使用 Azure DNS [承载反向查找区域和管理 PTR 记录](dns-reverse-dns-hosting.md)且同时适用于 IPv4 和 IPv6。 创建反向查找 (ARPA) 区域、设置委托和配置 PTR 记录的过程与常规 DNS 区域相同。 区别在于必须通过 ISP 而不是 DNS 注册机构来配置委托，并且只能使用 PTR 记录类型。

**为分配给 Azure 服务的 IP 地址配置反向 DNS 记录** - 通过 Azure 可以[为提供给 Azure 服务的 IP 地址配置反向查找](dns-reverse-dns-for-azure-services.md)。  Azure 将这种反向查找配置为相应 ARPA 区域中的 PTR 记录。  这些对应于 Azure 使用的所有 IP 范围的 ARPA 区域由 Microsoft 托管

## <a name="next-steps"></a>后续步骤

- 有关反向 DNS 的详细信息，请参阅 [Wikipedia 上的反向 DNS 查找](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)。
- 了解如何[在 Azure DNS 中为 ISP 分配的 IP 范围托管反向查找区域](dns-reverse-dns-for-azure-services.md)。
- 了解如何[管理 Azure 服务的反向 DNS 记录](dns-reverse-dns-for-azure-services.md)。
