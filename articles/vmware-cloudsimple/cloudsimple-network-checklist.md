---
title: Azure VMware Solution by CloudSimple - 网络检查列表
description: 有关在 Azure VMware Solution by CloudSimple 上分配网络 CIDR 的检查列表
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "77025004"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple 的网络先决条件

Azure VMware Solution by CloudSimple 提供一个 VMware 私有云环境，该环境可供用户和应用程序从本地环境、企业托管设备和 Azure 资源进行访问。 连接是通过 VPN 和 Azure ExpressRoute 连接等网络服务建立的。 其中某些网络服务要求指定网络地址范围才能启用这些服务。 

本文中的表格描述了一组地址范围以及使用指定地址的相应服务。 其中有些地址是必需的，而有些地址则取决于要部署的服务。 这些地址空间不应与任何本地子网、Azure 虚拟网络子网或规划的 CloudSimple 工作负载子网重叠。

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>创建私有云所需的网络地址范围

在创建 CloudSimple 服务和私有云的过程中，必须符合指定的网络无类别域际路由 (CIDR) 范围，如下所示。

| 名称/用于     | 说明                                                                                                                            | 地址范围            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| 网关 CIDR      | 边缘服务（VPN 网关）需要此 CIDR。  在创建 CloudSimple 服务过程中需要指定此 CIDR，它必须来自 RFC 1918 空间。 | /28                      |
| vSphere/vSAN CIDR | VMware 管理网络需要此 CIDR。 在创建私有云过程中必须指定此 CIDR。                                    | /24、/23、/22 或 /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>将 Azure 网络连接到本地网络时需要使用的网络地址范围

[通过 ExpressRoute 从本地网络连接到私有云网络](on-premises-connection.md)会建立 Global Reach 连接。  该连接使用边界网关协议 (BGP) 在本地网络、私有云网络与 Azure 网络之间交换路由。

| 名称/用于             | 说明                                                                                                                                                                             | 地址范围 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute 对等互连 CIDR | 使用 ExpressRoute Global Reach 建立本地连接时需要此 CIDR。 通过支持票证发出 Global Reach 连接请求时必须提供此 CIDR。 | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>使用站点到站点 VPN 连接到本地网络时需要使用的网络地址范围

[使用站点到站点 VPN 从本地网络连接到私有云网络](vpn-gateway.md)时需要以下 IP 地址、本地网络和标识符。 

| 地址/地址范围 | 说明                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 对等方 IP               | 本地 VPN 网关公共 IP 地址。 在本地数据中心与 CloudSimple 服务区域之间建立站点到站点 VPN 连接时需要此 IP 地址。 在创建站点到站点 VPN 网关期间需要此 IP 地址。                                         |
| 对等方标识符       | 本地 VPN 网关的对等方标识符。 此标识符通常与对等方 IP 相同。  如果在本地 VPN 网关上指定了唯一标识符，则必须指定该标识符。  在创建站点到站点 VPN 网关期间需要对等方 ID。   |
| 本地网络   | 访问区域中的 CloudSimple 网络时所需的本地前缀。  包括要访问 CloudSimple 网络的本地网络（包括用户要从中访问 CloudSimple 网络的客户端网络）中的所有前缀。                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>使用点到站点 VPN 连接时需要使用的网络地址范围

使用点到站点 VPN 连接可以从客户端计算机访问 CloudSimple 网络。  [若要设置点到站点 VPN](vpn-gateway.md)，必须指定以下网络地址范围。

| 地址/地址范围 | 描述                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 客户端子网         | 使用点到站点 VPN 进行连接时，客户端子网会提供 DHCP 地址。 在 CloudSimple 门户上创建点到站点 VPN 网关时需要此子网。  网络划分为两个子网：一个用于 UDP 连接，另一个用于 TCP 连接。 |

## <a name="next-steps"></a>后续步骤

* [用于访问私有云的本地防火墙设置](on-premises-firewall-configuration.md)
* [快速入门 - 创建 CloudSimple 服务](quickstart-create-cloudsimple-service.md)
* [快速入门 - 配置私有云](quickstart-create-private-cloud.md)
* 详细了解 [Azure 网络连接](cloudsimple-azure-network-connection.md)
* 详细了解 [VPN 网关](cloudsimple-vpn-gateways.md)
