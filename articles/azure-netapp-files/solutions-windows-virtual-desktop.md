---
title: 将 Azure NetApp 文件与 Azure 虚拟桌面配合使用 | Microsoft Docs
description: 提供了有关使用 Azure NetApp 文件部署 Azure 虚拟桌面的最佳实践指南和示例蓝图。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: dba2c43f0b146816fcbb07419efa7ff4cd926ebe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733360"
---
# <a name="benefits-of-using-azure-netapp-files-with-azure-virtual-desktop"></a>将 Azure NetApp 文件与 Azure 虚拟桌面配合使用的好处 

本文提供了有关使用 Azure NetApp 文件部署 Azure 虚拟桌面的最佳实践指南。

Azure NetApp 文件是 Azure 的一项高性能文件存储服务。 它可以提供高达 450,000 的 IOPS 和亚毫秒级的延迟，支持超大规模的 Azure 虚拟桌面部署。 你可以几乎立即按需调整带宽并更改 Azure NetApp 文件卷的服务级别，而不会暂停 IO，同时保留数据平面访问权限。 此功能可让你根据成本轻松优化 Azure 虚拟桌面部署规模。 你还可以创建节省空间的时间点卷快照，而不会影响卷性能。 借助此功能，你可以通过 `~snapshot` 目录中的副本回滚单个 [FSLogix 用户配置文件容器](../virtual-desktop/store-fslogix-profile.md)，或通过卷还原功能立即回滚整个卷。  拥有多达 255 个（旋转）快照，可保护卷免遭数据丢失或损坏，管理员有很多机会撤消已完成的操作。

## <a name="sample-blueprints"></a>示例蓝图

以下示例蓝图显示了 Azure 虚拟桌面与 Azure NetApp 文件的集成。 在共用桌面场景中，使用[多会话虚拟机](../virtual-desktop/windows-10-multisession-faq.yml#what-is-windows-10-enterprise-multi-session)将用户定向到池中可用的最佳会话（[广度优先模式](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-method)）主机。 另一方面，在每个用户都有自己的虚拟机的场景中，将保留个人桌面。

### <a name="pooled-desktop-scenario"></a>共用桌面场景

对于共用场景，Azure 虚拟桌面团队按用户计数向 vCPU [建议](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations)以下指导。 请注意，在此建议中未指定虚拟机大小。

|     工作负荷类型     |     亮    |     中型    |     重型    |
|-----------------------|--------------|---------------|--------------|
|     用户/vCPU    |     6        |     4         |     2        |


此建议已通过 500 个用户的 LoginVSI 测试得到证实，该测试将大约 62 个“知识/中等用户”登录到每个 D16as_V4 虚拟机上。 

例如，每个 D16as_V4 虚拟机 62 个用户，Azure NetApp 文件可以轻松地为每个环境中的 60,000 个用户提供支持。 用于评估 D32as_v4 虚拟机的上限的测试正在进行中。 如果 Azure 虚拟桌面用户/vCPU 建议适用于 D32as_v4，那么在提出 [1,000 个 IP VNet 限制](./azure-netapp-files-network-topologies.md)之前，将有超过 120,000 个用户容纳在 1,000 个虚拟机中，如下图所示。  

![Azure 虚拟桌面共用桌面场景](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>个人桌面场景 

在个人桌面场景中，下图显示了一般用途的体系结构建议。 用户被映射到特定的桌面 Pod，每个 Pod 只包含不到 1,000 个虚拟机，并为从管理 VNet 传播的 IP 地址留出空间。 Azure NetApp 文件可轻松处理每个单会话主机池 VNet 的 900 多个个人桌面，而虚拟机的实际数量将等于 1,000 减去中心 VNet 中的管理主机数。 如果需要更多的个人桌面，可以轻松添加更多 Pod（主机池和虚拟网络），如下图所示。 

![Windows 虚拟桌面个人桌面场景](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

构建像这样基于 POD 的体系结构时，在登录时将用户分配到的正确 Pod 至关重要，因为这样可以确保用户始终找到其用户配置文件。 

## <a name="next-steps"></a>后续步骤

- [使用 Azure NetApp 文件的解决方案体系结构](azure-netapp-files-solution-architectures.md)