---
title: Avere vFXT DNS - Azure
description: 使用 Avere vFXT for Azure 配置 DNS 服务器以进行轮询负载均衡
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: rohogue
ms.openlocfilehash: e1567cbd90bcb97088e7527cf7cd76ce3895e958
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668955"
---
# <a name="avere-cluster-dns-configuration"></a>Avere 群集 DNS 配置

本节介绍配置 DNS 系统以对 Avere vFXT 群集进行负载均衡的基础知识。

本文档不包含有关在 Azure 环境中设置和管理 DNS 服务器的说明。

不要使用轮询 DNS 对 Azure 中的 vFXT 群集进行负载均衡，而是考虑使用手动方法在客户端装载时均匀分配 IP 地址。 [装载 Avere 群集](avere-vfxt-mount-clients.md)中描述了几种方法。

在决定是否使用 DNS 服务器时，请记住以下事项：

* 如果只能通过 NFS 客户端访问系统，则无需使用 DNS，可使用数字 IP 地址指定所有网络地址。

* 如果你的系统支持 SMB (CIFS) 访问，则需要 DNS，因为必须为 Active Directory 服务器指定 DNS 域。

* 如果要使用 Kerberos 身份验证，则需要 DNS。

## <a name="load-balancing"></a>负载均衡

要分发总体负载，请将 DNS 域配置为对面向客户端的 IP 地址使用轮询负载分配。

## <a name="configuration-details"></a>配置详细信息

当客户端访问群集时，轮询 DNS (RRDNS) 会自动在所有可用接口之间平衡其请求。

要设置此系统，需要自定义 DNS 服务器的配置文件，使其在收到对 vFXT 群集的主域地址的装载请求时，在所有 vFXT 群集的装载点之间分配流量。 客户端使用其域名作为服务器参数来装载 vFXT 群集，并自动路由到下一个挂载 IP。

配置 RRDNS 有两个主要步骤：

1. 修改 DNS 服务器的 ``named.conf`` 文件，以设置查询到 vFXT 群集的循环顺序。 此选项会导致服务器循环使用所有可用 IP 值。 添加如下语句：

   ```bash
   options {
       rrset-order {
           class IN A name "vfxt.contoso.com" order cyclic;
       };
   };
   ```

1. 为每个可用 IP 地址配置 A 记录和指针 (PTR) 记录，如下例所示。

   ``nsupdate`` 命令提供了一个示例，演示如何为具有域名 vfxt.contoso.com 和三个装载地址（10.0.0.10、10.0.0.11 和 10.0.0.12）的 vFXT 群集正确配置 DNS：

   ```bash
   update add vfxt.contoso.com. 86400 A 10.0.0.10
   update add vfxt.contoso.com. 86400 A 10.0.0.11
   update add vfxt.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   这些命令为每个群集的装载地址创建一个 A 记录，并设置指针记录以适当支持反向 DNS 检查。

   下图显示了此配置的基本结构。

   :::image type="complex" source="media/round-robin-dns-diagram-vfxt.png" alt-text="显示客户端装载点 DNS 配置的关系图。":::
   <该关系图显示了三类元素之间的连接：单个 vFXT 域名（左侧）、三个 IP 地址（中间列）和三个内部使用反向 DNS 客户端接口（右侧列）。 左侧标记为“vfxt.contoso.com”的单个椭圆通过箭头连接，箭头指向标有 IP 地址 10.0.0.10、10.0.0.11 和 10.0.0.12 的三个椭圆。 从 vfxt.contoso.com 椭圆到三个 IP 椭圆的箭头标记为“A”。 每个 IP 地址椭圆都通过两个箭头连接到标记为客户端接口的椭圆 - IP 为 10.0.0.10 的椭圆连接到“client-IP-10.contoso.com”，IP 为“10.0.0.11”的椭圆连接到“client-IP-11.contoso.com”，IP 为 10.0.0.12 的椭圆连接到“client-IP-11.contoso.com”。 IP 地址椭圆和客户端接口椭圆之间的连接是两个箭头：一个箭头标记为“PTR”，从 IP 地址椭圆指向客户端接口椭圆，另一个箭头标记为“A”，从客户端接口椭圆指向 IP 地址椭圆。>:::image-end:::

配置完 RRDNS 系统后，告知你的客户端计算机使用该系统来解析其装载命令中的群集地址。

## <a name="cluster-dns-settings"></a>群集 DNS 设置

在“群集” > “管理网络设置”页面中指定 vFXT 群集使用的 DNS 服务器 。 该页面上的设置包括：

* DNS 服务器地址
* DNS 域名
* DNS 搜索域

有关使用此页面的详细信息，请阅读“Avere 群集配置指南”中的 [DNS 设置](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>)。
