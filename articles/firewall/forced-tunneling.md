---
title: Azure 防火墙强制隧道
description: 可以对强制隧道进行配置，将发往 Internet 的流量路由到其他防火墙或网络虚拟设备进行进一步处理。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/13/2021
ms.author: victorh
ms.openlocfilehash: b3d52451713c8fc504487aa293d566264f4eadb6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183376"
---
# <a name="azure-firewall-forced-tunneling"></a>Azure 防火墙强制隧道

当配置新的 Azure 防火墙时，将所有 Internet 绑定的流量路由到指定的下一个跃点，而不是直接转到 Internet。 例如，你可以通过 BGP 或用户定义路由 (UDR) 来播发默认路由，以便将网络流量强制发送到本地边缘防火墙或其他网络虚拟设备 (NVA)，从而在该流量传递到 Internet 之前对其进行处理。 若要支持此配置，必须创建已启用强制隧道配置的 Azure 防火墙。 这是避免服务中断的强制性要求。 如果这是预先存在的防火墙，则必须在“强制隧道”模式下重新创建防火墙以支持此配置。 有关详细信息，请参阅 [Azure 防火墙常见问题解答](firewall-faq.yml#how-can-i-stop-and-start-azure-firewall)，了解如何在“强制隧道”模式下停止和重启防火墙。

## <a name="forced-tunneling-configuration"></a>强制隧道配置

可以通过启用“强制隧道”模式在防火墙创建期间配置强制隧道，如下所示。 为了支持强制隧道，服务管理流量将与客户流量分开。 还需要一个名为“AzureFirewallManagementSubnet”的专用子网（最小子网大小为“/26”），此子网有其自己的关联公共 IP 地址。 

在“强制隧道”模式下，Azure 防火墙服务会引入管理子网 (AzureFirewallManagementSubnet) 进行操作。 默认情况下，该服务会将系统提供的路由表关联到管理子网。 此子网上唯一允许的路由是到 Internet 的默认路由，因此必须禁用“传播网关”路由。 避免在创建防火墙时将客户路由表关联到管理子网。 

:::image type="content" source="media/forced-tunneling/forced-tunneling-configuration.png" alt-text="配置强制隧道":::

在此配置中，AzureFirewallSubnet 现在可以包括到任何本地防火墙或 NVA 的路由，以便在将流量传递到 Internet 之前对其进行处理。 如果在此子网上启用了“传播网关路由”，还可以通过 BGP 将这些路由发布到 AzureFirewallSubnet。

例如，你可以在 AzureFirewallSubnet 上创建一个默认路由并使用你的 VPN 网关作为下一跃点，以到达你的本地设备。 还可以启用“传播网关路由”以获得通向本地网络的合适路由。

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="虚拟网络网关路由传播":::

如果启用强制隧道，则会将 Internet 绑定的流量由 SNAT 转换为 AzureFirewallSubnet 中的某个防火墙专用 IP 地址，从而向本地防火墙隐藏源。

如果组织对专用网络使用公共 IP 地址范围，Azure 防火墙会通过 SNAT 将流量发送到 AzureFirewallSubnet 中的某个防火墙专用 IP 地址。 但是，可以将 Azure 防火墙配置为不 SNAT 公共 IP 地址范围。 有关详细信息，请参阅 [Azure 防火墙 SNAT 专用 IP 地址范围](snat-private-range.md)。

将 Azure 防火墙配置为支持强制隧道后，便无法撤消配置。 如果删除防火墙上的所有其他 IP 配置，管理 IP 配置也会被删除，防火墙会被解除分配。 无法删除分配给管理 IP 配置的公共 IP 地址，但可以分配不同的公共 IP 地址。

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙](tutorial-hybrid-portal.md)
