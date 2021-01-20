---
title: 使用托管解决方案在 Azure 和 AWS 之间创建 VPN
description: 如何使用托管解决方案而不是 Vm 或设备在 Azure 与 AWS 之间创建 VPN 连接。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/15/2021
ms.author: cherylmc
ms.openlocfilehash: c1bc263ca67a7d05dbb0d40bb07ba1ae43c2db5c
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98605408"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>使用托管解决方案在 Azure 和 AWS 之间创建 VPN 连接

可以使用托管解决方案在 Azure 和 AWS 之间建立连接。 以前，你需要使用充当响应方的设备或 VM。 现在，你可以直接将 AWS 虚拟专用网关连接到 Azure VPN 网关，无需担心如何管理 IaaS 资源（例如虚拟机）。 本文将帮助你使用托管解决方案在 Azure 和 AWS 之间创建 VPN 连接。

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="体系结构关系图":::

## <a name="configure-azure"></a>配置 Azure

### <a name="configure-a-virtual-network"></a>配置虚拟网络

配置虚拟网络 有关说明，请参阅 [虚拟网络快速入门](../virtual-network/quick-create-portal.md)。

本文使用以下示例值：

* **资源组：** rg-azure-aws
* **区域：** 美国东部
* **虚拟网络名称：** vnet-azure
* **IPv4 地址空间：** 172.10.0.0/16
* **子网名称：** 子网-01
* **子网地址范围：** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>创建 VPN 网关

为虚拟网络创建 VPN 网关。 有关说明，请参阅 [教程：创建和管理 VPN 网关](tutorial-create-gateway-portal.md)。

本文使用以下示例值和设置：

* **网关类型：** VPN
* **VPN 类型：** 基于路由
* **SKU：** VpnGw1
* **生成：** 第1代
* **虚拟网络：** 必须是要为其创建网关的 VNet。
* **网关子网地址范围：** 172.10.0.0/27
* **公共 IP 地址**：新建
* **公共 IP 地址名称：** pip-aws
* **启用主动-主动模式：** 禁用
* **配置 BGP：** 禁用

示例：

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="虚拟网络网关摘要":::

## <a name="configure-aws"></a>配置 AWS

1. 创建虚拟私有云 (VPC) 。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="创建 VPC 信息":::

1. 在 VPC (虚拟网络) 中创建子网。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="创建子网":::

1. 创建一个指向 Azure VPN 网关公共 IP 地址的客户网关。 **客户网关** 是一种 AWS 资源，其中包含有关客户网关设备的 AWS 的信息，在本例中，为 Azure VPN 网关。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="创建客户网关":::

1. 创建虚拟专用网关。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="创建虚拟专用网关":::

1. 将虚拟专用网关附加到 VPC。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="将 VPG 附加到 VPC":::

1. 选择 VPC。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="附加":::

1. 创建站点到站点 VPN 连接。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="创建 VPN 连接":::

1. 将路由选项设置为 **Static** ，并指向 Azure 子网-01 前缀 **(172.10.1.0/24) 。**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="设置静态路由":::

1. 填充选项后， **创建** 连接。

1. 下载配置文件。 若要下载正确的配置，请将供应商、平台和软件更改为 **通用**，因为 Azure 不是有效的选项。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="下载配置":::

1. 该配置文件包含由 AWS 创建的两个 IPsec 隧道中的每个隧道的预共享密钥和公共 IP 地址。

   **隧道1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="隧道1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="隧道1配置":::

   **隧道2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="隧道2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="隧道2配置":::

1. 创建隧道后，你将看到类似于此示例的内容。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="AWS VPN 连接详细信息":::

## <a name="create-local-network-gateway"></a>创建本地网络网关

在 Azure 中，本地网络网关是通常表示本地位置的 Azure 资源。 它使用用于连接到本地 VPN 设备的信息进行填充。 但是，在此配置中，本地网络网关已创建并填充了 AWS 虚拟专用网关连接信息。 有关 Azure 本地网络网关的详细信息，请参阅 [AZURE VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md#lng)。

在 Azure 中创建本地网络网关。 有关步骤，请参阅 [创建本地网络网关](tutorial-site-to-site-portal.md#LocalNetworkGateway)。

指定以下值：

* **名称：** 在此示例中，我们使用 lng-aws。
* **终结点：** IP 地址
* **IP 地址：** AWS 虚拟专用网关的公共 IP 地址和 VPC CIDR 前缀。 你可以在先前下载的配置文件中找到公共 IP 地址。

为了实现高可用性，AWS 创建了两个 IPsec 隧道。 以下示例显示 IPsec 隧道 #1 的公共 IP 地址。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="本地网络网关":::

## <a name="create-vpn-connection"></a>创建 VPN 连接

在本部分中，将在 Azure 虚拟网络网关和 AWS 网关之间创建 VPN 连接。

1. 创建 Azure 连接。 有关创建连接的步骤，请参阅 [创建 VPN 连接](tutorial-site-to-site-portal.md#CreateConnection)。

   在下面的示例中，已从之前下载的配置文件中获取共享密钥。 在此示例中，我们使用 AWS 创建的 IPsec 隧道的值 #1，并在配置文件中描述。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Azure 连接对象":::

1. 查看连接。 几分钟后，将建立连接。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="工作连接":::

1. 验证 AWS IPsec 隧道 #1 是否已 **启动**。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="验证 AWS 隧道是否已启动":::

1. 编辑与 VPC 关联的路由表。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="编辑路由":::

1. 将路由添加到 Azure 子网。 此路由将通过 VPC 关。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="保存路由配置":::

## <a name="configure-second-connection"></a>配置第二个连接

在本部分中，将创建另一个连接以确保高可用性。

1. 创建另一个本地网络网关，该网关指向 AWS 上 #2 IPsec 隧道的公共 IP 地址。 这是备用网关。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="创建本地网络网关":::

1. 创建从 Azure 到 AWS 的第二个 VPN 连接。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="创建备用本地网络网关连接":::

1. 查看 Azure VPN 网关连接。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Azure 连接状态":::

1. 查看 AWS 连接。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="AWS 连接状态":::

现在已建立连接。

## <a name="test-connections"></a>测试连接

1. 向 AWS 上的 VPC 添加 Internet 网关。 Internet 网关是 Amazon VPN 与 Internet 之间的逻辑连接。 此资源可让你通过 Internet 从 AWS 公共 IP 进行连接。 VPN 连接不需要此资源。 我们仅用于测试。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="创建 Internet 网关":::

1. 选择 " **附加到 VPC**"。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="将 Internet 网关附加到 VPC":::

1. 选择 VPC 并 **连接 Internet 网关**。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="连接网关":::

1. 创建路由，以允许连接到 **0.0.0.0/0** (internet) 通过 Internet 网关。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="通过网关配置路由":::

1. 在 Azure 中，将自动创建路由。 可以通过选择 " **VM > 网络" > 网络接口 "> 有效路由**，从 Azure VM 检查路由。 你将看到2个路由，每个连接1个路由。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="检查有效路由":::

1. 从 Azure 上的 Linux VM，环境类似于以下示例。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Linux VM 的 Azure 概述":::

1. 从 AWS 上的 Linux VM，环境类似于以下示例。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Linux VM 的 AWS 概述":::

1. 测试从 Azure VM 的连接。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="来自 Azure 的 Ping 测试":::

1. 测试来自 AWS VM 的连接。

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="来自 AWS 的 Ping 测试":::

## <a name="next-steps"></a>后续步骤

有关 IKEv2 的 AWS 支持的详细信息，请参阅 [AWS 一文](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/)。
