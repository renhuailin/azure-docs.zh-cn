---
title: 方案：虚拟网络和分支的自定义隔离
titleSuffix: Azure Virtual WAN
description: 了解可防止选定 VNet 和分支相互访问的虚拟 WAN 路由方案。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 49e6a65bf14b3d7678a996685efde1b1a2c0a9d3
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719693"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>方案：虚拟网络和分支的自定义隔离

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在虚拟网络 (VNet) 和分支的自定义隔离方案中，目标是阻止一组特定的 VNet 访问另一组 VNet。 同样，只会允许分支（VPN/ER/用户 VPN）联系特定的 VNet 组。

我们还引入了其他要求，即 Azure 防火墙应检查分支到 VNet 以及 VNet 到分支的流量，但不检查 VNet 到 VNet 的流量。  

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>设计

为了确定将会需要多少个路由表，可以构建一个连接矩阵。 对于此方案，它将如下所示，其中每个单元格表示源（行）是否可以与目标（列）通信：

| 源 | 到:| *蓝色 VNet* | *红色 VNet* | 红色分支| 蓝色分支| 
|---|---|---|---|---|---|
| **蓝色 VNet** |   &#8594;|   直接     |           |   |  AzFW|
| **红色 VNet**  |   &#8594;|              |   直接  |  AzFW  | 
| 红色分支   |   &#8594;|   |   AzFW  |  直接 | 直接
| 蓝色分支| &#8594;| AzFW  |   |直接   | 直接

上表中的各单元格描述了虚拟 WAN 连接（流的“源”端，行标题）是否与目标（流的“目标”端，斜体形式的列标题）通信。 “直接”表示流量直接流经虚拟 WAN，而“AzFW”则表示 Azure 防火墙会在将流量转发到目标之前对其进行检查。 空白条目表示流在设置中被阻止。

在本例中，需要使用 VNet 的两个路由表在路径中没有 Azure 防火墙的情况下实现 VNet 隔离目标。 我们将这些路由表称为 RT_BLUE 和 RT_RED。

此外，分支必须始终关联到“Default”路由表。 若要确保 Azure 防火墙检查进出分支的流量，请在“Default”、“RT_RED”和“RT_BLUE”路由表中添加将流量指向 Azure 防火墙的静态路由，并设置网络规则以允许所需的流量。 我们还确保分支不传播到 RT_BLUE 和 RT_RED。

这样，最终设计如下：

* 蓝色虚拟网络：
  * 关联的路由表：**RT_BLUE**
  * 传播到路由表：RT_BLUE
* 红色虚拟网络：
  * 关联的路由表：**RT_RED**
  * 传播到路由表：RT_RED 
* 分支：
  * 关联的路由表：**默认**
  * 传播到路由表：**默认**
* 静态路由：
    * 默认路由表：虚拟网络地址空间，下一跃点为 Azure 防火墙
    * RT_RED：0.0.0.0/0，下一跃点为 Azure 防火墙
    * RT_BLUE：0.0.0.0/0，下一跃点为 Azure 防火墙
* 防火墙网络规则：
    *  允许规则 - 源前缀：蓝色分支地址前缀 目标前缀：蓝色 VNet 前缀 
    *  允许规则 - 源前缀：红色分支地址前缀 目标前缀：红色 VNet 前缀

> [!NOTE]
> 由于所有分支都需要关联到 Default 路由表，并且要传播到同一组路由表，因此所有分支都将具有相同的连接性配置文件。 换句话说，VNet 的红色/蓝色概念不能应用于分支。 但是，若要实现分支的自定义路由，我们可以将流量从分支转发到 Azure 防火墙。

> [!NOTE]
> 默认情况下，Azure 防火墙拒绝零信任模型中的流量。 如果没有与已检查的数据包匹配的显式允许规则，则 Azure 防火墙会丢弃该数据包。

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。



## <a name="workflow"></a><a name="architecture"></a>工作流

在图 1 中，有蓝色和红色 VNet 以及可访问蓝色或红色 VNet 的分支。

* 蓝色连接的 VNet 可以相互联系，并且可以访问所有蓝色分支 (VPN/ER/P2S) 连接。 在示意图中，蓝色分支是站点到站点 VPN 站点。
* 红色连接的 VNet 可以相互联系，并且可以访问所有红色分支 (VPN/ER/P2S) 连接。 在示意图中，红色分支是点到站点 VPN 用户。

设置路由时，请考虑以下步骤。

1. 在 Azure 门户中创建两个自定义路由表（RT_BLUE 和 RT_RED），以便自定义到这些 VNet 的流量。
2. 对于路由表 RT_BLUE，请应用以下设置以确保蓝色 VNet 了解所有其他蓝色 VNet 的地址前缀：
   * **Association**：选择所有蓝色 VNet。
   * 传播：选择所有蓝色 VNet。
3. 对于与红色 VNet 对应的 RT_RED 路由表，请重复相同的步骤。
4. 在虚拟 WAN 中预配 Azure 防火墙。 有关虚拟 WAN 中心内的 Azure 防火墙的详细信息，请参阅[在虚拟 WAN 中心内配置 Azure 防火墙](howto-firewall.md)。
5. 将一个静态路由添加到虚拟中心的 Default 路由表，将目标为 VNet 地址空间（蓝色和红色）的所有流量定向到 Azure 防火墙。 此步骤可确保将分支中的任何数据包发送到 Azure 防火墙进行检测。
    * 示例：目标前缀：10.0.0.0/8 下一跃点：Azure 防火墙
    >[!NOTE]
    > 还可以使用防火墙管理器通过选择“安全专用流量”选项来执行此步骤。 这会为适用于 VNet 和分支的所有 RFC1918 专用 IP 地址添加一个路由。 你需要在任何不符合 RFC1918 要求的分支或虚拟网络中手动进行添加。 

6. 将一个静态路由添加到 RT_RED 和 RT_BLUE，将所有流量定向到 Azure 防火墙。 此步骤可确保 VNet 不能直接访问分支。 此步骤不能通过防火墙管理器执行，因为这些虚拟网络不与默认路由表相关联。
    * 示例：目标前缀：0.0.0.0/0 下一跃点：Azure 防火墙

    > [!NOTE]
    > 路由是使用最长前缀匹配 (LPM) 执行的。 因此，0.0.0.0/0 静态路由不会优先于 BLUE_RT 和 RED_RT 中存在的确切前缀。 因此，Azure 防火墙不会检查 VNet 内部流量。

这会导致路由配置更改，如下图所示。

图 1
[ ![图 1](./media/routing-scenarios/custom-branch-vnet/custom-branch.png) ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
