---
title: VNet 对等互连和 Azure Bastion 体系结构
description: 了解如何将 VNet 对等互连和 Azure Bastion 配合使用来连接到 VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: ccbee86e8f5ae2ab514c8ec7ece4d41e1e890e4e
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733279"
---
# <a name="vnet-peering-and-azure-bastion"></a>VNet 对等互连和 Azure Bastion

Azure Bastion 和 VNet 对等互连可以一起使用。 配置 VNet 对等互连后，无需在每个对等互连的 VNet 中部署 Azure Bastion。 这就意味着，如果在一个虚拟网络 (VNet) 中配置了 Azure Bastion 主机，则可使用该主机连接到在对等互连的 VNet 中部署的 VM，而无需部署其他 Bastion 主机。 有关 VNet 对等互连的详细信息，请参阅[关于虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)。

Azure Bastion 使用以下类型的对等互连：

* **虚拟网络对等互连：** 连接同一 Azure 区域中的虚拟网络。
* **全局虚拟网络对等互连：** 跨 Azure 区域连接虚拟网络。

## <a name="architecture"></a>体系结构

配置 VNet 对等互连后，可以在中心辐射型拓扑或全网格型拓扑中部署 Azure Bastion。 Azure Bastion 部署是按虚拟网络进行的，而不是按订阅/帐户或虚拟机进行的。

在虚拟网络中预配 Azure Bastion 服务后，同一 VNet（以及对等互连的 VNet）中所有的 VM 均可获得 RDP/SSH 体验。 这就意味着，可以将 Bastion 部署合并到一个 VNet，而仍然可以访问对等互连的 VNet 中部署的 VM，集中进行总体部署。

:::image type="content" source="./media/vnet-peering/design.png" alt-text="设计和体系结构图":::

此图显示了中心辐射型模型中 Azure Bastion 部署的体系结构。 在此图中，可以看到以下配置：

* Bastion 主机部署在集中式中心虚拟网络中。
* 已部署集中式网络安全组 (NSG)。
* Azure VM 无需公共 IP。

**步骤：**

1. 使用任何 HTML5 浏览器连接到 Azure 门户。
2. 确保对目标 VM 和对等 VNet 都有“读取”访问权限。 此外，请在 IAM下 检查你是否对以下资源具有“读取”访问权限：
   * 虚拟机上的读者角色。
   * NIC 上的读者角色（使用虚拟机的专用 IP）。
   * Azure Bastion 资源上的读者角色。
   * 虚拟网络上的读者角色（如果没有对等互连虚拟网络，则不需要）。
3. 若要在“连接”下拉菜单中查看 Bastion，必须在“订阅”>“全局订阅”中选择有权访问的订阅。
4. 选择要连接到的虚拟机。
5. 在对等互连的 VNet 中会顺利检测到 Azure Bastion。
6. 单击一下，在浏览器中打开 RDP/SSH 会话。

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="“连接”":::

   若要详细了解如何通过 Azure Bastion 连接到 VM，请参阅：

   * [连接到 VM - RDP](bastion-connect-vm-rdp.md)。
   * [连接到 VM - SSH](bastion-connect-vm-ssh.md)。

## <a name="faq"></a>常见问题解答

关于常见问题解答，请参阅 Bastion VNet 对等互连[常见问题解答](bastion-faq.md#peering)。

## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)。
