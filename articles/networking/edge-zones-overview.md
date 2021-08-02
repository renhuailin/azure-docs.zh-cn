---
title: 关于 Azure Edge Zone 预览版
description: 了解 Microsoft 的边缘计算产品/服务：Azure Edge Zone。
services: vnf-manager
author: cherylmc
ms.service: vnf-manager
ms.topic: article
ms.date: 01/13/2021
ms.author: cherylmc
ms.openlocfilehash: 04555303d5128db6c183d27a0c5fcb69063fdc28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185404"
---
# <a name="about-azure-edge-zone-preview"></a>关于 Azure Edge Zone 预览版

Azure Edge Zone 是 Microsoft Azure 中的一个产品/服务系列，可以拉近数据处理设施与用户之间的距离。 可以将 VM、容器和其他所选 Azure 服务部署到 Edge Zone，以满足应用程序的低延迟和高吞吐量要求。

Edge Zone 的典型用例方案包括：

- 机器人实时指挥控制。
- 通过人工智能和机器学习进行实时分析与推理。
- 计算机视觉。
- 混合现实和 VDI 方案的远程渲染。
- 沉浸式多玩家游戏。
- 媒体流式传输和内容分发。
- 监控与保安。

有三种类型的 Azure Edge Zone：

- Azure Edge Zone
- Azure Edge Zones with Carrier
- Azure Private Edge Zone

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

![Azure Edge Zone](./media/edge-zones-overview/edge-zones.png "Azure Edge Zone")

Azure Edge Zone 是一个占用空间较小的 Azure 扩展，放置于远离 Azure 区域的人口密集区。 Azure Edge Zone 支持 VM、容器和选定的一组 Azure 服务，可让你在靠近最终用户的位置运行对延迟敏感的吞吐量密集型应用程序。 Azure Edge Zone 是 Microsoft 全球网络的一部分。 它们在靠近用户的边缘区域中运行的应用程序之间提供安全可靠的高带宽连接。 Azure Edge Zone 由 Microsoft 拥有和运营。 你可以使用同一组 Azure 工具和同一个门户在 Edge Zone 中管理和部署服务。

典型用例包括：

- 游戏和游戏流式传输。
- 媒体流式传输和内容分发。
- 通过人工智能和机器学习进行实时分析与推理。
- 混合现实的渲染。

以下大都会区将提供 Azure Edge Zone：

- 纽约州纽约市
- 加利福尼亚州洛杉矶
- 弗罗里达州迈阿密

有关详细信息，请[与 Edge Zone 团队联系](https://aka.ms/EdgeZones)。

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Azure Edge Zones with Carrier

![Edge Zones with Carrier](./media/edge-zones-overview/edge-carrier.png "Edge Zones with Carrier")

Azure Edge Zones with Carrier 是占用空间较小的 Azure 扩展，放置于人口密集区的移动运营商数据中心。 Azure Edge Zones with Carrier 基础设施放置在与移动运营商 5G 网络相距一个跃点的位置。 这样放置使得从移动设备访问应用程序的延迟不到 10 毫秒。

Azure Edge Zonse with Carrier 部署在移动运营商的数据中心，并连接到 Microsoft 全球网络。 它们在靠近用户运行的应用程序之间提供安全可靠的高带宽连接。 开发人员可以使用他们熟悉的同一套工具在 Edge Zone 中构建和部署服务。

典型用例包括：

- 游戏和游戏流式传输。
- 媒体流式传输和内容分发。
- 通过人工智能和机器学习进行实时分析与推理。
- 混合现实的渲染。
- 联网汽车。
- 远程医疗。

将与以下运营商合作提供 Edge Zone：

- AT&T（亚特兰大、达拉斯和洛杉矶）

正在开发连接到 5G 网络的已优化且可缩放应用程序的 ISV 现在可以使用 AT&T 在洛杉矶提供的 Azure Edge Zone 新预览位置，来构建和体验超低延迟平台以及移动和互联方案。 注册早期采用者计划即可享用安全的高带宽连接。

有关详细信息，请[与 Edge Zone 团队联系](https://aka.ms/EdgeZones)。

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure Private Edge Zone

![Private Edge Zone](./media/edge-zones-overview/private-edge.png "Private Edge Zone")

Azure Private Edge Zone 是放置于本地的占用空间较小的 Azure 扩展。 Azure Private Edge Zone 基于 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) 平台。 它可以实现低延迟访问本地部署的计算和存储服务。 Private Edge Zone 还可让你将 ISV 应用程序和虚拟化网络功能 (VNF) 作为 [Azure 托管应用程序](https://azure.microsoft.com/services/managed-applications/)连同虚拟机和容器一起部署在本地。 这些 VNF 包括移动封包核心、路由器、防火墙和 SD-WAN 设备。 Azure Private Edge Zone 附带云原生的业务流程解决方案，可让你在 Azure 门户中管理 VNF 和应用程序的生命周期。

Azure Private Edge Zone 可让你使用在 Azure 中构建和部署应用程序时所用的熟悉工具在本地开发和部署应用程序。

它还可用于：

- 运行专用移动网络（专用 LTE、专用 5G）。
- 实施防火墙等安全功能。
- 在相同 Private Edge Zone 设备上使用 SD-WAN 设备将本地网络扩展到多个分支和 Azure，并在 Azure 中对其进行管理。

典型用例包括：

- 机器人实时指挥控制。
- 通过人工智能和机器学习进行实时分析与推理。
- 计算机视觉。
- 混合现实和 VDI 方案的远程渲染。
- 监控与保安。

我们拥有丰富的生态系统，其中网罗了能够实现使用 Private Edge Zone 的端到端解决方案的 VNF 供应商、ISV 和 MSP 合作伙伴。 有关详细信息，请[与 Private Edge Zone 团队联系](https://aka.ms/EdgeZonesPartner)。

### <a name="private-edge-zone-partners"></a><a name="private-edge-partners"></a>Private Edge Zone 合作伙伴

![Private Edge Zone 合作伙伴](./media/edge-zones-overview/partners.png "Private Edge Zone 合作伙伴")

#### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>虚拟化网络功能 (VNF)

##### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>用于移动网络的虚拟化演进式封包核心 (vEPC)

- [Affirmed Networks](https://www.affirmednetworks.com/)
- [Celona](https://www.celona.io/azure-edge)
- [Druid Software](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nokia Digital Automation Cloud](https://www.dac.nokia.com/)

##### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>移动电台合作伙伴

- [Celona](https://www.celona.io/azure-edge)
- [Commscope Ruckus](https://support.ruckuswireless.com/)

##### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN 供应商

- [128 Technology](https://www.128technology.com/)
- [NetFoundry](https://netfoundry.io/)
- [Nuage Networks from Nokia](https://www.nuagenetworks.net/)
- [Versa Networks](https://www.versa-networks.com/)
- [VMware SD-WAN by Velocloud](https://www.velocloud.com/)

##### <a name="router-vendors"></a><a name="router-vendors"></a>路由器供应商

- [Arista](https://www.arista.com/)

##### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>防火墙供应商

- [Palo Alto Networks](https://www.paloaltonetworks.com/)

##### <a name="managed-solutions-providers-mobile-operators-and-global-system-integrators-gsis"></a><a name="msp-mobile"></a>托管解决方案提供商：移动运营商和全球系统集成商 (GSI)

| GSI 和运营商 | 移动运营商 |
| --- | --- |
| Amdocs                       | Etisalat             |
| American Tower               | NTT 通信   |
| CenturyLink                  | Proximus             |
| Expeto                       | Rogers               |
| Federated Wireless           | SK Telecom           |
| Infosys                      | Telefonica           |
| Tech Mahindra                | Telstra              |
|                              | Vodafone             |

有关如何成为合作伙伴的信息，请[与 Private Edge Zone 团队联系](https://aka.ms/EdgeZonesPartner)。

### <a name="private-edge-zone-solutions"></a><a name="solutions-private-edge"></a>Private Edge Zone 解决方案

#### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Private Edge Zone 中的专用移动网络

![Private Edge Zone 中的专用移动网络](./media/edge-zones-overview/mobile-networks.png "Private Edge Zone 中的专用移动网络")

现在，你可以在 Private Edge Zone 中部署专用移动网络。 专用移动网络可以实现业务关键型应用程序所需的超低延迟、高容量、可靠且安全的无线网络。

专用移动网络可实现如下所述的方案：
- 指挥和控制仓库中的自动导引运输车 (AGV)。
- 在智能工厂中的机器人之间进行实时通信。
- 增强现实和虚拟现实边缘应用程序。

虚拟化演进式封包核心 (vEPC) 网络功能相当于专用移动网络的大脑。 现在你可以在 Private Edge Zone 中部署 vEPC。 有关 Private Edge Zone 支持的 vEPC 合作伙伴列表，请参阅 [vEPC ISV](#vEPC)。

在 Private Edge Zone 中部署专用移动网络解决方案需要其他组件，例如移动接入点、SIM 卡和其他 VNF（如路由器）。 访问已授权或未授权频段对于设置专用移动网络至关重要。 此外，你可能需要 RF 规划、物理布局、安装和支持方面的帮助。 有关合作伙伴列表，请参阅[移动电台合作伙伴](#mobile-radio)。

Microsoft 提供了一个合作伙伴生态系统，可帮助完成此过程的各个方面。 合作伙伴可以帮助规划网络、采购所需设备、设置硬件以及在 Azure 中管理配置。 与 Microsoft 紧密集成且经过证实的众多合作伙伴将确保你的解决方案可靠且易于使用。 你可以专注于自己的核心方案，并依赖 Microsoft 及其合作伙伴来帮助解决余下的事情。

#### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>Private Edge Zone 中的 SD-WAN

![Private Edge Zone 中的 SD-WAN](./media/edge-zones-overview/sd-wan.png "Private Edge Zone 中的 SD-WAN")

使用 SD-WAN 可以创建具有以下优势的企业级广域网 (WAN)：

- 更高的带宽
- 对云进行高性能访问
- 服务插入
- 可靠性
- 策略管理
- 宽广的网络视野

SD-WAN 提供可在冗余中心控制器中协调的无缝分支机构连接，只需花费较低的费用即可拥有此连接。
Private Edge Zone 中的 SD-WAN 可让你从以 capex 为中心的模型转移到软件即服务 (SaaS) 模型，以降低 IT 预算。 可以使用你选择的 SD-WAN 合作伙伴、业务流程协调程序或控制器启用新服务，并立即将其传播到整个网络。

## <a name="next-steps"></a>后续步骤

有关详细信息，请与以下团队联系：

* [Edge Zone 团队](https://aka.ms/EdgeZones)
* [Private Edge Zone 团队（如果想要成为合作伙伴）](https://aka.ms/EdgeZonesPartner)
