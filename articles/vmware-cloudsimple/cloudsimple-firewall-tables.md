---
title: Azure VMware Solution by CloudSimple - 防火墙表
description: 了解 CloudSimple 私有云防火墙表和防火墙规则，包括在每个防火墙表中创建的默认规则。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c44c39f66a0a0161eea8a7e9656bbe0e3d1015c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140864"
---
# <a name="firewall-tables-overview"></a>防火墙表概述

防火墙表列出了用于筛选进出私有云资源的网络流量的规则。 可以将防火墙表应用于 VLAN/子网。 规则控制源网络或 IP 地址与目标网络或 IP 地址之间的网络流量。

## <a name="firewall-rules"></a>防火墙规则

下表描述了防火墙规则中的参数。

| 属性 | 详细信息 |
| ---------| --------|
| **名称** | 唯一标识防火墙规则及其用途的名称。 |
| **Priority** | 介于 100 到 4096 之间的数字，其中 100 表示优先级最高。 规则按优先顺序处理。 流量与某个规则匹配时，规则处理即会停止。 因此，不会处理优先级较低的、其属性与高优先级规则相同的规则。  注意避免规则冲突。 |
| **状态跟踪** | 跟踪可以是无状态的（私有云、Internet 或 VPN），也可以是有状态的（公共 IP）。  |
| 协议  | 选项包括“任何”、“TCP”或“UDP”。 如果需要 ICMP，请使用“任何”。 |
| **方向** | 该规则是应用到入站还是出站流量。 |
| **操作** | 允许或拒绝规则中定义的流量类型。 |
| **Source** | 可以是 IP 地址、无类别域际路由 (CIDR) 块（例如 10.0.0.0/24）或“任何”。  指定范围、服务标记或应用程序安全组可以减少创建的安全规则数。 |
| **源端口** | 网络流量的来源端口。  可以指定单个端口或端口范围，例如 443 或 8000-8080。 指定范围可以减少创建的安全规则数。 |
| **目标** | 可以是 IP 地址、无类别域际路由 (CIDR) 块（例如 10.0.0.0/24）或“任何”。  指定范围、服务标记或应用程序安全组可以减少创建的安全规则数。  |
| **目标端口** | 网络流量流向的端口。  可以指定单个端口或端口范围，例如 443 或 8000-8080。 指定范围可以减少创建的安全规则数。|

### <a name="stateless"></a>无状态

无状态规则仅查看各个数据包，并基于规则对其进行筛选。  
相反方向的流量可能需要其他规则。  对以下点之间的流量使用无状态规则：

* 私有云的子网
* 本地子网和私有云子网
* 来自私有云的 Internet 流量

### <a name="stateful"></a>有状态

 有状态规则知道通过它的连接。 将为现有连接创建流记录。 是允许还是拒绝通信取决于流记录的连接状态。  对公共 IP 地址使用此规则类型，以筛选来自 Internet 的流量。

### <a name="default-rules"></a>默认规则

在每个防火墙表上创建以下默认规则。

|优先级|名称|状态跟踪|方向|流量类型|协议|源|Source Port|目标|Destination Port|操作|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|有状态|出站|公共 IP 或 Internet 流量|All|任意|任意|任意|任意|允许|
|65001|deny-all-from-internet|有状态|入站|公共 IP 或 Internet 流量|All|任意|任意|任意|任意|拒绝|
|65002|allow-all-to-intranet|无状态|出站|私有云内部或 VPN 流量|All|任意|任意|任意|任意|Allow|
|65003|allow-all-from-intranet|无状态|入站|私有云内部或 VPN 流量|All|任意|任意|任意|任意|Allow|

## <a name="next-steps"></a>后续步骤

* [设置防火墙表和规则](firewall.md)
