---
title: 在 VMware 虚拟机上使用 Azure 应用程序网关
description: 介绍如何使用 Azure 应用程序网关来管理 CloudSimple 私有云环境中 VMware 虚拟机上运行的 Web 服务器的传入 Web 流量
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d5ec66203ac5f9d153869bf82eada6fb1765851
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618245"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>在 CloudSimple 私有云环境中的 VMware 虚拟机上使用 Azure 应用程序网关

可以使用 Azure 应用程序网关来管理 CloudSimple 私有云环境中 VMware 虚拟机上运行的 Web 服务器的传入 Web 流量。

利用公共-专用混合部署中的 Azure 应用程序网关，可以管理发往应用程序的 Web 流量，提供安全前端，并为这些应用程序在 VMware 环境中运行的服务卸载 TLS 处理工作量。 Azure 应用程序网关根据配置的规则和运行状况探测，将传入的 Web 流量路由到 VMware 环境中的后端池实例。

此 Azure 应用程序网关解决方案需要：

* 一个 Azure 订阅。
* 创建并配置一个 Azure 虚拟网络，并在该虚拟网络中配置一个子网。
* 创建并配置 NSG 规则，并使用 ExpressRoute 将 vNet 对等互连到 CloudSimple 私有云。
* 创建并配置私有云。
* 创建并配置 Azure 应用程序网关。

## <a name="azure-application-gateway-deployment-scenario"></a>Azure 应用程序网关部署方案

在此方案中，Azure 应用程序网关在 Azure 虚拟网络中运行。 该虚拟网络已通过 ExpressRoute 线路连接到私有云。 可以通过虚拟网络子网中的 IP 访问私有云中的所有子网。

![Azure 虚拟网络中的 Azure 负载均衡器](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>如何部署该解决方案

部署过程包括以下任务：

1. [验证是否满足先决条件](#1-verify-prerequisites)
2. [将 Azure 虚拟连接连接到私有云](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [部署 Azure 应用程序网关](#3-deploy-an-azure-application-gateway)
4. [在私有云中创建并配置 Web 服务器 VM 池](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. 验证先决条件

验证是否满足以下先决条件：

* 已创建 Azure 资源管理器和虚拟网络。
* 已在 Azure 虚拟网络中创建专用子网（用于应用程序网关）。
* 已创建 CloudSimple 私有云。
* 虚拟网络中的 IP 子网与私有云中的子网之间不存在 IP 冲突。

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. 将 Azure 虚拟网络连接到私有云

若要将 Azure 虚拟网络连接到私有云，请遵循此过程。

1. [在 CloudSimple 门户中复制 ExpressRoute 对等互连信息](virtual-network-connection.md)。

2. [为 Azure 虚拟网络配置虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。

3. [将虚拟网络链接到 CloudSimple ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)。

4. [使用复制的对等互连信息将虚拟网络链接到 ExpressRoute 线路](virtual-network-connection.md)。

## <a name="3-deploy-an-azure-application-gateway"></a>3. 部署 Azure 应用程序网关

[通过 Azure 门户使用基于路径的路由规则创建应用程序网关](../application-gateway/create-url-route-portal.md)中提供了有关此操作的详细说明。 下面是所需步骤的摘要：

1. 在订阅和资源组中创建虚拟网络。
2. 在虚拟网络中创建子网（用作专用子网）。
3. 创建标准应用程序网关（选择性地启用 WAF）：在 Azure 门户主页中，单击页面左上方的“资源” > “网络” > “应用程序网关”  。 选择标准 SKU 和大小，并提供 Azure 订阅、资源组和位置信息。 如果需要，请为此应用程序网关创建新的公共 IP，并提供有关此应用程序网关的虚拟网络和专用子网的详细信息。
4. 添加包含虚拟机的后端池，并将其添加到应用程序网关。

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. 在私有云中创建并配置 Web 服务器 VM 池

在 vCenter 中，创建包含所选 OS 和 Web 服务器（例如 Windows/IIS 或 Linux/Apache）的 VM。 选择为私有云中的 Web 层指定的子网/VLAN。 验证 Web 服务器 VM 上是否至少有一个 vNIC 位于 Web 层子网中。
