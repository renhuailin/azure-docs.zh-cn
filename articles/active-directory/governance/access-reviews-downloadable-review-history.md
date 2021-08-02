---
title: 创建并管理可下载的访问评审历史记录报表（预览）- Azure Active Directory
description: 使用 Azure Active Directory 访问评审，可下载组织中访问评审的评审历史记录。
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/25/2021
ms.author: ajburnle
ms.openlocfilehash: 6f0d9b92b9aa3bb48533d5270c0fcd31aa8df82c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025670"
---
# <a name="create-and-manage-downloadable-access-review-history-report-preview-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中创建并管理可下载的访问评审历史记录报表（预览版）

通过 Azure Active Directory (Azure AD) 访问评审，可以创建一个可下载的评审历史记录，以帮助组织获得更多见解。 报表将拉取在创建报表时由评审者作出的决策。 这些报表可以构造为包括特定期限内的特定访问评审，并可经过筛选以包含不同的评审类型和评审结果。
 
## <a name="who-can-access-and-request-review-history"></a>谁可以访问和请求评审历史记录

有权查看访问评审的任何用户都可查看历史记录和请求评审历史记录。 若要查看哪些角色可以查看和创建访问评审，请参阅[可以评审哪些资源类型？](deploy-access-reviews.md#what-resource-types-can-be-reviewed)。 全局管理员和全局读者可以查看所有访问评审。 所有其他用户仅可查看自己生成的访问评审的报表。

## <a name="how-to-create-a-review-history-report"></a>如何创建评审历史记录报表

必备角色：有权查看访问评审的所有用户

1. 在 Azure 门户中，依次选择“Azure Active Directory”和“标识治理” 。

1. 在左侧菜单中的“访问评审”下，选择“评审历史记录” 。
 
1. 选择“新建报表”。 

1. 指定评审的开始日期和结束日期。

1. 选择要包括在报表中的评审类型和评审结果。 

    ![访问评审 - 访问评审历史记录报表 - 创建](./media/access-reviews-downloadable-review-history/create-review-history.png)

1. 然后选择“创建”以创建访问评审历史记录报表。

## <a name="how-to-download-review-history-reports"></a>如何下载评审历史记录报表

创建评审历史记录报表后，可以下载它。 创建的所有报表都可以在 30 天内以 CSV 格式下载。

1. 选择“标识治理”下的“评审历史记录” 。 创建的所有评审历史记录报表都将可用。 
1. 选择要下载的报表。 

## <a name="what-is-included-in-a-review-history-report"></a>评审历史记录报表中包含哪些内容？

报表提供有关每个用户的详细信息，其中包含以下内容：

| 元素名称 | 说明 |
| --- | --- |
| AccessReviewId |  评审对象 ID |
| ReviewType | 评审类型包括组、应用程序、Azure AD 角色、Azure 角色和访问包|
|ResourceDisplayName | 评审的资源的显示名称 |
| ResourceId | 评审的资源的 ID |
| ReviewName |  评审名称 |
| CreatedDateTime | 评审的创建日期/时间 |
| ReviewStartDate | 评审的开始日期
| ReviewEndDate | 评审的结束日期 |
| ReviewStatus | 评审的状态。 有关所有评审状态，请参阅[此处](create-access-review.md)的访问评审状态表 |
| OwnerId | 审阅者所有者 ID |
| OwnerName | 审阅者所有者姓名 |
| OwnerUPN | 审阅者所有者用户主体名称 |
| PrincipalId | 评审的主体的 ID |
| 主体名称 | 评审的主体的名称 |
| PrincipalUPN | 评审的用户的主体名称 |
| PrincipalType | 主体的类型。 选项包括用户、组和服务主体 |
| ReviewDate | 评审的日期/时间 |
| ReviewResult | 评审结果包括“拒绝”、“批准”和“未评审” |
|理由 | 审阅者提供评审结果的理由 |
| ReviewerId | 审阅者 ID |
| ReviewerName | 审阅者姓名 |
| ReviewerUPN | 审阅者用户主体名称 |
| ReviewerEmailAddress | 审阅者电子邮件地址 |
| AppliedByName | 应用评审结果的用户的姓名 |
| AppliedByUPN | 应用评审结果的用户的用户主体名称|
| AppliedByEmailAddress | 应用评审结果的用户的电子邮件地址 |
| AppliedDate | 应用评审结果的日期 |
| AccessRecommendation | 系统建议包括“批准”、“拒绝”和“无信息” |
|SubmissionResult | 评审结果提交状态包括“已应用”和“未应用”。 |

## <a name="next-steps"></a>后续步骤
- [评审组或应用程序的访问权限](perform-access-review.md)
- [评审自己对组或应用程序的访问权限](review-your-access.md)
- [完成组或应用程序的访问评审](complete-access-review.md)
