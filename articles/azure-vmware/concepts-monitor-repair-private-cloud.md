---
title: 概念-监视和修复 Azure VMware 解决方案私有云
description: 了解 Azure VMware 解决方案如何监视和修复 Azure VMware 解决方案私有云上的 VMware ESXi 服务器。
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 11/20/2020
ms.openlocfilehash: 7f367a6ef9b86f235a16a698735cfb58a11f6862
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033589"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>监视和修复 Azure VMware 解决方案私有云

Azure VMware 解决方案持续监视 Azure VMware 解决方案私有云上的 VMware ESXi 服务器。 

## <a name="what-azure-vmware-solution-monitors"></a>什么是 Azure VMware 解决方案监视器

Azure VMware 解决方案监视主机上的以下故障情况：  

- 处理器状态 
- 内存状态 
- 连接和电源状态 
- 硬件风扇状态 
- 网络连接丢失 
- 硬件系统板状态 
- VSAN 主机的磁盘 () 出现错误 
- 硬件电压 
- 硬件温度状态 
- 硬件电源状态 
- 存储状态 
- 连接失败 

> [!NOTE]
> Azure VMware 解决方案租户管理员不得编辑或删除以上定义的 VMware vCenter 警报，因为这些警报由 vCenter 上的 Azure VMware 解决方案控制平面管理。 Azure VMware 解决方案监视使用这些警报来触发 Azure VMware 解决方案主机修补过程。

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware 解决方案主机修正  

当 Azure VMware 解决方案检测到租户的私有云上的 Azure VMware 解决方案节点发生降级或失败时，将触发主机修正过程。 主机修正涉及将错误节点替换为新的正常节点。  

主机修正过程通过在群集中添加新的健康节点开始。 然后，在可能的情况下，故障主机会处于 VMware vSphere 维护模式下。 VMware vMotion 用于将 Vm 从故障主机移到群集中的其他可用服务器，这可能会导致工作负荷的实时迁移零停机。 如果无法将故障主机置于维护模式，则会从群集中删除主机。

## <a name="next-steps"></a>后续步骤

下面是一些你可能想要了解的有关的主题：

- [Azure VMware 解决方案私有云升级](concepts-upgrades.md)
- [Azure VMware 解决方案 Vm 的生命周期管理](lifecycle-management-of-azure-vmware-solution-vms.md)
- [通过 Azure 安全中心集成保护 Azure VMware 解决方案 Vm](azure-security-integration.md)
- [备份具有 Azure 备份服务器的 Azure VMware 解决方案 Vm](backup-azure-vmware-solution-virtual-machines.md)
- [使用 Azure VMware 解决方案完成虚拟机灾难恢复](disaster-recovery-for-virtual-machines.md)
