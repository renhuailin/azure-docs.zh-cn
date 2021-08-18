---
title: 查看 Azure AD PIM 中 Azure AD 角色的审核日志报表 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中查看 Azure AD 角色的审核日志历史记录。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 06/03/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28b901643e150cf98b97633f0dbec0a348b3995
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112232858"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中查看 Azure AD 角色的审核历史记录

可以使用 Privileged Identity Management (PIM) 审核历史记录来查看过去 30 天内所有特权角色的所有角色分配和激活操作。 如果审核数据的保留时间需要长于默认保留期，可以使用 Azure Monitor 将其路由到 Azure 存储帐户。 有关详细信息，请参阅[将 Azure AD 日志存档到 Azure 存储帐户](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)。 若要查看 Azure Active Directory (Azure AD) 组织中活动的完整审核历史记录（包括管理员、最终用户和同步活动），可以使用 [Azure Active Directory 安全和活动报告](../reports-monitoring/overview-reports.md)。

按以下步骤查看 Azure AD 角色的审核历史记录。

## <a name="view-resource-audit-history"></a>查看资源审核历史记录

可以通过资源审核查看与 Azure AD 角色关联的所有活动。

1. 打开“Azure AD Privileged Identity Management”。 

1. 选择“Azure AD 角色”  。

1. 选择“资源审核”  。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    ![带筛选器的资源审核列表](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>查看我的审核

使用“我的审核”，可以查看你的个人角色活动。

1. 打开“Azure AD Privileged Identity Management”。 

1. 选择“Azure AD 角色”  。

1. 选择要查看其审核历史记录的资源。

1. 选择“我的审核”  。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    ![当前用户的审核列表](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中查看 Azure 资源角色的活动和审核历史记录](azure-pim-resource-rbac.md)
