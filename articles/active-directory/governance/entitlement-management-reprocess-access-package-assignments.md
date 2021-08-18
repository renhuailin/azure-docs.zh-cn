---
title: 在 Azure AD 权利管理中重新处理访问包的分配 - Azure Active Directory
description: 了解如何在 Azure Active Directory 权利管理中重新处理访问包的分配。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/25/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1b488f8c9331932b82ab0ab55db9c7dcb652add
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129234"
---
# <a name="reprocess-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中重新处理访问包的分配

作为访问包管理员，你可以使用重新处理功能在访问包中自动重新评估和强制执行用户的原始分配。 重新处理后，如果用户对资源的访问受到除权利管理之外的更改的影响，用户无需重复访问包请求过程。

例如，用户可能已被手动从组中删除，从而导致该用户失去对必要资源的访问权限。 

权利管理不会阻止对访问包资源进行外部更新，因此权利管理 UI 无法准确显示此更改。 因此，即使用户不再拥有对资源的访问权限，用户的分配状态也会显示为“已送达”。 但是，如果重新处理用户的分配，则会再次将用户分配添加到访问包的资源。 重新处理可确保访问包分配是最新的，用户有权访问必要的资源，并且分配准确反映在 UI 中。

本文介绍如何重新处理现有访问包中的分配。

## <a name="prerequisites"></a>必备条件

若要使用 Azure AD 权利管理并将用户分配到访问包，必须具有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5 许可证

## <a name="open-an-existing-access-package-and-reprocess-user-assignments"></a>打开现有访问包并重新处理用户分配

必备角色：全局管理员、Identity Governance 管理员、用户管理员、目录所有者、访问包管理员或访问包分配管理员

如果你的用户的状态为“已送达”，但是无权访问访问包内的资源，则你可能需要重新处理分配，以将这些用户重新分配到访问包的资源。 按照以下步骤重新处理现有访问包的分配：

1.  登录 [Azure 门户](https://portal.azure.com)。

1.  依次单击“Azure Active Directory”、“Identity Governance” 。

1.  在左侧菜单中，单击“访问包”，然后打开包括要重新处理的用户分配的访问包。

1.  在左侧的“管理”下，单击“分配” 。

    ![Azure 门户中的权利管理](./media/entitlement-management-reprocess-access-package-assignments/reprocess-access-package-assignment.png)

1.  选择要重新处理其分配的所有用户。

1.  单击“重新处理”。

## <a name="next-steps"></a>后续步骤

- [查看、添加和删除访问包的分配](entitlement-management-access-package-assignments.md)
- [查看报表和日志](entitlement-management-reports.md)
