---
title: Azure VMware Solution by CloudSimple - 使用 ExpressRoute 将私有云连接到 Azure 网络
description: 介绍如何使用 ExpressRoute 将 CloudSimple 私有云环境连接到 Azure 虚拟网络
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dfd394edde01fbeb999a5b821808fd1620bf15f4
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618188"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>介绍如何使用 ExpressRoute 将 CloudSimple 私有云环境连接到 Azure 虚拟网络

可以使用 ExpressRoute 将 CloudSimple 私有云环境连接到 Azure 虚拟网络。  利用这种高带宽、低延迟的连接，可从私有云环境访问 Azure 订阅中运行的服务。

利用虚拟网络连接，可以：

* 使用 Azure 作为私有云上虚拟机的备份目标。
* 在 Azure 订阅中部署 KMS 服务器，以加密私有云 vSAN 数据存储。
* 使用混合应用程序，即，应用程序的 Web 层在公有云中运行，而同时应用程序和数据库层在私有云中运行。

![Azure ExpressRoute 到虚拟网络的连接](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>设置虚拟网络连接

若要设置到私有云的虚拟网络连接，需要授权密钥、对等线路 URI 以及对 Azure 订阅的访问权限。 此信息可在 CloudSimple 门户中的“虚拟网络连接”页上找到。 有关说明，请参阅[获取 Azure 虚拟网络到 CloudSimple 的连接的对等互连信息](virtual-network-connection.md)。 如果在获取信息时遇到任何问题，请提交<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支持请求</a>。

> [!TIP]
> 如果已有 Azure 虚拟网络、网关子网和虚拟网络网关，则可跳至步骤 4。

1. 在 Azure 订阅上创建虚拟网络，并验证所选地址空间是否与私有云的地址空间不同。  如果已有 Azure 虚拟网络，可以使用这个现有的虚拟网络。  有关详细信息，请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)。
2. 在 Azure 虚拟网络上创建网关子网。  如果 Azure 虚拟网络中已有网关子网，可以使用这个现有的子网。 有关详细信息，请参阅[创建网关子网](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)。
3. 在虚拟网络上创建虚拟网络网关。  如果有现有的虚拟网络网关，可以使用这个现有的网关。 有关详细信息，请参阅[创建虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)。
4. 按照[将虚拟网络连接到线路 - 不同的订阅](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)中所述，通过兑换授权密钥，创建虚拟网络与私有云之间的连接。

> [!WARNING]
> 如果使用现有的虚拟网络网关，并且它具有到 CloudSimple ExpressRoute 线路所在同一位置的 ExpressRoute 连接，将不会建立该连接。  创建新虚拟网络并按照前面的步骤操作。

## <a name="test-the-virtual-network-connection"></a>测试虚拟网络连接

在创建了连接后，可以通过选择“设置”下的“属性”来检查连接的状态 。  “状态”和“预配状态”应该显示为“成功”。

![连接状态](media/azure-expressroute-connection.png)

若要测试虚拟网络连接，请执行以下操作：

1. 在 Azure 订阅中创建虚拟机。
2. 查找私有云 vCenter 的 IP 地址（请参考欢迎电子邮件）。
3. 从在 Azure 虚拟网络中创建的虚拟机对云 vCenter 执行 Ping 操作。
4. 从在私有云 vCenter 中运行的虚拟机对 Azure 虚拟机执行 Ping 操作。

如果在建立连接时遇到任何问题，请提交<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支持请求</a>。
