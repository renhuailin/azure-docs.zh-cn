---
title: 完成对组和应用程序的访问评审 - Azure AD
description: 了解如何在 Azure Active Directory 访问评审中完成对组成员或应用程序访问的访问评审。
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 02/08/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e31b9ce115ebe0723a9d082aaddd8056e486ed27
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176971"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中完成对组和应用程序的访问评审

你以管理员身份[创建对组或应用程序的访问评审](create-access-review.md)，审阅者[执行访问评审](perform-access-review.md)。 本文介绍如何查看访问评审的结果并应用结果。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>必备条件

- Azure AD Premium P2
- 全局管理员、用户管理员、安全管理员或安全读取者

有关详细信息，请参阅[许可证要求](access-reviews-overview.md#license-requirements)。

## <a name="view-an-access-review"></a>查看访问评审

审阅者完成评审时，你可以跟踪进度。

1. 登录到 Azure 门户并打开[“标识治理”页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)。

1. 在左侧菜单中，单击“访问评审”。 

1. 在列表中，单击“访问评审”。

    若要查看访问评审的未来实例，请导航到访问评审，然后选择计划的评审。

    在 " **概述** " 页上，可以查看当前实例的进度。 在评审完成之前，目录中的任何访问权限都不会更改。

     ![查看所有公司组](./media/complete-access-review/all-company-group.png)

    当前下的所有边栏选项卡仅在每个评审实例的持续时间内可查看。 

    "结果" 页提供了有关在实例中评审的每个用户的详细信息，包括停止、重置和下载结果的能力。

    ![查看跨 Microsoft 365 组的来宾访问权限](./media/complete-access-review/all-company-group-results.png)


    如果你查看的是查看跨 Microsoft 365 组的来宾访问权限的访问评审，则 "概述" 边栏选项卡会列出评审中的每个组。 
   
    ![跨 Microsoft 365 组的评审来宾访问](./media/complete-access-review/review-guest-access-across-365-groups.png)

    单击某个组可以查看该组的审核进度，以及停止、重置、应用和删除。

   ![跨 Microsoft 365 组的评审来宾访问详细信息](./media/complete-access-review/progress-group-review.png)

1. 如果要在某个访问评审达到计划的结束日期之前停止它，请单击“停止”按钮。

    当你停止审阅时，审阅者将无法再给出响应。 停止后将无法重新开始评审。

1. 如果不再关注此访问评审，可以单击“删除”按钮将其删除。

## <a name="apply-the-changes"></a>应用更改

如果基于您在 **完成设置** 中所做的选择启用了 "**自动应用结果**"，则将在评审结束日期之后或在您手动停止审阅时执行自动应用。

如果没有为审核启用 "**自动应用结果**" 功能，请在评审持续时间结束后导航到 "**系列**" 下的 "**查看历史记录**"，或在早期停止评审后，单击要应用的评审的实例。

![应用访问评审更改](./media/complete-access-review/apply-changes.png)

单击 " **应用** " 以手动应用更改。 如果在评审中拒绝了某个用户的访问权限，则当你单击“应用”时，Azure AD 会删除该用户的成员资格或应用程序分配。

![应用访问评审更改按钮](./media/complete-access-review/apply-changes-button.png)


评审状态将从“已完成”变为各种中间状态（例如“正在应用”），并最终变为“结果已应用”状态。 几分钟后，应会看到被拒绝的用户（如果有）已从组成员身份或应用程序分配中删除。

手动或自动应用结果不会影响源自本地目录或动态组的组。 若要更改源自本地的组，请下载结果，并将这些更改应用到该目录中组的表示形式。

## <a name="retrieve-the-results"></a>检索结果

若要查看一次性访问评审的结果，请单击“结果”页。 若只查看某个用户的访问，请在“搜索”框中键入其访问已进行评审的用户的显示名称或用户主体名称。

![检索访问评审结果](./media/complete-access-review/retrieve-results.png) 

若要查看定期进行的活动访问评审的进度，请单击“结果”页面。

若要查看已完成的定期访问评审实例的结果，请单击“评审历史记录”，然后根据实例的开始日期和结束日期，从已完成的访问评审实例列表中选择特定实例。 该实例的结果可以从“结果”页面获得。

若要检索某个访问评审的所有结果，请单击“下载”按钮。 可以在 Excel 中或在可打开 UTF-8 编码 CSV 文件的其他程序中查看生成的 CSV 文件。

## <a name="remove-users-from-an-access-review"></a>从访问评审中删除用户

 默认情况下，删除的用户将在 Azure AD 中保持删除状态 30 天，在此期间，管理员可以根据需要还原这些用户。  30 天后，该用户将被永久删除。  此外，使用 Azure Active Directory 门户，全局管理员可以在达到该时间段之前，显式地[永久删除最近删除的用户](../fundamentals/active-directory-users-restore.md)。  某个用户被永久删除后，随后有关该用户的数据将从活动访问评审中删除。  有关已删除用户的审核信息仍保留在审核日志中。

## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 访问评审管理用户访问权限](manage-user-access-with-access-reviews.md)
- [使用 Azure AD 访问评审管理来宾访问权限](manage-guest-access-with-access-reviews.md)
- [创建组或应用程序的访问评审](create-access-review.md)
- [针对充当 Azure AD 管理角色的用户创建访问评审](../privileged-identity-management/pim-how-to-start-security-review.md)
