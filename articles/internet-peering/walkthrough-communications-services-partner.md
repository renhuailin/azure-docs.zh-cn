---
title: 用于通信服务的 Azure Internet 对等互连演练
titleSuffix: Azure
description: 用于通信服务的 Azure Internet 对等互连演练
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: 395ca8022b0355ff79c200dfbe87bc78e29de089
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060851"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>用于通信服务的 Azure Internet 对等互连演练

本节介绍通信服务提供商与 Microsoft 建立直接互连所需执行的步骤。

通信服务提供商：通信服务提供商是提供通信服务（通信、消息传递和会议）的组织，他们希望将其通信服务基础结构（SBC/SIP 网关等）与 Azure 通信服务和 Microsoft Teams 集成。 

Azure Internet 对等互连可帮助通信服务提供商在 Microsoft 的任何边缘站点（热门位置）与其建立直接互连。 [PeeringDB](https://www.peeringdb.com/net/694) 中提供了所有公共边缘站点的列表。

Azure Internet 对等互连为通信服务提供高度可靠且启用服务质量 (Quality of Service, QoS)，以确保交付以性能为中心的高质量服务。

## <a name="technical-requirements"></a>技术要求
建立用于通信服务的直接互连的技术要求如下所示：
-   对等机必须提供自己的公共自治系统编号 (Autonomous System Number, ASN)。
-   对等机在每个互连位置 (PNI) 必须具有冗余互连，以确保本地冗余。
-   对等机必须已准备好异地冗余，以确保在区域/大都市圈出现站点故障时进行故障转移。
-   对等机必须将 BGP 会话设置为主动-主动以确保高可用性和更快的收敛，并且不应预配为主要和备份。
-   对等机的对等互连路由器与对等互连线路之比保持为 1:1，并且不应用速率限制。
-   对等机必须提供并播发自己的可公开路由 IPv4 地址空间，以供对等机的通信服务终结点（例如 SBC）使用。 
-   对等机必须详细说明驻留在每个播发的子网中的流量类和终结点类。 
-   对等机必须通过双向转发检测 (Bi-directional Forwarding Detection, BFD) 运行 BGP，以实现亚秒级路由收敛。
-   在 Azure 门户中注册所有通信基础结构前缀，并使用团体字符串 8075:8007 播发。
-   对等机不得在运行有状态防火墙的设备上终止对等互连。 
-   默认情况下，Microsoft 会将所有互连链路配置为 LAG（链路束），因此，对等机必须在互连链路上支持链路聚合控制协议 (Link Aggregation Control Protocol, LACP)。

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>与 Microsoft 建立用于通信服务的直接互连。

若要使用 Azure Internet 对等互连建立直接互连，请执行以下步骤：

1\.    将对等机公共 ASN 与 Azure 订阅相关联：

如果对等机已将公共 ASN 与 Azure 订阅相关联，请跳过此步骤。

[使用门户将对等机 ASN 与 Azure 订阅相关联 - Azure | Microsoft Docs](./howto-subscription-association-portal.md)

下一步是为对等互连服务创建直接对等连接。

> [!NOTE]
> ASN 关联获得批准后，请在电子邮件中附加你的 ASN 和订阅 ID 并发送至 peeringservice@microsoft.com，以将你的订阅与通信服务相关联。 

2\.    为对等互连服务创建直接对等互连连接：

按照说明[使用门户创建或修改直接对等互连](./howto-direct-portal.md)

确保它满足高可用性要求。

请确保在“创建对等互连”页上选择以下选项：

“对等互连类型”：“直接”

“Microsoft 网络”：“8075”

“SKU”：“高级免费”


在“直接对等互连连接”页下，选择以下选项：

“会话地址提供商”：“Microsoft”

“用于对等互连服务”：“启用”

> [!NOTE] 
> 选择激活对等互连服务时，请忽略以下消息。
> *除非你已发送电子邮件至 peering@microsoft.com 了解关于成为 MAPS 提供商的相关信息，否则请勿启用它。*


  2a. 将现有直接对等互连连接用于对等互连服务

如果要使用现有直接对等互连为对等互连服务提供支持，可在 Azure 门户上激活它。
1.  按照说明[使用门户将旧版直接对等互连转换为 Azure 资源](./howto-legacy-direct-portal.md)。
根据需要订购额外线路以满足高可用性要求。

2.  按照相应步骤使用门户在直接对等互连上[启用对等互连服务](./howto-peering-service-portal.md)。




3\.    注册前缀以优化路由

若要优化通信服务基础结构前缀的路由，应向对等互连注册你的所有前缀。
[注册 Azure 对等互连服务 - Azure 门户 | Microsoft Docs](../peering-service/azure-portal.md)

系统会为通信服务合作伙伴自动填充前缀键，因此，合作伙伴无需使用任何前缀键进行注册。 

请确保在为区域建立的直接互连上公布要注册的前缀。


## <a name="faqs"></a>常见问题解答：

**Q.**  我的通信服务具有较小的子网（少于 24 个）。 较小的子网是否也可以路由？

**A.**  可以，Microsoft Azure 对等互连服务也支持较小的前缀路由。 请确保为路由注册较小的前缀，并在互连上公布这些前缀。

**Q.**  我们将通过这些互连获得哪些 Microsoft 路由？

**A.** Microsoft 在这些互连上公布 Microsoft 的所有公共服务前缀。 这将确保可通过同一个互连访问通信服务及其他云服务。

**Q.**  我需要设置前缀限制，Microsoft 将公布的路由数目是多少？

**A.** Microsoft 在 Internet 上公布大约 280 个前缀，以后可能会增加 10% 到 15%。 因此，将“最大前缀计数”设置为 400 到 500 是安全的做法。

**Q.** Microsoft 是否会将对等机前缀重新播发到 Internet？

**A.** 不是。

**Q.** 此服务是否需要付费？

**A.** 不需要，但对等机需要承担站点交叉连接费用。

**Q.** 互连的最低链路速度是多少？

**A.** 10Gbps。

**Q.** 对等机是否绑定到 SLA？

**A.** 是，一旦利用率达到 40%，必须开始为期 45 到 60 天的 LAG 增加流程。

**Q.** 此服务与当前的直接对等互连或快速路由相比具有哪些优势？

**A.** 无需结算，整个路径针对 Microsoft WAN 上的语音流量进行了优化，并且通过 BFD 将收敛缩短至亚秒级。

**Q.** 完成加入过程需要多长时间？

**A.** 所需时间因以下因素而异：站点数和站点位置、对等机是要迁移现有的专用对等互连还是建立新线路。 运营商应计划 3 周以上的时间。

**Q.** 是否可以使用 API 来加入？

**A.** 目前未提供 API 支持，必须通过 Web 门户执行配置。
