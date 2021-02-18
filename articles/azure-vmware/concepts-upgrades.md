---
title: 概念-私有云更新和升级
description: 了解 Azure VMware 解决方案中的关键升级过程和功能。
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: d93453cbf6ad744844a04cd298cc18ad181cc0b0
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634986"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware 解决方案私有云更新和升级

Azure VMware 解决方案私有云的一个优点是为你维护平台。 维护包括对 VMware 验证的软件包的自动更新，以帮助确保使用最新版本的 Azure VMware 解决方案私有云软件。

具体而言，Azure VMware 解决方案私有云包括：

- 通过 VMware ESXi 虚拟机监控程序预配的专用裸机服务器节点 
- 用于管理 ESXi 和 vSAN 的 vCenter 服务器 
- VMware NSX-对于 vSphere 工作负荷 Vm，软件定义的网络  
- 适用于 vSphere 工作负荷 Vm 的 VMware vSAN 数据存储  
- 用于工作负荷移动性的 VMware HCX  

Azure VMware 解决方案私有云还包括 Azure 是中的资源，用于连接和运行私有云。 Azure VMware 解决方案持续监视是和 VMware 组件的运行状况。 当 Azure VMware 解决方案检测到故障时，它将采取措施来修复失败的组件。 

## <a name="what-components-get-updated"></a>哪些组件已更新？   

Azure VMware 解决方案更新以下 VMware 组件： 

- 在裸机服务器节点上运行的 vCenter Server 和 ESXi 
- vSAN 
- NSX-T 

Azure VMware 解决方案还更新了是中的软件，例如驱动程序、网络交换机上的软件和裸机节点上的固件。 

## <a name="types-of-updates"></a>更新类型

Azure VMware 解决方案将以下类型的更新应用于 VMware 组件：

- 修补程序： VMware 发布的安全修补程序和 bug 修补程序。 
- 更新：一个或多个 VMware 组件的次要版本更新。 
- 升级：一个或多个 VMware 组件的主要版本更新。

在将修补程序应用到私有云之前和之后，你将收到通知。 在将更新或升级应用到私有云之前，我们还将与你一起计划一个维护时段。 

## <a name="vmware-appliance-backup"></a>VMware 设备备份 

Azure VMware 解决方案还采用以下 VMware 组件的配置备份：

- vCenter Server 
- NSX-T 管理器 

发生故障时，Azure VMware 解决方案可以从配置备份还原这些组件。 

有关 VMware 软件版本的详细信息，请参阅 [私有云和群集概念文章](concepts-private-clouds-clusters.md) 和 [常见问题解答](faq.yml)。

## <a name="next-steps"></a>后续步骤

现在，你已介绍 Azure VMware 解决方案中的关键升级过程和功能，你可能想要了解：

- [如何创建私有云](tutorial-create-private-cloud.md)。
- [如何启用 Azure VMware 解决方案资源](enable-azure-vmware-solution.md)。

<!-- LINKS - external -->

<!-- LINKS - internal -->
