---
title: Azure Bastion | Microsoft Docs
description: 了解 Azure Bastion，它提供与虚拟机之间的安全无缝的 RDP/SSH 连接，而无需在外部公开 RDP/SSH 端口。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 07/12/2021
ms.author: cherylmc
ms.custom: contperf-fy2q1-portal
ms.openlocfilehash: fe1cc48b3106d4874e151ed8be114e6a98e51720
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434173"
---
# <a name="what-is-azure-bastion"></a>什么是 Azure Bastion？

Azure Bastion 是你部署的一项服务，借此可使用浏览器和 Azure 门户连接到虚拟机。 Azure Bastion 服务是的一种完全平台管理的 PaaS 服务，可在虚拟网络中进行预配。 可通过 TLS 直接从 Azure 门户实现与虚拟机之间的安全无缝的 RDP/SSH 连接。 通过 Azure Bastion 连接时，你的虚拟机无需公共 IP、代理或特殊的客户端软件。

Bastion 为预配它的虚拟网络中的所有 VM 提供安全的 RDP 和 SSH 连接。 使用 Azure Bastion 可防止虚拟机向外部公开 RDP/SSH 端口，同时仍然使用 RDP/SSH 提供安全访问。

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="显示 Azure Bastion 体系结构的示意图。":::

## <a name="key-benefits"></a><a name="key"></a>主要优点

* 在 Azure 门户中直接使用 RDP 和 SSH 连接：可以通过单击无缝体验直接在 Azure 门户中进行 RDP 和 SSH 会话。
* 穿越防火墙，通过 TLS 进行 RDP/SSH 远程会话：Azure Bastion 使用基于 HTML5 的 Web 客户端，该客户端自动流式传输到本地设备。 在端口 443 上通过 TLS 进行 RDP/SSH 会话，从而安全穿越公司防火墙。
* Azure VM 无需公共 IP  ：Azure Bastion 使用 VM 上的专用 IP 打开与 Azure 虚拟机的 RDP/SSH 连接。 虚拟机无需公共 IP。
* **轻松管理网络安全组 NSG**：Azure Bastion 是 Azure 提供的完全托管平台 PaaS 服务，为提供安全 RDP/SSH 连接进行了内部强化。 无需向 Azure Bastion 子网应用任何 NSG。 由于 Azure Bastion 通过专用 IP 连接到虚拟机，所以可将 NSG 配置为仅允许来自 Azure Bastion 的 RDP/SSH。 这样消除了每次需要安全地连接到虚拟机时管理 NSG 的麻烦。 有关 NSG 的详细信息，请参阅[网络安全组](../virtual-network/network-security-groups-overview.md#security-rules)。
* 端口扫描防护：因为无需将虚拟机公开到公共 Internet，因此可防止 VM 受到虚拟网络外部的恶意用户的端口扫描。
* **防止零日漏洞。仅在一个位置强化：** Azure Bastion 是完全托管平台 PaaS 服务。 由于它位于虚拟网络外围，因此你无需担心如何强化虚拟网络中的每个虚拟机。 Azure 平台通过使 Azure Bastion 保持强化且始终保持最新来防范零天攻击。

## <a name="skus"></a><a name="sku"></a>SKU

Azure Bastion 有两个可用的 SKU：基本和标准。 标准 SKU 目前为预览状态。 有关详细信息（包括如何升级 SKU），请参阅[配置设置](configuration-settings.md#skus)一文。 

下表显示了各种功能及对应的 SKU。

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

## <a name="architecture"></a><a name="architecture"></a>体系结构

Azure Bastion 部署到虚拟网络并支持虚拟网络对等互连。 具体而言，Azure Bastion 管理 RDP/SSH 与在本地或对等虚拟网络中创建的 VM 之间的连接。

RDP 和 SSH 是连接 Azure 中运行的工作负载的基本方法。 不要通过 Internet 公开 RDP/SSH 端口，这被视为一个严重的威胁面。 这通常是由于协议漏洞造成的。 若要包含此威胁面，可以在外围网络的公共端部署 bastion 主机（也称为跳转服务器）。 Bastion 主机服务器在设计和配置上考虑了抵御攻击。 Bastion 服务器还为位于 bastion 后以及网络内的工作负载提供 RDP 和 SSH 连接。

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="显示 Azure Bastion 体系结构的示意图。":::

此图显示了 Azure Bastion 部署体系结构。 在此图中：

* 堡垒主机部署在虚拟网络中，该网络包含具有最小 /27 前缀的 AzureBastionSubnet 子网。
* 用户使用任何 HTML5 浏览器连接到 Azure 门户。
* 用户选择要连接到的虚拟机。
* 单击一下，在浏览器中打开 RDP/SSH 会话。
* Azure VM 无需公共 IP。

## <a name="host-scaling"></a><a name="host-scaling"></a>主机缩放

Azure Bastion 支持手动主机缩放。 你可以配置主机实例（缩放单元）数，以便管理 Azure Bastion 可支持的并发 RDP/SSH 连接数。 增加主机实例数后，Azure Bastion 可以管理更多并发会话。 减少实例数会减少可支持的并发会话数。 Azure Bastion 最多支持 50 个主机实例。 此功能仅适用于 Azure Bastion 标准 SKU。

有关详细信息，请参阅[配置设置](configuration-settings.md#instance)一文。

## <a name="pricing"></a><a name="pricing"></a>定价

Azure Bastion 定价包括基于 SKU、缩放单元和数据传输速率的小时定价组合。 可在 [定价](https://azure.microsoft.com/pricing/details/azure-bastion) 页上找到定价信息。

## <a name="whats-new"></a><a name="new"></a>新增功能

订阅 RSS 源，并在 [Azure 更新](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Bastion)页面上查看最新的 Azure Bastion 功能更新。

## <a name="bastion-faq"></a>Bastion 常见问题解答

关于常见问题解答，请参阅 Bastion [常见问题解答](bastion-faq.md)。

## <a name="next-steps"></a>后续步骤

* [教程：创建 Azure Bastion 主机并连接到 Windows VM](tutorial-create-host-portal.md)。
* [Learn 模块：Azure Bastion 简介](/learn/modules/intro-to-azure-bastion/)。
* 了解 Azure 的一些其他关键[网络功能](../networking/fundamentals/networking-overview.md)。
