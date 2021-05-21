---
title: 在 Azure 实验室服务中为教室实验室使用仪表板 | Microsoft Docs
description: 了解如何在 Azure 实验室服务中为教室实验室使用仪表板。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a91e1649d913956219ddcf192ab315a245b32eae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434594"
---
# <a name="dashboard-for-labs"></a>实验室的仪表板
本文介绍了 Azure 实验室服务中教室实验室的仪表板视图。 

![屏幕捕获显示了 Azure 实验室服务中教室实验室的仪表板视图](./media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>“成本和计费”磁贴
此磁贴提供以下成本估算详细信息：

| 设置 | 值 | 
| ------- | ----- | 
| 配额小时数 | 用户在计划时间之外可以使用 VM 的最大小时数。 |
| 计划小时数 | 根据实验室中设置的计划将产生的小时数。 仅当在所有计划事件上都设置了“自/至”日期时，此值才可用。 |
| 小时数/用户 | 配额小时数和计划小时数之和。 |
| 最大用户数 | 实验室中基于所有要声明的虚拟机得出的最大用户数。 |
| 小时数 x 用户数 | 小时数/用户乘以用户数。 |
| 调整的配额 | 添加到特定用户的配额小时数之和。 |
| 总小时数 * $/小时 | 基于所选 VM 大小的每小时成本。 此成本基于标准即付即用价格。 |
| 总估计成本 | 这是此实验室基于当前设置的最高价格。 |

## <a name="template-tile"></a>“模板”磁贴
可在此磁贴上看到以下信息：

- 创建模板的日期 
- 上次发布模板的日期 

它还提供一个链接，用于导航到“模板”页，可在其中[管理类的模板 VM](how-to-create-manage-template.md)。 

## <a name="virtual-machine-pool-tile"></a>“虚拟机池”磁贴

可在此磁贴上看到以下信息：

- 分配给学生（用户）的虚拟机数
- 尚未分配给学生的虚拟机数

它还提供一个链接，用于导航到“虚拟机池”页，可在其中[管理实验室中的虚拟机池](how-to-set-virtual-machine-passwords.md)。 

## <a name="users-tile"></a>“用户”磁贴

可在此磁贴上看到以下信息：

- 注册到类的用户数
- 已添加到实验室但未注册到类的用户数 

它还提供一个链接，用于导航到“用户”页，可在其中[管理实验室的用户](how-to-configure-student-usage.md)。 

## <a name="schedules-tile"></a>“计划”磁贴
可在此磁贴上看到实验室当前的计划事件。 它还提供一个链接，用于导航到“计划”页，可在其中[创建和管理计划](how-to-create-schedules.md)。 该磁贴仅显示两个计划事件的详细信息，以及实验室剩余计划事件的数量。 

![计划事件](./media/use-dashboard/scheduled-events.png)

