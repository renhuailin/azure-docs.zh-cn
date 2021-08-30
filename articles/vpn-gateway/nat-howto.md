---
title: 在 Azure VPN 网关上配置 NAT
titleSuffix: Azure VPN Gateway
description: 了解如何在 Azure VPN 网关上配置 NAT。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/24/2021
ms.author: yushwang
ms.openlocfilehash: 8b9d3d298986aa4aae6ead4e9733c9b0994b1f91
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113214058"
---
# <a name="how-to-configure-nat-on-azure-vpn-gateways-preview"></a>如何在 Azure VPN 网关上配置 NAT（预览版）

本文可帮助你使用 Azure 门户在 Azure VPN 网关上配置 NAT（网络地址转换）。

## <a name="about-nat"></a><a name="about"></a>关于 NAT

NAT 定义了在 IP 数据包中将一个 IP 地址转换为另一个 IP 地址的机制。 它通常用于连接具有重叠 IP 地址范围的网络。 连接网络的网关设备上的 NAT 规则或策略指定网络上地址转换的地址映射。

有关 Azure VPN 网关上的 NAT 支持的详细信息，请参阅[关于 Azure VPN 网关上的 NAT](nat-overview.md)。

> [!IMPORTANT] 
> Azure VPN 网关 NAT 目前为预览版。 
> * 请务必阅读本文的[预览限制](#limits)部分。
> * Azure VPN 网关 NAT 仅支持静态的 1:1 NAT 规则。 不支持动态 NAT 规则。
> * 以下 SKU 支持 NAT：VpnGw2~5、VpnGw2AZ~5AZ.。

## <a name="getting-started"></a>入门

本文帮助你在网络连接中配置 NAT，其中的每一部分都是一个基本构建基块。 如果完成所有三个部分，则你构建了如图示 1 所示的拓扑：

### <a name="diagram-1"></a><a name="diagram">图示 1</a>

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="图示 1 的屏幕截图。" lightbox="./media/nat-overview/vpn-nat.png" border="false":::

### <a name="prerequisites"></a>先决条件

* 确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 查看[预览限制](#limits)。

## <a name="part-1-create-vnet-and-gateways"></a><a name ="vnet"></a>第 1 部分：创建 VNet 和网关

在本部分中，创建虚拟网络、VPN 网关和本地网关资源，以与[图示 1](#diagram) 中所示的资源对应。

若要创建这些资源，请使用[站点到站点教程](tutorial-site-to-site-portal.md)一文中的步骤。 完成本文中的以下部分，但不要创建任何连接。

* [VNet](tutorial-site-to-site-portal.md#CreatVNet)
* [VPN 网关](tutorial-site-to-site-portal.md#VNetGateway)
* [本地网关](tutorial-site-to-site-portal.md#LocalNetworkGateway)
* [配置 VPN 设备](tutorial-site-to-site-portal.md#VPNDevice)


>[!IMPORTANT]
>  使用以下文章中的步骤时，请勿在文章中创建连接资源。 操作将失败，因为 VNet、分支 1 和分支 2 之间的 IP 地址空间相同。 使用以下部分中的步骤创建 NAT 规则，然后使用 NAT 规则创建连接。
>


以下屏幕截图显示了要创建的资源的示例。

* **VNet**

   :::image type="content" source="./media/nat-howto/vnet.png" alt-text="显示 VNet 地址空间的屏幕截图。" lightbox="./media/nat-howto/vnet.png":::
* **VPN 网关**

   :::image type="content" source="./media/nat-howto/vpn-gateway.png" alt-text="显示网关的屏幕截图。" lightbox="./media/nat-howto/vpn-gateway.png":::
* **分支 1 本地网关**

   :::image type="content" source="./media/nat-howto/branch-1.png" alt-text="显示分支 1 本地网关的屏幕截图。" lightbox="./media/nat-howto/branch-1.png" :::

* **分支 2 本地网关**

   :::image type="content" source="./media/nat-howto/branch-2.png" alt-text="显示分支 2 本地网关的屏幕截图。" lightbox="./media/nat-howto/branch-2.png":::

   > [!IMPORTANT] 
   > 在预览期间，如果本地网关地址空间等于或小于 VNet 地址空间，请使用 BGP，并将本地网关地址空间字段留空 。 在预览期间，此方案不支持静态路由（非 BGP）。
   >

## <a name="part-2-create-nat-rules"></a><a name ="nat-rules"></a>第 2 部分：创建 NAT 规则

在创建连接之前，必须在 VPN 网关上创建并保存 NAT 规则。 下表显示了必需的 NAT 规则。 对于拓扑，请参阅[图示 1](#diagram)。

**NAT 规则表**

| 名称     | 类型   | 模型        | 内部    | 外部     | 连接          |
| ---      | ---    | ---         | ---         | ---          | ---                 |
| VNet     | 静态 | EgressSNAT  | 10.0.1.0/24 | 100.0.1.0/24 | 两个连接    | 
| Branch_1 | 静态 | IngressSNAT | 10.0.1.0/24 | 100.0.2.0/24 | 分支 1 连接 |
| Branch_2 | 静态 | IngressSNAT | 10.0.1.0/24 | 100.0.3.0/24 | 分支 2 连接 |

使用以下步骤在 VPN 网关上创建所有 NAT 规则。

1. 在 Azure 门户中，导航到“虚拟网络网关”资源页，然后选择“NAT 规则(预览版)”。 
1. 使用上面的 NAT 规则表，填写值。

   :::image type="content" source="./media/nat-howto/nat-rules.png" alt-text="显示 NAT 规则的屏幕截图。" lightbox="./media/nat-howto/nat-rules.png":::
1. 单击“保存”将 NAT 规则保存到 VPN 网关资源。 此操作可能最多需要 10 分钟才能完成。

## <a name="part-3-create-connections-and-link-nat-rules"></a><a name ="connections"></a>第 3 部分：创建连接和链接 NAT 规则

在本部分中，创建连接，然后将 NAT 规则与连接相关联，以实现[图示 1](#diagram)中的示例拓扑。

### <a name="1-create-connections"></a>1. 创建连接

按照[创建站点到站点连接](tutorial-site-to-site-portal.md)文中的步骤创建两个连接，如下所示：

   :::image type="content" source="./media/nat-howto/connections.png" alt-text="显示“连接”页的屏幕截图。" lightbox="./media/nat-howto/connections.png":::

### <a name="2-associate-nat-rules-with-the-connections"></a>2. 将 NAT 规则与连接关联

在此步骤中，将 NAT 规则与每个连接资源相关联。

1. 在 Azure 门户中，导航到连接资源，然后选择“配置”。

1. 在“入口 NAT 规则”下，选择先前创建的 NAT 规则。

   :::image type="content" source="./media/nat-howto/config-nat.png" alt-text="显示配置的 NAT 规则的屏幕截图。" lightbox="./media/nat-howto/config-nat.png":::

1. 单击“保存”以将配置应用于连接资源。

1. 重复上述步骤，为其他连接资源应用 NAT 规则。

1. 如果使用 BGP，请在“NAT 规则”页中选择“启用 BGP 路由转换”，然后单击“保存” 。 请注意，该表现在显示了与每个 NAT 规则关联的连接。

   :::image type="content" source="./media/nat-howto/nat-rules-linked.png" alt-text="显示“启用 BGP”的屏幕截图。" lightbox="./media/nat-howto/nat-rules-linked.png":::

完成这些步骤后，你的设置将与[图示 1](#diagram) 中所示的拓扑相匹配。

## <a name="preview-limitations"></a><a name ="limits"></a>预览版限制

> [!IMPORTANT] 
> NAT 功能预览期间有几个限制。 其中一些限制将在正式发布之前得以解决。
>

* Azure VPN 网关 NAT 仅支持静态的 1:1 NAT 规则。 不支持动态 NAT 规则。
* 以下 SKU 支持 NAT：VpnGw2~5、VpnGw2AZ~5AZ。
* 仅 IPsec/IKE 跨界连接支持 NAT。 不支持 VNet 到 VNet 连接或 P2S 连接。
* 创建连接过程中，NAT 规则不能与连接资源关联。 首先创建连接资源，然后在“连接配置”页中关联 NAT 规则。
* 对于预览版，如果本地网关地址空间等于或小于 VNet 地址空间，请使用 BGP，并将本地网关地址空间留空 。 由于本地网关与 VNet 之间存在地址冲突，不支持静态路由（非 BGP）
* 不同本地网关（本地网络或分支）的地址空间可以与 IngressSNAT 规则相同，以映射到不重叠的前缀，如[图示 1](#diagram) 所示。
* 已启用“使用基于策略的流量选择器”的连接不支持 NAT 规则。

## <a name="next-steps"></a>后续步骤

连接完成后，即可将虚拟机添加到虚拟网络。 请参阅 [创建虚拟机](../virtual-machines/windows/quick-create-portal.md) 以获取相关步骤。
