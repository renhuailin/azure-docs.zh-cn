---
title: VMware Solution by CloudSimple - Azure 网络连接
description: 了解如何将 Azure 虚拟网络连接到 CloudSimple 区域网络
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "77025072"
---
# <a name="azure-network-connections-overview"></a>Azure 网络连接概述

在区域中创建 CloudSimple 服务并创建节点时，可以执行以下操作：

* 请求 Azure ExpressRoute 线路，并将其附加到该区域中的 CloudSimple 网络。
* 使用 Azure ExpressRoute 将 CloudSimple 区域网络连接到 Azure 虚拟网络或本地网络。
* 可以从私有云环境访问 Azure 订阅或本地网络中运行的服务。

ExpressRoute 连接是一种带宽高且延迟低的连接。

## <a name="benefits"></a>优点

利用 Azure 网络连接，可以：

* 将 Azure 用作私有云上虚拟机的备份目标。
* 在 Azure 订阅中部署 KMS 服务器，以加密私有云 vSAN 数据存储。
* 使用混合应用程序，即，应用程序的 Web 层在公有云中运行的同时应用程序和数据库层在私有云中运行。

## <a name="azure-virtual-network-connection"></a>Azure 虚拟网络连接

可以使用 ExpressRoute 将私有云连接到 Azure 资源。  ExpressRoute 连接允许从私有云访问 Azure 订阅中运行的资源。  此连接允许将私有云网络扩展到 Azure 虚拟网络。  来自 CloudSimple 网络的路由将通过 BGP 与 Azure 虚拟网络交换。  如果已配置虚拟网络对等互连，则所有对等互连的虚拟网络都可从 CloudSimple 网络进行访问。

![Azure ExpressRoute 与虚拟网络的连接](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute 与本地网络的连接

可以将现有 Azure ExpressRoute 线路连接到 CloudSimple 区域。 ExpressRoute Global Reach 功能用于将两个线路彼此连接。  在本地和 CloudSimple ExpressRoute 线路之间建立连接。  此连接允许将本地网络扩展到私有云网络。 来自 CloudSimple 网络的路由将通过 BGP 与本地网络交换。

![本地 ExpressRoute 连接 - Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>连接到本地网络和 Azure 虚拟网络

与本地网络和 Azure 虚拟网络的连接可在 CloudSimple 网络中共存。  该连接使用 BGP 在本地网络、Azure 虚拟网络和 CloudSimple 公网络之间交换路由。  如果存在 Global Reach 连接，则将 CloudSimple 网络连接到 Azure 虚拟网络时，Azure 虚拟网络路由将在本地网络中可见。  路由交换发生在 Azure 中的边缘路由器之间。

![与 Azure 虚拟网络连接的本地 ExpressRoute 连接](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>重要注意事项

从本地网络和 Azure 虚拟网络连接到 CloudSimple 网络允许在所有网络之间进行路由交换。

* Azure 虚拟网络将在本地网络和 CloudSimple 网络中可见。
* 如果已从本地网络连接到 Azure 虚拟网络，则使用 Global Reach 连接到 CloudSimple 网络将允许从 CloudSimple 网络访问虚拟网络。
* 子网地址不得在任何连接的网络之间重叠。
* CloudSimple 不会将默认路由播发到 ExpressRoute 连接
* 如果本地路由器播发默认路由，则来自 CloudSimple 网络和 Azure 虚拟网络的流量将使用播发的默认路由。  因此，无法使用公共 IP 地址访问 Azure 上的虚拟机。

## <a name="next-steps"></a>后续步骤

* [使用 ExpressRoute 将 Azure 虚拟网络连接到 CloudSimple](virtual-network-connection.md)
* [使用 ExpressRoute 从本地连接到 CloudSimple](on-premises-connection.md)
