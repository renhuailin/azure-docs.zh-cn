---
title: Azure VMware Solution by CloudSimple 中的 VLAN 和子网
description: 了解 CloudSimple 私有云中的 VLAN 和子网，以及 CloudSimple 提供的部署 CloudSimple 服务的网络。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e3235b394f739e8af846f30dfe51705d0926c843
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140660"
---
# <a name="vlans-and-subnets-overview"></a>VLAN 和子网概述

CloudSimple 为每个区域都提供了部署 CloudSimple 服务的网络。  网络是一个默认启用路由的 TCP 第 3 层地址空间。  在此区域中创建的所有私有云和子网无需任何额外配置即可相互通信。  可以使用 VLAN 在 vCenter 上创建分布式端口组。

![CloudSimple 网络拓扑](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

为每个私有云都会创建 VLAN（第 2 层网络）。  第 2 层流量停留在私有云的边界内，便于你隔离私有云中的本地流量。  在私有云上创建的 VLAN 只能用于创建相应私有云中的分布式端口组。  在私有云上创建的 VLAN 会自动配置在连接到私有云的主机的所有交换机上。

## <a name="subnets"></a>子网

在创建 VLAN 时，可以通过定义子网的地址空间来创建子网。 地址空间中的 IP 地址作为子网网关进行分配。 为每个客户和区域都会分配一个专用的第 3 层地址空间。 可以在网络区域中使用本地网络或 Azure 虚拟网络配置任何 RFC 1918 非重叠地址空间。

默认情况下，所有子网都可以相互通信，减少了私有云之间路由的配置开销。 跨同一区域中 PC 的东-西数据停留在相同的第 3 层网络中，并通过此区域内的本地网络基础结构进行传输。 区域内私有云之间的通信不需要出口。 这种方法消除了在不同的私有云中部署不同的工作负载造成的任何 WAN/出口性能损失。

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN 子网 CIDR 范围

私有云创建为由 vCenter 服务器托管的独立 VMware 堆栈（ESXi 主机、vCenter、vSAN 和 NSX）环境。  管理组件部署在为 vSphere/vSAN 子网 CIDR 选择的网络中。  在部署过程中，网络 CIDR 范围被划分为不同的子网。

* 最小 vSphere/vSAN 子网 CIDR 范围前缀：/24
* 最大 vSphere/vSAN 子网 CIDR 范围前缀：/21

> [!CAUTION]
> vSphere/vSAN CIDR 范围中的 IP 地址保留给私有云基础结构使用。  请不要在任何虚拟机上使用此范围内的 IP 地址。

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN 子网 CIDR 范围限制

选择 vSphere/vSAN 子网 CIDR 范围大小会影响私有云的大小。  根据 vSphere/vSAN 子网 CIDR 的大小，可配置的最大节点数如下表所示。

| 指定的 vSphere/vSAN 子网 CIDR 前缀长度 | 最大节点数 |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>在私有云上创建的管理子网

以下管理子网在创建私有云时创建。

* 系统管理： ESXi 主机的管理网络、DNS 服务器、vCenter 服务器的 VLAN 和子网。
* VMotion： ESXi 主机的 vMotion 网络的 VLAN 和子网。
* VSAN： ESXi 主机的 vSAN 网络的 VLAN 和子网。
* NsxtEdgeUplink1： VLAN 上行链路到外部网络的 VLAN 和子网。
* NsxtEdgeUplink2： VLAN 上行链路到外部网络的 VLAN 和子网。
* NsxtEdgeTransport： 控制 NSX-T 中第 2 层网络范围的传输区域的 VLAN 和子网。
* NsxtHostTransport： 主机传输区域的 VLAN 和子网。

### <a name="management-network-cidr-range-breakdown"></a>管理网络 CIDR 范围分解

指定的 vSphere/vSAN 子网 CIDR 范围被划分为多个子网。  下表展示了允许的前缀的分解示例。  此示例使用 192.168.0.0 作为 CIDR 范围。

示例：

| 指定的 vSphere/vSAN 子网 CIDR/前缀 | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| 系统管理 | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T 主机传输 | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T 边缘传输 | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T 边缘上行链路 1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T 边缘上行链路 2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>后续步骤

* [创建并管理 VLAN 和子网](create-vlan-subnet.md)
