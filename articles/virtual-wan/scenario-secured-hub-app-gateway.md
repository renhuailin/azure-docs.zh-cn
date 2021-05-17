---
title: 确保应用程序网关和后端池之间的流量安全
titleSuffix: Azure Virtual WAN
description: 了解对通过应用程序网关的流量提供保护的虚拟 WAN 路由方案。 应用程序网关部署在分支 VNet 中，后者连接到受保护的虚拟 WAN 中心。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb3343bc9c3dffa8f84280cd0799c7e92f4f50c
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108165796"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>场景：确保应用程序网关和后端池之间的流量安全

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在此情况中，用户的流量通过部署在分支 VNet 中的应用程序网关进入 Azure，该分支 VNet 连接到安全虚拟 WAN 中心（具有 Azure 防火墙的虚拟 WAN 中心）。 目标是使用安全虚拟中心中的 Azure 防火墙检查应用程序网关和后端池之间的流量。

此情况中有两种特定的设计模式，具体取决于应用程序网关和后端池是在同一 VNet 中还是在不同的 VNet 中。

* **场景 1：** 应用程序网关和后端池在与虚拟 WAN 中心对等的同一虚拟网络中（单独的子网）。
* **场景 2：** 应用程序网关和后端池在与虚拟 WAN 中心对等的不同虚拟网络中。

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>场景 1 - 同一 VNet

在此情况下，应用程序网关和后端池在与虚拟 WAN 中心对等的同一虚拟网络中（单独的子网）。

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="场景 1 的关系图。" lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>工作流

目前，从虚拟 WAN 路由表播发到分支虚拟网络的路由将应用到整个虚拟网络，而不是分支 VNet 的子网。 因此，在此场景中需要使用用户定义的路由。 有关用户定义的路由 (UDR) 的信息，请参阅[虚拟网络自定义路由](../virtual-network/virtual-networks-udr-overview.md#user-defined)。


1. 在 Azure 防火墙管理器中包含应用程序网关和后端池的分支虚拟网络上，选择“启用安全 Internet 流量”和“启用安全专用流量” 。
1. 在应用程序网关子网上配置用户定义的路由 (UDR)。

   * 若要确保应用程序网关能够将流量直接发送到 Internet，请指定以下 UDR：

     * **地址前缀：** 0.0.0.0.0/0
     * **下一个跃点：** Internet

   * 若要确保应用程序网关能够通过虚拟 WAN 中心中的 Azure 防火墙将流量发送到后端池，请指定以下 UDR：

      * **地址前缀：** 后端池子网 (10.2.0.0/24)
      * **下一个跃点：** Azure 防火墙专用 IP

1. 在后端池子网上配置用户定义的路由 (UDR)。

   * **地址前缀：** 应用程序网关子网
   * **下一个跃点：** Azure 防火墙专用 IP

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>场景 2 - 不同 VNet

在此情况下，应用程序网关和后端池在与虚拟 WAN 中心对等的不同虚拟网络中。

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="场景 2 的关系图。" lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>工作流

目前，从虚拟 WAN 路由表播发到分支虚拟网络的路由将应用到整个虚拟网络，而不是分支 VNet 的子网。 因此，在此场景中需要使用用户定义的路由。 有关用户定义的路由 (UDR) 的信息，请参阅[虚拟网络自定义路由](../virtual-network/virtual-networks-udr-overview.md#user-defined)。

1. 在“Azure 防火墙管理器”中，选择两个分支虚拟网络上的“启用安全 Internet 流量”和“启用安全专用流量”  。

1. 在应用程序网关子网上配置用户定义的路由 (UDR)。 若要确保应用程序网关能够将流量直接发送到 Internet，请指定以下 UDR：

   * **地址前缀：** 0.0.0.0.0/0
   * **下一个跃点：** Internet

## <a name="next-steps"></a>后续步骤

* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
* 有关用户定义的路由的详细信息，请参阅[虚拟网络自定义路由](../virtual-network/virtual-networks-udr-overview.md#user-defined)。
* 有关虚拟 WAN 安全虚拟中心的信息，请参阅[安全虚拟中心](../firewall-manager/secured-virtual-hub.md)。
