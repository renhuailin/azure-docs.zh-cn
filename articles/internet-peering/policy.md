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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592323"
---
# <a name="peering-policy"></a>对等互连策略
Microsoft 维护了一个选择性的对等互连策略，旨在确保提供最佳客户体验（由行业标准和最佳做法提供支持），并可根据未来的需求和对等互连的战略定位进行调整。 因此，Microsoft 保留在必要时对策略进行例外处理的权利。 Microsoft 对网络的一般要求在以下各节中进行了介绍。 它们适用于直接对等互连请求和 Exchange 对等互连请求。 

## <a name="technical-requirements"></a>技术要求

* 具有足够容量的完全冗余网络，且在交换通信时不会发生拥塞。
* 对等将具有可公开路由的自治系统编号 (ASN)。
* IPv4 和 IPv6 均受支持，Microsoft 希望在每个对等互连位置建立两种类型的会话。
* 不支持 MD5。
* ASN 详细信息：

    * Microsoft 管理 AS8075 以及以下 ASN：AS8068、AS8069、AS12076。 有关 AS8075 对等互连的 ASN 的完整列表，请参阅 AS-SET MICROSOFT。
    * 与 Microsoft 对等互连的所有各方均同意在任何情况下均不接受来自 AS12076（快速路由）的路由，并应筛选掉所有对等方的 AS12076。

* 路由策略：
    * 对等方将至少有一个可公开路由的 /24。
    * Microsoft 将重写收到的多出口鉴别器 (MED)。
    * Microsoft 优先接收来自对等方的用于指示路由源的 BGP 社区标记。
    * 建议对等方在与 Microsoft 的对等互连会话上将最大前缀设置为 2000 (IPv4) 和 500 (IPv6) 路由。
    * 除非事先明确达成共识，否则对等方应在与 Microsoft 对等的所有位置公布一致的路由。
    * 通常，具有 AS8075 的对等互连会话将播发所有 AS-MICROSOFT 路由。 Microsoft 可能会公布一些具体的区域性信息。
    * 任何一方都不会建立静态路由、不得已使用的路由，或以其他方式将流量发送到未通过 BGP 公布的路由的另一方。
    * 出于筛选目的，对等方需要在公共 Internet 路由注册表 (IRR) 数据库中注册其路由，并将此信息保持为最新。      
    * 对等方将遵守 MANRS 行业标准，以确保路由安全。  Microsoft 可自行选择：1.）不与未签名和注册路由的公司建立对等互连；2.）删除无效的 RPKI 路由；3.）不接受来自已建立但未注册和签名的对等方的路由。 

## <a name="operational-requirements"></a>操作要求
* 人员配备齐全的全天候网络运营中心 (NOC)，能够帮助解决所有技术和性能问题、安全违规、拒绝服务攻击或对等方或其客户的任何滥用行为。
* 对等方应在 [PeeringDB](https://www.peeringdb.com) 上具有完整且最新的配置文件，包括来自企业域的全天候 NOC 电子邮件和电话号码。 我们使用这些信息来验证对等方的详细信息（例如 NOC 信息、技术联系信息以及它们在对等互连设施中是否存在，等等）。不接受个人 Yahoo、Gmail 和 Hotmail 帐户。

## <a name="physical-connection-requirements"></a>物理连接要求
* [PeeringDB](https://www.peeringdb.com/net/694) 中列出了可以连接到 Microsoft 以实现直接对等互连或 Exchange 对等互连的位置

* **Exchange 对等互连：**
    * 对等方应具有至少 10 Gb 的 exchange 连接。
    * 当高峰使用量超过 50% 时，对等方应升级其端口。
    * Microsoft 鼓励对等维护与 exchange 的不同连接，以支持故障转移场景。

* **直接对等互连：**
    * 互连必须使用 100 Gbps 光学器件通过单模式光纤。
    * Microsoft 将仅与 ISP 或网络服务提供商建立直接对等互连。
    * 当高峰使用量超过 50% 时，对等应升级其端口，并在每个地铁范围内维护不同的容量（无论是在地铁内的一个位置是多个位置）。
    * 每个直接对等互连都包含两个连接，这两个连接从位于对等边缘的对等路由器指向两个 Microsoft Edge 路由器。 Microsoft 要求跨这些连接进行双重 BGP 会话。 对等方可以选择不在自端部署冗余设备。


## <a name="traffic-requirements"></a>流量要求

* 通过 Exchange 对等互连的对等方必须具有至少 500 Mb 但小于 2 Gb 的流量。 如果流量超过 2 Gb，应建立直接对等互连。
* Microsoft 要求进行直接对等互连时至少需要 2 Gb。 每个达成一致的对等互连位置必须支持故障转移，以确保对等互连在故障转移场景中保持本地化。 

## <a name="next-steps"></a>后续步骤

* 若要了解如何通过相关步骤设置与 Microsoft 的直接对等互连，请按[直接对等互连演练](walkthrough-direct-all.md)进行操作。
* 若要了解如何通过相关步骤设置与 Microsoft 的 Exchange 对等互连，请按 [Exchange 对等互连演练](walkthrough-exchange-all.md)进行操作。
