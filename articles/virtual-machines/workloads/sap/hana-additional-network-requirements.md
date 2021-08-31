---
title: Azure 上的 SAP HANA（大型实例）的其他网络要求 | Microsoft Docs
description: 了解可能具有的 Azure SAP HANA（大型实例）的附加网络要求。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/3/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a91f2116eb7eb2e6d7908e5c8c4e5171fff8c294
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114361065"
---
# <a name="other-network-requirements-for-large-instances"></a>大型实例的其他网络要求

本文介绍在 Azure 上部署 SAP HANA 大型实例时可能具有的其他网络要求。

## <a name="prerequisites"></a>先决条件

本文假定你已完成下述步骤：
- [将 Azure VM 连接到 HANA 大型实例](hana-connect-azure-vm-large-instances.md)
- [将虚拟网络连接到 HANA 大型实例](hana-connect-vnet-express-route.md)

## <a name="add-more-ip-addresses-or-subnets"></a>添加更多 IP 地址或子网

你可能会发现需要添加更多 IP 地址或子网。 可以使用 Azure 门户、PowerShell 或 Azure CLI 添加多个 IP 地址或子网。

将新的 IP 地址范围作为新范围添加到虚拟网络地址空间。 不要生成新的聚合范围。 向 Microsoft 提交此更改。 这样就可以从新的 IP 地址范围连接到客户端中的 HANA 大型实例。 可以发起 Azure 支持请求来添加新的虚拟网络地址空间。 收到确认消息后，执行[将 Azure VM 连接到 HANA 大型实例](hana-connect-azure-vm-large-instances.md)中讨论的步骤。 

要从 Azure 门户创建其他子网，请参阅[使用 Azure 门户创建虚拟网络](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 要通过 PowerShell 创建子网，请参阅[使用 PowerShell 创建虚拟网络](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)。

## <a name="add-virtual-networks"></a>添加虚拟网络

首次连接一个或多个 Azure 虚拟网络后，可能需要连接更多用于访问 Azure SAP HANA（大型实例）的虚拟网络。 首先，提交 Azure 支持请求。 请在请求中包含指明特定 Azure 部署的特定信息。 此外请包含 IP 地址空间范围或 Azure 虚拟网络地址空间的范围。 然后，Microsoft SAP HANA 服务管理团队将提供所需的信息来连接添加的虚拟网络和 Azure ExpressRoute。 对于每个虚拟网络，都需要使用唯一授权密钥才能连接到 HANA 大型实例 ExpressRoute 线路。

## <a name="increase-expressroute-circuit-bandwidth"></a>提高 ExpressRoute 线路带宽

咨询 Microsoft 上的 SAP HANA 服务管理部门。 如果他们建议提高 Azure SAP HANA（大型实例）ExpressRoute 线路的带宽，请创建 Azure 支持请求。 （可以请求将单条线路的带宽提高到最大 10 Gbps）。完成该操作后，会收到通知；无需执行任何操作即可在 Azure 中提高速度。

## <a name="add-another-expressroute-circuit"></a>添加另一条 ExpressRoute 线路

咨询 Microsoft 上的 SAP HANA 服务管理部门。 如果他们建议添加其他 ExpressRoute 线路，请创建 Azure 支持请求（包含获取连接到新线路的授权信息的请求）。 提出请求之前，必须定义虚拟网络上使用的地址空间。 之后，Microsoft 上的 SAP HANA 服务管理部门才能提供授权。

创建新线路并且 Microsoft SAP HANA 服务管理团队完成配置后，你会收到通知，其中包含继续下一步所需的信息。 如果 Azure 虚拟网络已连接到同一 Azure 区域中的另一条 Azure SAP HANA（大型实例）ExpressRoute 线路，则无法连接到此附加线路。

## <a name="delete-a-subnet"></a>删除子网

可以使用 Azure 门户、PowerShell 或 Azure CLI 来删除虚拟网络子网。 如果 Azure 虚拟网络 IP 地址范围或地址空间是一个聚合范围，则不需要与 Microsoft 协调。 （虚拟网络仍会传播包含已删除子网的 BGP 路由地址空间。） 

可能已经将 Azure 虚拟网络地址范围或地址空间定义为多个 IP 地址范围。 其中一个范围可能已分配给已删除的子网。 请确保从虚拟网络地址空间中删除该子网。 然后通知 Microsoft SAP HANA 服务管理团队，让其将该线路从 Azure SAP HANA（大型实例）能够与之通信的范围中删除。

有关详细信息，请参阅[删除子网](../../../virtual-network/virtual-network-manage-subnet.md#delete-a-subnet)。

## <a name="delete-a-virtual-network"></a>删除虚拟网络

有关信息，请参阅[删除虚拟网络](../../../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

Microsoft SAP HANA 服务管理团队会删除对 Azure SAP HANA（大型实例）ExpressRoute 线路的现有授权。 它还会删除与 HANA 大型实例进行通信的 Azure 虚拟网络 IP 地址范围或地址空间。

删除虚拟网络后，请提出 Azure 支持请求并提供要删除的 IP 地址空间范围。

请确保删除所有内容。 删除：
- ExpressRoute 连接
- 虚拟网络网关
- 虚拟网络网关公共 IP
- 虚拟网络

## <a name="delete-an-expressroute-circuit"></a>删除 ExpressRoute 线路

若要删除某个额外的 Azure SAP HANA（大型实例）ExpressRoute 线路，请向 Microsoft SAP HANA 服务管理团队提出 Azure 支持请求。 请求他们删除该线路。 在 Azure 订阅中，可以根据需要删除或保留虚拟网络。 但是，必须删除 HANA 大型实例 ExpressRoute 线路与链接的虚拟网络网关之间的连接。

## <a name="next-steps"></a>后续步骤

了解如何安装和配置 Azure SAP HANA（大型实例）。

> [!div class="nextstepaction"]
> [安装和配置 SAP HANA（大型实例）](hana-installation.md)
