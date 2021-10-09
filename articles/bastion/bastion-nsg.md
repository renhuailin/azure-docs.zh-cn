---
title: 在 Azure Bastion 中使用 VM 和 NSG
description: 了解如何在 Azure Bastion 中使用网络安全组。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: cherylmc
ms.openlocfilehash: adccd5873030adcc5c286ed8d23326796c27ab9c
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080971"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>使用 NSG 访问和 Azure Bastion

使用 Azure Bastion 时，可以使用网络安全组 (NSG)。 有关详细信息，请参阅[安全组](../virtual-network/network-security-groups-overview.md)。

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

在此图中：

* Bastion 主机部署到虚拟网络中。
* 用户使用任何 HTML5 浏览器连接到 Azure 门户。
* 用户导航到虚拟机，以便进行 RDP/SSH 操作。
* 连接集成 - 在浏览器中单击“RDP/SSH 会话”
* Azure VM 无需公共 IP。

## <a name="network-security-groups"></a><a name="nsg"></a>网络安全组

本部分介绍用户与 Azure Bastion 之间的网络流量，以及到虚拟网络中的目标 VM 的网络流量：

> [!IMPORTANT]
> 如果选择在 Azure Bastion 资源中使用 NSG，则必须创建以下所有入口和出口流量规则。 在 NSG 中省略以下任何规则都会阻止 Azure Bastion 资源在将来接收必要的更新，从而使资源易受将来的安全漏洞的影响。
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure Bastion 将专门部署到 ***AzureBastionSubnet***。

* **入口流量：**

   * **来自公共 Internet 的入口流量：** Azure Bastion 将创建一个公共 IP，需要在该公共 IP 上启用端口 443，用于入口流量。 不需要在 AzureBastionSubnet 上打开端口 3389/22。
   * **来自 Azure Bastion 控制平面的入口流量：** 对于控制平面连接，请启用从 GatewayManager 服务标记进行的端口 443 入站。 这使控制平面（即网关管理器）能够与 Azure Bastion 通信。
   * **来自 Azure Bastion 数据平面的入口流量：** 对于 Azure Bastion 基础组件之间的数据平面通信，启用端口 8080、5701 从 VirtualNetwork 服务标记到 VirtualNetwork 服务标记入站 。 这使得 Azure Bastion 的组件能够彼此通信。
   * **来自 Azure 负载均衡器的入口流量：** 对于运行状况探测，请从 AzureLoadBalancer 服务标记启用端口 443 入站。 这使得 Azure 负载均衡器能够检测连接性


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="屏幕截图显示 Azure Bastion 连接的入站安全规则。" lightbox="./media/bastion-nsg/inbound.png":::

* **出口流量：**

   * **流向目标 VM 的出口流量：** Azure Bastion 将通过专用 IP 到达目标 VM。 NSG 需要允许端口 3389 和 22 的出口流量流向其他目标 VM 子网。 如果你使用自定义端口功能作为标准 SKU 的一部分，则 NSG 将改为需要允许你在目标 VM 上打开的自定义端口值的出口流量流向其他目标 VM 子网。
   * **流向 Azure Bastion 数据平面的出口流量：** 对于 Azure Bastion 基础组件之间的数据平面通信，启用端口 8080、5701 从 VirtualNetwork 服务标记到 VirtualNetwork 服务标记出站 。 这使得 Azure Bastion 的组件能够彼此通信。
   * **流向 Azure 中其他公共终结点的出口流量：** Azure Bastion 需要能够连接到 Azure 中的各种公共终结点，以便执行相应操作（例如，存储诊断日志和计量日志）。 因此，Azure Bastion 需要出站到 443，再到 AzureCloud 服务标记。
   * **流向目标 Internet 的出口流量：** 若要进行会话和证书验证，Azure Bastion 需要能与 Internet 通信。 为此，建议你启用到 Internet 的出站端口 80。


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="屏幕截图显示 Azure Bastion 连接的出站安全规则。" lightbox="./media/bastion-nsg/outbound.png":::

### <a name="target-vm-subnet"></a>目标 VM 子网
此子网包含要通过 RDP/SSH 连接到的目标虚拟机。

   * **来自 Azure Bastion 的入口流量：** Azure Bastion 将通过专用 IP 到达目标 VM。 RDP/SSH 端口（分别为端口 3389/22 或自定义端口值（如果你使用自定义端口功能作为标准 SKU 的一部分））需要通过专用 IP 在目标 VM 端打开。 最佳做法是，可以在此规则中添加 Azure Bastion 子网 IP 地址范围，仅允许 Bastion 在目标 VM 子网中的目标 VM 上打开这些端口。


## <a name="next-steps"></a>后续步骤

有关 Azure Bastion 的详细信息，请参阅[常见问题解答](bastion-faq.md)。
