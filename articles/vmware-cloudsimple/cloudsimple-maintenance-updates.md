---
title: CloudSimple 维护和更新
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述与计划的维护和更新对应的 CloudSimple 服务过程
author: sharaths-cs
ms.author: dikamath
ms.date: 03/09/2021
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1cbd708f6c1f5159655099d5d72900ab0c608147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522302"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple 维护和更新

根据设计，私有云环境应该没有单一故障点。

* ESXi 群集配置了 vSphere 高可用性 (HA)。 群集的大小设置为至少有一个用于复原的备用节点。
* vSAN 提供冗余的主存储，要求至少使用三个节点来防范单一故障。 可以配置 vSAN，使之能够为更大的群集提供更强的复原能力。
* vCenter、PSC 和 NSX 管理器 VM 配置了 RAID-10 存储来防范存储故障。 VM 通过 vSphere HA 来防范节点/网络故障。
* ESXi 主机有冗余风扇和 NIC。
* 在 HA 对中配置了 TOR 和主干交换机，用于提供复原能力。

CloudSimple 持续监视以下 VM 的正常运行时间和可用性，并提供可用性 SLA：

* ESXi 主机
* vCenter
* PSC
* NSX 管理器

CloudSimple 还会持续监视以下项的故障：

* 硬盘
* 物理 NIC 端口
* 服务器
* 风扇
* 强力
* 交换机
* 交换机端口

如果某个磁盘或节点发生故障，则会自动将新的节点添加到受影响的 VMware 群集，使其立即回到正常运行状况。

CloudSimple 会在私有云中备份、维护和更新这些 VMware 元素：

* ESXi
* vCenter 平台服务
* 控制器
* vSAN
* NSX

## <a name="back-up-and-restore"></a>备份和还原

CloudSimple 备份包括：

* 对 VCenter、PSC 和 DVS 规则进行的夜间增量备份。
* vCenter 原生 API，用于在应用程序层备份组件。
* 在更新或升级 VMware 管理软件之前进行的自动备份。
* 在数据通过 TLS 1.2 加密通道传输到 Azure 之前在源中进行的 vCenter 数据加密。 数据存储在 Azure blob 中，在其中进行跨区域复制。

可以通过创建[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来请求还原。

## <a name="maintenance"></a>维护

CloudSimple 执行多种类型的计划内维护。

### <a name="backendinternal-maintenance"></a>后端/内部维护

此维护通常涉及重新配置物理资产或安装软件补丁。 它不会影响所维护的资产的正常使用。 由于冗余 NIC 分布到每个物理机架，因此正常的网络流量和私有云操作不会受到影响。 仅当你的组织希望在维护时段内使用全部冗余带宽时，你才会注意到性能影响。

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple 门户维护

更新 CloudSimple 控制平面或基础结构时，必须进行某种程度的服务停机。 目前，维护频率可以达到每月一次。 随着时间的推移，此频率预计会降低。 CloudSimple 会提供门户维护通知，并会尽量缩短维护时间。 在门户维护期间，以下服务会继续正常运行，不会产生任何影响：

* VMware 管理平面和应用程序
* vCenter 访问
* 所有网络和存储
* 所有 Azure 流量

### <a name="vmware-infrastructure-maintenance"></a>VMware 基础结构维护

偶尔需要对 VMware 基础结构的配置进行更改。  目前，该维护可能 1-2 个月进行一次，但维护频率预计会随着时间的推移而降低。 通常，这种类型的维护不会干扰 CloudSimple 服务的正常使用。 在 VMware 维护期间，以下服务会继续正常运行，不会产生任何影响：

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

CloudSimple 可以在 VMware 发布关键安全补丁后立即对其进行测试。 

在部署下一个计划的更新之前，将会实施有文档记录的 VMware 解决方法，而不是安装相应的补丁。 

## <a name="next-steps"></a>后续步骤

[使用 Veeam 备份工作负荷 VM](backup-workloads-veeam.md)
