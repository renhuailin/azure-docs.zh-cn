---
title: PIM 中访问评审的资源仪表板 - Azure AD | Microsoft Docs
description: 介绍如何使用资源仪表板在 Azure AD Privileged Identity Management (PIM) 中执行访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d87ff7dc27dea5b25995dcfbd7f969a6d01fec56
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669257"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>使用资源仪表板在 Privileged Identity Management 中执行访问评审

可以使用资源仪表板在 Privileged Identity Management (PIM) 中执行访问评审。 Azure Active Directory (Azure AD) 中的“管理员视图”仪表板有三个主要组件：

- 资源角色激活操作的图形表示形式
- 按分配类型显示角色分配分布情况的图表
- 一个数据区域，其中包含有关新角色分配的信息

![管理员视图仪表板的屏幕截图，显示图形和图表](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![显示数据列表的“管理员视图”仪表板屏幕截图](media/pim-resource-roles-overview-dashboards/role-settings.png)

过去七天执行的资源角色激活操作的图形表示形式。 此数据对应于选定的资源，显示最常见角色（所有者、参与者、用户访问管理员）以及所有角色的激活情况。

在激活图形的一侧，有两个图表按分配类型显示了用户和组的角色分配分布情况。 选择图表中的切片会将值更改为百分比（或相反）。

图表下方列出了过去 30 天获得了新角色分配的用户和组的数量，以及按分配总数以降序排列的角色。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中启动对 Azure 资源角色的访问评审](./pim-create-azure-ad-roles-and-resource-roles-review.md)