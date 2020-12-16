---
title: Microsoft 对等互连策略
titleSuffix: Azure
description: Microsoft 对等互连策略
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bee41bb8e5beb4df3086ab50499cb185a83e4efe
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592323"
---
# <a name="peering-policy"></a>对等互连策略
Microsoft 维护了一个选择性的对等互连策略，旨在确保获得最佳的客户体验（由行业标准和最佳实践支持）、针对未来需求进行缩放以及对等互连的战略定位。 因此，Microsoft 保留在必要时对策略进行例外的权利。 Microsoft 对网络的一般要求在以下各节中进行了介绍。 它们适用于直接对等互连请求和 Exchange 对等互连请求。 

## <a name="technical-requirements"></a>技术要求

* 具有足够容量的完全冗余网络，无需拥塞即可交换通信。
* 对等端将具有可公开路由的自治系统编号 (ASN) 。
* IPv4 和 IPv6 均受支持，Microsoft 希望在每个对等位置建立两种类型的会话。
* MD5 不受支持。
* **ASN 详细信息：**

    * Microsoft 管理 AS8075 以及以下 Asn： AS8068、AS8069、AS12076。 有关 AS8075 对等互连的 Asn 的完整列表，请参阅 MICROSOFT。
    * 在任何情况下，与 Microsoft 的所有对等方都同意不接受来自 AS12076 (Express Route) 的路由，并应筛选出所有对等方的 AS12076。

* **路由策略：**
    * 对等机将至少有一个公开可路由的/24。
    * Microsoft 将覆盖收到的多出口鉴别器 (中) 。
    * Microsoft 首选接收来自对等方的 BGP 社区标记，以指示路由源。
    * 建议对等互连在与 Microsoft 的对等互连会话上设置最大前缀 2000 (IPv4) 和 500 (IPv6) 路由。
    * 除非事先明确达成共识，否则对等方应在与 Microsoft 对等的所有位置公布一致的路由。
    * 通常，具有 AS8075 的对等互连会话将播发所有 AS-MICROSOFT 路由。 Microsoft 可能会公布一些区域信息。
    * 任何一方都不会建立静态路由、最后一次使用的路由，或以其他方式将流量发送到未通过 BGP 公布的路由。
    * 若要进行筛选，需要对等互连才能在公共 Internet 路由注册表 (IRR) 数据库中注册其路由，并将此信息保持为最新。      
    * 对等方将遵循路由安全的 MANRS 行业标准。  Microsoft 可以自行决定： 1. ) 不要与未签名和注册路由的公司建立对等互连;2. ) 删除无效的 RPKI 路由;3. ) 不接受来自未注册和签名的已建立对等方的路由。 

## <a name="operational-requirements"></a>操作要求
* 完全配备人员全天候的网络运营中心 (NOC) ，能够帮助解决所有技术和性能问题、安全违规、拒绝服务攻击或源自对等方或其客户的任何其他滥用。
* 对等端应在 [PeeringDB](https://www.peeringdb.com) 上具有完整的和最新的配置文件，包括来自企业域和电话号码的 24x7 NOC 电子邮件。 我们使用此信息来验证对等方的详细信息，例如 NOC 信息、技术联系人信息，以及它们在对等互连设施上的存在性等。不接受个人 Yahoo、Gmail 和 hotmail 帐户。

## <a name="physical-connection-requirements"></a>物理连接要求
* [PeeringDB](https://www.peeringdb.com/net/694)中列出了可以连接到 Microsoft 以实现直接对等互连或 Exchange 对等互连的位置。

* **Exchange 对等互连：**
    * 对等端应至少具有 10 Gb 的 exchange 连接。
    * 当高峰使用量超过50% 时，对等机应升级其端口。
    * Microsoft 鼓励同行维护与 exchange 的不同连接，以支持故障转移方案。

* **直接对等互连：**
    * 互连必须使用 100 Gbps 光学器件通过单模式光纤。
    * Microsoft 将仅与 ISP 或网络服务提供商建立直接对等互连。
    * 当高峰使用量超过50% 时，对等方应升级其端口，并在每个大都市范围内的每个大都市内维护不同的容量。
    * 每个直接对等互连都包含两个从对等机的路由器中的两个 Microsoft 边缘路由器的连接。 Microsoft 要求跨这些连接进行双重 BGP 会话。 对等方可以选择不在其一端部署冗余设备。


## <a name="traffic-requirements"></a>流量要求

* 跨 Exchange 对等互连的对等方必须具有至少 500 Mb 的流量并小于 2 Gb。 如果流量超过 2 Gb，应建立直接对等互连。
* 对于直接对等互连，Microsoft 至少需要 2 Gb。 双方双方都在对等互连位置都必须支持故障转移，以确保在故障转移方案期间对等互连保持本地化。 

## <a name="next-steps"></a>后续步骤

* 若要了解有关设置与 Microsoft 的直接对等互连的步骤，请遵循 [直接对等演练](walkthrough-direct-all.md)。
* 若要了解有关与 Microsoft 建立 Exchange 对等互连的步骤，请遵循 [exchange 对等互连演练](walkthrough-exchange-all.md)。
