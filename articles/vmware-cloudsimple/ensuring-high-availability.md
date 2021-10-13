---
title: 确保应用程序在 Azure 上的 VMware 中运行时的高可用性
description: 介绍 CloudSimple 的高可用性功能，该功能用于应对在 CloudSimple 私有云中运行的应用程序的常见应用程序故障情况
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ef9c852370052f5613d7395c7518c6a3f15b8d0
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614681"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>确保应用程序在 Azure 上的 VMware 中运行时的高可用性

CloudSimple 解决方案为在 Azure 环境中的 VMware 上运行的应用程序提供高可用性。 下表列出了故障情况和关联的高可用性功能。

|  故障情况  |  应用程序是否受保护？  |  平台 HA 功能  |  VMware HA 功能  |  Azure HA 功能  |
|----------------------------------------|------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------|
|  磁盘故障  |  是  |  快速替换故障节点  |  [关于 vSAN 默认存储策略](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html)  |  |
|  风扇故障  |  是  |  冗余风扇，快速替换故障节点  |  |  |
|  NIC 故障  |  是  |  冗余 NIC，快速替换故障节点  |  |  |
|  主机电源故障  |  是  |  冗余电源  |  |  |
|  ESXi 主机故障  |  是  |  快速替换故障节点  |  [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html)  |  |
|  VM 故障  |  是  |  [负载均衡器](load-balancers.md)  |  [VMware vSphere 高可用性](https://www.vmware.com/products/vsphere/high-availability.html)  |  用于无状态 VMware VM 的 Azure 负载均衡器  |
|  叶交换机端口故障  |  是  |  冗余 NIC  |  |  |
|  叶交换机故障  |  是  |  冗余叶交换机  |  |  |
|  机架故障  |  是  |  放置组  |  |  |
|  本地 DC 的网络连接  |  是  |  冗余网络服务  |  |  冗余 ER 线路  |
|  Azure 的网络连接  |  是  |  |  |  冗余 ER 线路  |
|  数据中心故障  |  是  |  |  |  可用性区域  |
|  区域故障  |  是  |  |  |  Azure 区域  |

Azure VMware Solution by CloudSimple 提供以下高可用性功能。

## <a name="fast-replacement-of-failed-node"></a>快速替换故障节点

CloudSimple 控制平面软件持续监视 VMware 群集的运行状况，并检测 ESXi 节点发生故障的时间。 然后，它自动将新的 ESXi 主机从随时可用的节点池添加到受影响的 VMware 群集，并从群集中移除发生故障的节点。 此功能可确保快速还原 VMware 群集中的备用容量，以便还原 vSAN 和 VMware HA 提供的群集复原能力。

## <a name="placement-groups"></a>放置组

创建私有云的用户可以在所选区域中选择一个 Azure 区域和一个放置组。 放置组是分布在多个机架上但位于同一脊网络段内的一组节点。 同一放置组中的节点可以彼此联系，最多可以有两个额外的交换机跃点。 放置组始终位于单个 Azure 可用性区域内，并跨多个机架。 CloudSimple 控制平面尽力将私有云的节点分布到多个机架中。 不同放置组中的节点必须放置在不同的机架中。

## <a name="availability-zones"></a>可用性区域

可用性区域是一种高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 可用性区域是 Azure 区域中特殊的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 每个区域都有一个可用性区域。 有关详细信息，请参阅 [Azure 中的可用性区域是什么？](../availability-zones/az-overview.md)。

## <a name="redundant-azure-expressroute-circuits"></a>冗余 Azure ExpressRoute 线路

使用 ExpressRoute 连接到 Azure vNet 的数据中心具有冗余线路，可提供高度可用的网络连接链接。

## <a name="redundant-networking-services"></a>冗余网络服务

私有云的所有 CloudSimple 网络服务（包括 VLAN、防火墙、公共 IP 地址、Internet 和 VPN）都设计为具有高可用性，并能够支持服务 SLA。

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>用于无状态 VMware VM 的 Azure Layer 7 负载均衡器

用户可以将 Azure Layer 7 负载均衡器放置在 VMware 环境中运行的无状态 Web 层 VM 之前，以实现 Web 层的高可用性。

## <a name="azure-regions"></a>Azure 区域

Azure 区域是一组数据中心，部署在定义了延迟的外围中，并通过专用的区域性低延迟网络互相连接。 有关详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions)。
