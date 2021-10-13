---
title: Azure VMware Solution by CloudSimple - 创建 CloudSimple 私有云
description: 介绍了如何创建 CloudSimple 私有云，以将 VMware 工作负载扩展到具有操作灵活性和连续性的云中
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6a17c7c79acd770ddcb17e4c5c938ad1d1ffc26b
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615061"
---
# <a name="create-a-cloudsimple-private-cloud"></a>创建 CloudSimple 私有云

私有云是支持 ESXi 主机、vCenter、vSAN 和 NSX 的独立 VMware 堆栈。 私有云通过 CloudSimple 门户进行管理。 它们在各自的管理域中有各自的 vCenter 服务器。 堆栈运行在专用节点和独立裸机硬件节点上。

创建私有云有助于满足对网络基础结构的各种常见需求：

* 增长。 如果已达到现有基础结构的硬件刷新点，那么使用私有云可以进行扩展，而不需要新的硬件投资。

* 快速扩展。 如果出现任何临时或计划外的容量需求，那么使用私有云可以在没有延迟的情况下创建额外容量。

* 增加保护。 使用包含三个或更多节点的私有云，可以获得自动冗余和高可用性保护。

* 长期基础结构需求。 如果数据中心容量已满，或你要通过重构来降低成本，那么使用私有云可以停用数据中心，并迁移到基于云的解决方案，同时与企业运营保持兼容。

如果创建私有云，则会得到一个 vSphere 群集以及在此群集中创建的所有管理 VM。

## <a name="before-you-begin"></a>准备阶段

必须先预配节点，然后才能创建私有云。 若要详细了解如何预配节点，请参阅[为 Azure VMware Solution by CloudSimple 预配节点](create-nodes.md)。

为私有云的 vSphere/vSAN 子网分配 CIDR 范围。 私有云创建为由 vCenter 服务器管理的独立 VMware 堆栈环境（包含 ESXi 主机、vCenter、vSAN 和 NSX）。 管理组件部署在为 vSphere/vSAN 子网 CIDR 选择的网络中。 在部署过程中，网络 CIDR 范围被划分为不同的子网。 vSphere/vSAN 子网地址空间必须是唯一的。 它不得与任何与 CloudSimple 环境通信的网络重叠。 与 CloudSimple 通信的网络包括本地网络和 Azure 虚拟网络。 若要详细了解 vSphere/vSAN 子网，请参阅 VLAN 和子网概述。

* 最小 vSphere/vSAN 子网 CIDR 范围前缀：/24
* 最大 vSphere/vSAN 子网 CIDR 范围前缀：/21


## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问 [CloudSimple 门户](access-cloudsimple-portal.md)。

## <a name="create-a-new-private-cloud"></a>新建私有云

1. 选择“所有服务”  。
2. 搜索“CloudSimple 服务”。
3. 选择要在其上创建私有云的 CloudSimple 服务。
4. 在“概述”中，单击“创建私有云”，为 CloudSimple 门户打开新的浏览器标签页。 如果出现提示，请使用 Azure 登录凭据进行登录。

    ![在 Azure 中创建私有云](media/create-private-cloud-from-azure.png)

5. 在 CloudSimple 门户中，命名私有云。
6. 选择私有云的“位置”。
7. 选择与在 Azure 上预配的类型一致的“节点类型”。
8. 指定“节点计数”。  至少需要三个节点才能创建私有云。

    ![创建私有云 - 基本信息](media/create-private-cloud-basic-info.png)

9. 单击“下一步: 高级选项”。
10. 输入 vSphere/vSAN 子网的 CIDR 范围。 请确保 CIDR 范围不与任何本地或其他 Azure 子网（虚拟网络）或网关子网重叠。

    CIDR 范围选项：/24、/23、/22 或 /21。 /24 CIDR 范围支持最多 9 个节点，/23 CIDR 范围支持最多 41 个节点，而 /22 和 /21 CIDR 范围支持最多 64 个节点（私有云中的最大节点数）。

    > [!IMPORTANT]
    > vSphere/vSAN CIDR 范围中的 IP 地址预留给私有云基础结构使用。  请不要在任何虚拟机上使用此范围内的 IP 地址。

11. 单击“下一步: 审阅并创建”。
12. 查看设置。 如果需要更改任何设置，请单击“上一步”。
13. 单击“创建”。

此时，私有云预配过程开始。 预配私有云最长可能需要两个小时才能完成。

有关扩展现有私有云的说明，请参阅[扩展私有云](expand-private-cloud.md)。
