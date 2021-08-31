---
title: 允许跨全球 Azure 区域实现灾难恢复
description: 本文介绍 Azure Site Recovery 中的全球灾难恢复功能。
author: JYOTHIRMAISURI
manager: evansma
ms.service: site-recovery
ms.topic: article
ms.date: 08/09/2021
ms.author: v-jysur
ms.openlocfilehash: 1179558e01ff23c66db652395e10c7d6259af09b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778636"
---
# <a name="enable-global-disaster-recovery-using-azure-site-recovery"></a>允许使用 Azure Site Recovery 进行全球灾难恢复

本文介绍如何将 Azure 虚拟机从一个可用性区域复制、故障转移和故障回复到相同或不同 Azure 区域中的另一个可用性区域。

>[!NOTE]
> Azure Site Recovery 不会将数据移动或存储到所选的源和目标区域之外。 你可以从其他（第三个）区域选择恢复服务保管库。 恢复服务保管库包含元数据，但不包含实际的客户数据。

Azure Site Recovery 服务通过在计划内和计划外服务中断期间使业务应用保持正常运行，来帮助实现业务连续性并实施灾难恢复策略。 我们建议使用灾难恢复选项，以在发生区域性服务中断时使应用程序保持正常运行。

## <a name="disaster-recovery-for-global-azure-regions"></a>全球 Azure 区域的灾难恢复

Azure 是推出具有 Azure 到 Azure 灾难恢复功能的一流云本机灾难恢复解决方案的首个大型公有云提供商。 此产品/服务允许你将应用程序从一个 Azure 区域复制和故障转移到同一大洲的另一个 Azure 区域。 还支持区域到区域灾难恢复。   

Azure Site Recovery 现在支持全球灾难恢复。 你现在可以跨多个洲从任何 Azure 区域复制和故障转移应用程序。

许多人在全球开展业务，他们想要利用 Azure 的全球影响力，在全球范围内托管和复制应用程序。 通过 Azure Site Recovery 利用全球灾难恢复即可实现这一目标。 这一新的产品/服务可消除 Azure 到 Azure 灾难恢复的洲际边界。 通过我们强大的全球网络主干支持，你可以将应用程序复制和故障转移到你选择的全球范围内的任何 Azure 区域。  

此产品/服务完成了区域到区域灾难恢复、洲内灾难恢复和全球灾难恢复的本机-公有云灾难恢复组合。

>[!NOTE]
>选择距离较远的大洲的灾难恢复 (DR) 区域对恢复点目标 (RPO) 有少量影响，但并不显著，不会影响任何 Azure Site Recovery 的 SLA。

此产品/服务完成了区域到区域灾难恢复、洲内灾难恢复和全球灾难恢复的本机-公有云灾难恢复组合。  

## <a name="before-you-begin"></a>在开始之前
本文假设已根据 [Azure 到 Azure 灾难恢复教程](azure-to-azure-tutorial-enable-replication.md)中所述，准备好 Azure Site Recovery 部署。

确保满足先决条件，并且已创建恢复服务保管库。

>[!NOTE]
> 在为你的虚拟机启用复制之前，请先参阅[支持矩阵](azure-to-azure-support-matrix.md)。

## <a name="supported-platform-features"></a>支持的平台功能

| **功能** | **支持声明** |
| --- | --- |
| 经典 VM | 否 |
| ARM VM |  是 |
| Azure 磁盘加密 v1（双通道，使用 Azure Active Directory (Azure AD)） |  是 |
| Azure 磁盘加密 v2（单通道，不使用 Azure AD） |  是 |
|     |  否 |
| 托管磁盘 |  是 |
| 客户管理的密钥 |   是 |
| 邻近放置组 |  是 |
| 备份互操作性 | 支持文件级备份和还原。 不支持磁盘和 VM 级备份与还原。 |
| 热添加/移除 | 可以在启用区域到区域复制后添加磁盘。 不支持在启用区域到区域复制后移除磁盘。 |

## <a name="enable-replication"></a>启用复制

使用以下步骤快速复制你的设备：

1. 在门户中，转到虚拟机。

2. 在左侧的“操作”下，选择“灾难恢复”

3. 对于“基本”，请选择“目标区域” 。

   所有全球可用的 Azure 区域都显示在下拉菜单下。 根据偏好选择目标区域。  

   ![全球灾难恢复的设置](./media/azure-to-azure-enable-global-disaster-recovery/enable-global-disaster-recovery.png)

   你可以根据需要进行其他配置更改。

4. 单击“高级设置”以查看其他设置，如“目标订阅”、“目标虚拟网络”、“目标可用性”、“目标邻近放置组”。

5. 单击“查看 + 开始复制”。

## <a name="next-steps"></a>后续步骤

- 了解有关[区域到区域灾难恢复](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)的详细信息。
- 了解有关 [Azure 到 Azure 灾难恢复体系结构](azure-to-azure-architecture.md)的详细信息
