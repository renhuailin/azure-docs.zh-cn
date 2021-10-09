---
title: 在 Azure AD 权利管理中评审访问包的访问权限
description: 了解如何在 Azure Active Directory 访问评审中完成权利管理访问包的访问评审。
services: active-directory
documentationCenter: ''
author: amsliu
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/15/2021
ms.author: amsliu
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b86ddd01b155b54eaa954df2a67df907901b288
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639672"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权利管理中评审访问包的访问权限

Azure AD 权利管理简化了企业管理组、应用程序和 SharePoint 站点访问权限的方式。 本文介绍如何针对作为指定审阅者分配到访问包的其他用户执行访问评审。

## <a name="prerequisites"></a>必备条件

要查看用户的活动访问包分配，评审创建者必须满足以下先决条件：
- Azure AD Premium P2
- 全局管理员、标识治理管理员或用户管理员

有关详细信息，请参阅[许可证要求](entitlement-management-overview.md#license-requirements)。

>[!NOTE]
>审阅者可以是评审创建者选择的任何人（组所有者、用户管理员、用户自己或任何选定的用户或组）。


## <a name="open-the-access-review"></a>打开访问评审

请执行以下步骤找到并打开访问评审：

1. 你可能会从 Microsoft 收到一封要求你进行访问评审的电子邮件。 请找到该电子邮件，打开访问评审。 下面是用于评审访问权限的示例电子邮件：
    
    ![“访问评审”审阅者电子邮件](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. 单击“评审用户访问权限”链接，打开访问评审。 

1. 如果未收到该电子邮件，可通过直接导航到 https://myaccess.microsoft.com 查找待处理的访问评审。  （对于 US Government 版，请改用 `https://myaccess.microsoft.us`。）

1. 单击左侧导航栏上的“访问评审”，查看分配给你的待处理访问评审的列表。
    
    ![在“我的访问权限”上选择“访问评审”](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. 单击要开始的评审。
    
    ![选择访问评审](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>执行访问评审

打开访问评审后，你将看到需要评审的用户的名称。 可通过两种方式批准或拒绝访问权限：
- 可以手动批准或拒绝一个或多个用户的访问权限
- 可以接受系统建议

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>手动批准或拒绝一个或多个用户的访问权限
1. 审阅用户的列表，并确定哪些用户需要继续拥有访问权限。

    ![要审阅的用户的列表](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. 若要批准或拒绝访问权限，请选择用户名称左侧的单选按钮。

1. 在用户名上方的栏中选择“批准”或“拒绝”。

    ![选择用户](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. 如果不确定，可以单击“不知道”按钮。

    如果做出这一选择，则用户将保留访问权限，并且此选择将记录在审核日志中。 该日志会向任何其他审阅者显示你仍完成了评审。

1. 你可能需要提供你做出决策的原因。 请键入一个原因，然后单击“提交”。

    ![批准或拒绝访问权限](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. 你可以在评审结束之前随时更改你的决策。 为此，请从列表中选择用户并更改决策。 例如，可以为以前已拒绝的用户批准访问权限。

如果有多个评审者，将记录最后提交的响应。 举例而言，假设管理员指定了两位评审者 – Alice 和 Bob。 Alice 首先打开该评审并批准了访问权限。 在评审结束之前，Bob 打开该评审并拒绝了访问权限。 在这种情况下，将记录上次的拒绝访问决策。

>[!NOTE]
>如果某个用户在评审中被拒绝访问，该用户不会立即被从访问包中删除。 一旦在评审关闭后应用评审结果，该用户将立即被从访问包中删除。 评审将在评审持续时间结束时自动关闭，如果管理员手动停止评审，则会提前关闭。 

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>使用系统生成的建议批准或拒绝访问权限

若要更快地评审多个用户的访问权限，可以使用系统生成的建议，只需单击一下即可接受建议。 建议是根据用户的登录活动生成的。

1.  在页面底部的栏中，单击“接受建议”。
    
    ![选择“接受建议”](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    你将看到建议操作的摘要。

1.  单击“提交”接受建议。

## <a name="next-steps"></a>后续步骤

- [自我评审访问包](entitlement-management-access-reviews-self-review.md)
