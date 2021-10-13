---
title: 在 Privileged Identity Management (PIM) - Azure AD 中查看特权访问组分配的审核历史记录 | Microsoft Docs
description: 在 Azure AD Privileged Identity Management (PIM) 中查看特权访问组分配的活动和审核历史记录。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72a415ba766dd9a1149dfe1089fe96ff2924988b
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668098"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>在 Privileged Identity Management 中审核特权访问组分配的活动历史记录（预览）

使用 Privileged Identity Management (PIM)，可以查看 Azure Active Directory (Azure AD) 组织中 Azure 特权访问组成员和所有者的活动、激活和审核历史记录。

> [!NOTE]
> 如果你的组织已将管理功能外包给使用 [Azure Lighthouse](../../lighthouse/overview.md) 的服务提供商，则此处将不会显示该服务提供商授权的角色分配。

按以下步骤查看特权访问组的审核历史记录。

## <a name="view-resource-audit-history"></a>查看资源审核历史记录

可以通过资源审核查看与特权访问组关联的所有活动。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“特权访问组（预览）”。

1. 选择要查看其审核历史记录的特权访问组。

1. 在“活动”下，选择“资源审核” 。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    ![带筛选器的资源审核列表](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>查看我的审核

“我的审核”允许你查看特权访问组的个人角色活动。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“特权访问组（预览）”。

1. 选择要查看其审核历史记录的特权访问组。

1. 在“活动”下，选择“我的审核” 。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    ![当前用户的审核列表](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配组成员资格和所有权](groups-assign-member-owner.md)
- [在 PIM 中批准或拒绝特权访问组的请求](groups-approval-workflow.md)
- [在 PIM 中查看 Azure AD 角色的审核历史记录](groups-audit.md)
