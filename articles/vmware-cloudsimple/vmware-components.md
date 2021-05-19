---
title: 私有云 VMware 组件
titleSuffix: Azure VMware Solution by CloudSimple
description: 了解如何利用 CloudSimple 服务在 Azure 位置中部署本机 VMware。 私有云与 Azure 云的其余部分集成。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff9aec1904be754990958869666e9d67038e4fb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88142496"
---
# <a name="private-cloud-vmware-components"></a>私有云 VMware 组件

私有云是管理域中由 vCenter 服务器托管的独立 VMware 堆栈（ESXi 主机、vCenter、vSAN 和 NSX）环境。  利用 CloudSimple 服务，可以在 Azure 位置中的 Azure 裸机基础结构上部署本机 VMware。  私有云与 Azure 云的其余部分集成。  使用以下 VMware 堆栈组件部署私有云：

* VMware ESXi - Azure 专用节点上的虚拟机监控程序
* VMware vCenter - 用于集中管理私有云 vSphere 环境的设备
* VMware vSAN - 超聚合基础结构解决方案
* VMware NSX Data Center - 网络虚拟化和安全软件  

## <a name="vmware-component-versions"></a>VMware 组件版本

使用以下软件版本部署私有云 VMware 堆栈。

| 组件 | 版本 | 许可版本 |
|-----------|---------|------------------|
| ESXi | 6.7U2 | Enterprise Plus |
| vCenter | 6.7U2 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.4.1 | 高级 |

## <a name="esxi"></a>ESXi

创建私有云时，VMware ESXi 安装在预配的 CloudSimple 节点上。  ESXi 提供用于部署工作负载虚拟机 (VM) 的虚拟机监控程序。  节点在私有云上提供超聚合基础结构（计算和存储）。  节点是私有云上的 vSphere 群集的一部分。  每个节点都有四个连接到基础网络的物理网络接口。  两个物理网络接口用于在 vCenter 上创建 vSphere 分布式交换机 (VDS)，另外两个用于创建 NSX 托管的虚拟分布式交换机 (N-VDS)。  在主动-主动模式下配置网络接口以实现高可用性。

详细了解 VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter 服务器设备

vCenter 服务器设备 (VCSA) 为 VMware Solution by CloudSimple 提供身份验证、管理和业务流程功能。 创建私有云时，会部署具有嵌入式 Platform Services Controller (PSC) 的 VCSA。  在部署私有云时创建的 vSphere 群集上部署 VCSA。  每个私有云都有其自己的 VCSA。  私有云的扩展将节点添加到私有云上的 VCSA 中。

### <a name="vcenter-single-sign-on"></a>vCenter 单一登录

VCSA 上的嵌入式 Platform Services Controller 与 vCenter 单一登录域相关联。  域名为 cloudsimple.local。  系统会创建默认用户 CloudOwner@cloudsimple.com 以访问 vCenter。  可以添加本地/Azure Active Directory [vCenter 标识源](set-vcenter-identity.md)。

## <a name="vsan-storage"></a>vSAN 存储

私有云是使用完全配置的全闪存 vSAN 存储（本地到群集）创建的。  使用 vSAN 数据存储创建 vSphere 群集需要至少三个相同 SKU 的节点。  默认情况下，会在 vSAN 数据存储上启用重复数据删除和压缩。  两个磁盘组在 vSphere 群集的每个节点上创建。 每个磁盘组都包含一个缓存磁盘和三个容量磁盘。

默认 vSAN 存储策略在 vSphere 群集上创建并应用于 vSAN 数据存储。  此策略确定如何在数据存储中预配和分配 VM 存储对象，以确保所需的服务级别。  存储策略定义容忍的失败次数 (FTT) 和失败容忍方法。  可以创建新的存储策略并将其应用到 VM。 若要维护 SLA，必须在 vSAN 数据存储上维护 25% 的备用容量。  

### <a name="default-vsan-storage-policy"></a>默认 vSAN 存储策略

下表显示默认 vSAN 存储策略参数。

| vSphere 群集中的节点数 | FTT | 失败容忍方法 |
|------------------------------------|-----|--------------------------|
| 3 到 4 个节点 | 1 | RAID 1（镜像）- 创建 2 个副本 |
| 5 到 16 个节点 | 2 | RAID 1（镜像）- 创建 3 个副本 |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center 在私有云上提供网络虚拟化、微分段和网络安全功能。  可以通过 NSX 在私有云上配置 NSX Data Center 支持的所有服务。  创建私有云时，会安装并配置以下 NSX 组件。

* NSXT 管理器
* 传输区域
* 主机和边缘上行配置文件
* 边缘传输、Ext1 和 Ext2 的逻辑交换机
* ESXi 传输节点的 IP 池
* 边缘传输节点的 IP 池
* 边缘节点
* 适用于控制器和 Edge VM 的 DRS 反关联规则
* 第 0 层路由器
* 在第 0 层路由器上启用 BGP

## <a name="vsphere-cluster"></a>vSphere 群集

将 ESXi 主机配置为群集，以确保私有云的高可用性。  创建私有云时，会在第一个群集上部署 vSphere 的管理组件。  为管理组件创建了资源池，并在此资源池中部署了所有管理 VM。 无法删除第一个群集来收缩私有云。  vSphere 群集使用 vSphere HA 为 VM 提供高可用性。  容忍的失败次数基于群集中的可用节点数。  可以使用公式 ```Number of nodes = 2N+1```，其中 ```N``` 是容忍的失败次数。

### <a name="vsphere-cluster-limits"></a>vSphere 群集限制

| 资源 | 限制 |
|----------|-------|
| 要创建私有云（第一个 vSphere 群集）的最小节点数 | 3 |
| 私有云上的 vSphere 群集中的最大节点数 | 16 |
| 私有云中的最大节点数 | 64 |
| 私有云中的最大 vSphere 群集数 | 21 |
| 新 vSphere 群集上的最小节点数 | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware 基础结构维护

偶尔需要对 VMware 基础结构的配置进行更改。 目前，该维护可能 1-2 个月进行一次，但维护频率预计会随着时间的推移而降低。 通常，这种类型的维护不会干扰 CloudSimple 服务的正常使用。 在 VMware 维护期间，以下服务会继续正常运行，不会产生任何影响：

* VMware 管理平面和应用程序
* vCenter 访问
* 所有网络和存储
* 所有 Azure 流量

## <a name="updates-and-upgrades"></a>更新和升级

CloudSimple 负责在私有云中对 VMware 软件（ESXi、vCenter、PSC 和 NSX）进行生命周期管理。

软件更新包括：

* **补丁**。 VMware 发布的安全补丁或 bug 修补程序。
* 更新。 VMware 堆栈组件的次要版本更改。
* **升级**。 VMware 堆栈组件的主版本更改。

CloudSimple 可以在 VMware 发布关键安全补丁后立即对其进行测试。 按照 SLA，CloudSimple 在一周内向私有云环境推出安全修补程序。

CloudSimple 提供对 VMware 软件组件的季度维护更新。 当新的 VMware 软件主版本可用时，CloudSimple 与客户合作以协调适合升级的维护时段。  

## <a name="next-steps"></a>后续步骤

* [CloudSimple 维护和更新](cloudsimple-maintenance-updates.md)
