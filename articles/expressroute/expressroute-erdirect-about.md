---
title: 关于 Azure ExpressRoute Direct
description: 了解 Azure ExpressRoute Direct 的主要功能和载入到 ExpressRoute Direct 所需的信息，如可用的 SKU 和相关技术要求。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: duau
ms.openlocfilehash: 01de2c75cbb6c1f8b2e153e18add612608e75ac7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433924"
---
# <a name="about-expressroute-direct"></a>关于 ExpressRoute Direct

使用 ExpressRoute Direct，可以直接连接到 Microsoft 战略性分布在全球各地的对等互连位置的全球网络。 ExpressRoute Direct 提供双 100 Gbps 或 10 Gbps 连接，支持大规模的主动/主动连接。 你可以使用任何服务提供程序来实现 ER 定向。

ExpressRoute Direct 提供的主要功能包括但不限于：

* 大规模数据引入到存储（如存储和 Cosmos DB）
* 针对受监管和需要专用和独立连接的行业的物理隔离，例如：银行、政府和零售
* 根据业务部门，细化控制线路分布

## <a name="onboard-to-expressroute-direct"></a>载入到 ExpressRoute Direct

需要先注册订阅，然后才能使用 ExpressRoute Direct。 若要注册，请使用 Azure PowerShell 运行以下命令：

1.  登录到 Azure 并选择想要注册的订阅。

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. 使用以下命令注册公共预览版订阅：

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

注册后，验证 Microsoft.Network 资源提供程序是否已在你的订阅中注册。 通过注册资源提供程序来配置订阅，以供资源提供程序使用。

1. 按照 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)中所述访问你的订阅设置。

1. 在你的订阅中，对于“资源提供程序”，验证 Microsoft.Network 提供程序是否显示为“已注册”状态。 如果已注册的提供程序列表中不存在 Microsoft.Network 资源提供程序，请添加该提供程序。

如果开始使用 ExpressRoute Direct 后发现所选的对等互连位置没有可用的端口，请记录支持请求以请求更多库存。

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>使用服务提供程序的 ExpressRoute，以及 ExpressRoute Direct

| **使用服务提供程序的 ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| 使用服务提供程序启用快速载入并连接到现有的基础结构 | 要求 100 Gbps/10 Gbps基础结构以及对所有层进行全面管理
| 集成数百个提供程序，包括以太网和 MPLS | 直接/专用容量，适用于受管制行业和大规模数据引入 |
| 从 50 Mbps 到 10 Gbps 的线路 SKU | 客户可以在 100 Gbps ExpressRoute Direct 上选择以下线路 SKU 的组合： <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> 客户可以在 10 Gbps ExpressRoute Direct 上选择以下线路 SKU 的组合：<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| 针对单租户优化 | 针对具有多个业务部门和多个工作环境的单个租户进行了优化

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct 线路

使用 Microsoft Azure ExpressRoute，可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Microsoft Azure 和 Microsoft 365 等 Microsoft 云服务建立连接。

每个对等互连位置都可以访问 Microsoft 全球网络，并且默认可访问地缘政治区域中的任何区域。 可以访问使用高级线路的全球所有区域。  

大多数方案中的功能都相当于使用 ExpressRoute 服务提供商来运营的线路。 若要支持进一步细分以及通过 ExpressRoute Direct 提供的新功能，可以使用 ExpressRoute Direct 线路上存在的某些关键功能。

## <a name="circuit-skus"></a>线路 SKU

ExpressRoute Direct 支持将数据大规模引入到 Azure 存储和其他大数据服务中的方案。 100-Gbps ExpressRoute Direct 上的 ExpressRoute 线路现在也支持 40 Gbps 和 100 Gbps 线路 SKU。  物理端口对仅为 100 或 10 Gbps，可以有多个虚拟线路。 线路大小：

| 100 Gbps ExpressRoute Direct | 10 Gbps ExpressRoute Direct | 
| --- | --- |
| 订阅的带宽：200 Gbps | 订阅的带宽：20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>技术要求

* Microsoft Enterprise Edge Router (MSEE) 接口：
    * 双 10 千兆位或 100 千兆位以太网端口，仅跨路由器对
    * 单模式 LR 纤程连接
    * IPv4 和 IPv6
    * IP MTU 1500 字节

* 交换机/路由器第 2 层/第 3 层连接：
    * 必须支持 1 802.1Q (Dot1Q) 标记或两个 Tag 802.1Q (QinQ) 标记封装
    * Ethertype = 0x8100
    * 必须基于 Microsoft 指定的 VLAN ID 添加外部 VLAN 标记 (STAG) - 仅适用于 QinQ
    * 必须支持每个端口和设备多个 BGP 会话 (VLAN)
    * IPv4 和 IPv6 连接。 对于 IPv6，将不会创建其他子接口。将向现有子接口添加 IPv6 地址。 
    * 可选：[双向转发检测 (BFD)](./expressroute-bfd.md) 支持，默认在 ExpressRoute 线路上的所有专用对等互连上配置

## <a name="vlan-tagging"></a>VLAN 标记

ExpressRoute Direct 同时支持 QinQ 和 Dot1Q VLAN 标记。

* **QinQ VLAN 标记** 允许基于单个 ExpressRoute 线路的隔离路由域。 Azure 在创建线路时动态分配一个不能更改的 S-Tag。 线路上的每个对等互连（专用对等互连和 Microsoft 对等互连）都会使用唯一的 C-Tag 作为 VLAN。 C-Tag 在 ExpressRoute Direct 端口的所有线路中不需要独一无二。

* **Dot1Q VLAN 标记** 允许基于单个 ExpressRoute Direct 端口对的单个标记 VLAN。 在对等互连上使用的 C-Tag 必须在 ExpressRoute Direct 端口对的所有线路和对等互连中独一无二。

## <a name="workflows"></a>工作流

### <a name="set-up-expressroute-direct"></a>设置 ExpressRoute Direct

:::image type="content" source="./media/expressroute-erdirect-about/set-up-workflow.png" alt-text="ExpressRoute Direct 设置工作流的关系图。" lightbox="./media/expressroute-erdirect-about/set-up-workflow-expanded.png":::

### <a name="delete-expressroute-direct"></a>删除 ExpressRoute Direct

:::image type="content" source="./media/expressroute-erdirect-about/delete-workflow.png" alt-text="ExpressRoute Direct 删除工作流的关系图。" lightbox="./media/expressroute-erdirect-about/delete-workflow-expanded.png":::

## <a name="sla"></a>SLA

ExpressRoute Direct 提供相同的企业级 SLA，并且可以通过主动/主动冗余连接连接到 Microsoft 全球网络中。 ExpressRoute 基础设施是冗余的，连接到 Microsoft 全球网络的功能也是冗余的，并且具有多样性，可以根据客户需求进行适当的缩放。

## <a name="pricing"></a>定价

若要详细了解 ExpressRoute Direct 如何计费，请参阅 [ExpressRoute 常见问题解答](expressroute-faqs.md#when-does-billing-start-and-stop-for-the-expressroute-direct-port-pairs)。 有关定价的详细信息，请参阅 [ExpressRoute 定价](https://azure.microsoft.com/pricing/details/expressroute/)。

## <a name="next-steps"></a>后续步骤

了解如何[配置 ExpressRoute Direct](expressroute-howto-erdirect.md)。
