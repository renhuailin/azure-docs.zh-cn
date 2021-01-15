---
title: 使用 Azure Active Directory 的权利管理管理外部访问
description: 如何使用 Azure Active Directory 的权利管理作为整体外部访问安全计划的一部分。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9de0ce5e83b33cd793953e2b863f26dffafe58ee
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222388"
---
# <a name="manage-external-access-with-entitlement-management"></a>使用权利管理管理外部访问 


授权[管理](../governance/entitlement-management-overview.md)是一种标识调控功能，使组织能够通过自动执行访问请求工作流、访问分配、审核和过期来大规模管理标识和访问生命周期。 通过授权管理，委派的非管理员可以创建 [访问包](../governance/entitlement-management-overview.md) ，其他组织的外部用户可以请求访问这些包。 可以将一个和多个阶段的审批工作流配置为评估请求，并为用户 [预配](../governance/what-is-provisioning.md) 定期检查的时间限制的访问权限。 权利管理启用对外部帐户的基于策略的设置和取消预配。

## <a name="key-concepts-for-enabling-entitlement-management"></a>启用权利管理的关键概念

以下关键概念对于了解权限管理非常重要。

### <a name="access-packages"></a>访问包

[访问包](../governance/entitlement-management-overview.md)是授权管理的基础。 访问包是用户需要对项目进行协作或执行其他任务的策略控制资源的分组。 例如，访问包可能包括：

* 对特定 SharePoint 站点的访问权限。

* 企业应用程序，包括自定义的内部应用和 SaaS 应用，如 Salesforce。

* Microsoft 团队渠道。

* Microsoft 365 组。 

### <a name="catalogs"></a>目录

访问包驻留在 [目录](../governance/entitlement-management-catalog-create.md)中。 当你要将相关资源分组并访问包和委派管理这些资源的能力时，可以创建目录。 首先，将资源添加到目录，然后可以添加这些资源来访问包。 例如，你可能想要创建一个 "财务" 目录，并将 [其管理委托](../governance/entitlement-management-delegate.md) 给财务团队的成员。 然后，该用户可以 [添加资源](../governance/entitlement-management-catalog-create.md)、创建访问包和管理对这些包的访问权限。

下图显示了一个典型的管理生命周期，使外部用户能够访问具有过期的访问包。

![外部用户调控周期的关系图。](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>自助外部访问

你可以通过 [Azure AD 我的 Access 门户](../governance/entitlement-management-request-access.md) 来查看访问包，使外部用户能够请求访问。 策略确定谁可以请求访问包。 指定允许谁请求访问包：

* 特定 [连接的组织](../governance/entitlement-management-organization.md)

* 所有已配置的已连接组织

* 任何组织中的所有用户

* 已在你的租户中的成员或来宾用户

### <a name="approvals"></a>审批   
访问包可以包括必需的访问审批。 **始终为外部用户实现审批过程**。 审批可以是单个或多个阶段批准。 审批由策略决定。 如果内部用户和外部用户都需要访问同一包，可能会为不同类别的已连接组织以及内部用户设置不同的访问策略。

### <a name="expiration"></a>过期时间  
访问包可以包含到期日期。 可以将过期时间设置为特定的一天，或为用户提供特定的访问天数。 当访问包过期并且用户没有其他访问权限时，可以删除或阻止表示用户的 B2B 来宾用户对象登录。 建议为外部用户强制访问包过期。 并非所有访问包都有过期时间。 对于不是这样的情况，请确保执行访问评审。

### <a name="access-reviews"></a>访问评审

访问包可能需要定期 [访问评审](../governance/manage-guest-access-with-access-reviews.md)，这需要包所有者或 designee 来证明用户访问的持续需要。 

在设置评审之前，请确定以下各项。

* 谁

   * 继续访问的标准是什么？

   * 谁是指定的审阅者？

* 计划评审的频率是多少？

   * 内置选项包括每月、每季度、每两年或每年。 

   * 建议为支持外部访问的包提供季度或更多的频率。 

 

> [!IMPORTANT]
> 访问权限包的访问评审仅查看通过授权管理授予的访问权限。 因此，你必须设置其他进程以查看为权限管理外部的外部用户提供的任何访问权限。

有关访问评审的详细信息，请参阅 [计划 Azure AD 访问评审部署](../governance/deploy-access-reviews.md)。

## <a name="using-automation-in-entitlement-management"></a>在权利管理中使用自动化

您可以 [通过使用 Microsoft Graph 来执行权利管理功能](https://docs.microsoft.com/graph/tutorial-access-package-api)，包括

* [管理访问包](https://docs.microsoft.com/graph/api/resources/accesspackage?view=graph-rest-beta)

* [管理访问评审](https://docs.microsoft.com/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [管理连接的组织](https://docs.microsoft.com/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [管理授权管理设置](https://docs.microsoft.com/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>建议 

建议使用权利管理来控制外部访问。

**对于具有一个或多个业务合作伙伴的项目， [创建和使用访问包](../governance/entitlement-management-access-package-create.md) 以加入并设置这些合作伙伴的用户对资源的访问权限**。 

* 如果目录中已有 B2B 用户，还可以直接将其分配给相应的访问包。

* 可以通过 [Azure 门户](../governance/entitlement-management-access-package-assignments.md)或 [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta)分配访问权限。

**在访问包过期时，使用标识调控设置将用户从目录中删除**。

![配置管理外部用户的生命周期的屏幕截图。](media/secure-external-access/6-manage-external-lifecycle.png)

这些设置仅适用于通过授权管理载入的用户。

将 **[目录和访问包的管理委托](../governance/entitlement-management-delegate.md)给企业所有者，这些所有者具有有关应访问的人员的详细信息**。

![配置目录的屏幕截图。](media/secure-external-access/6-catalog-management.png)

**强制外部用户有权访问的 [访问包过期](../governance/entitlement-management-access-package-lifecycle-policy.md) 。**


![配置访问包过期的屏幕截图。](media/secure-external-access/6-access-package-expiration.png)

* 如果知道基于项目的访问包的结束日期，请使用 "开始日期" 设置特定日期。 

* 否则，建议不要再365天，除非它被称为多年活动。

* 允许用户扩展访问权限。

* 需要批准才能授予扩展。

**[强制对包进行访问评审](../governance/manage-guest-access-with-access-reviews.md) ，以避免对来宾的不适当的访问。**

![创建新访问包的屏幕截图。](media/secure-external-access/6-new-access-package.png)

* 每季度强制评审。

* 对于符合要求的项目，请将审阅者设置为特定审阅者，而不是对外部用户进行自我检查。 对于审阅者来说，访问包管理器的用户是一个不错的开端。 

* 对于不太敏感的项目，让用户自行查看会降低组织删除不再与本组织的用户的访问权限。

有关详细信息，请参阅对 [外部用户的访问权限 Azure AD 权限管理](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出的顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. [发现当前状态](2-secure-access-current-state.md)

3. [创建调控计划](3-secure-access-plan.md)

4. [使用组进行安全保护](4-secure-access-groups.md)

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [使用权利管理 (安全访问](6-secure-access-entitlement-managment.md) 。 ) 

7. [使用条件性访问策略的安全访问](7-secure-access-conditional-access.md)

8. [使用敏感度标签进行安全访问](8-secure-access-sensitivity-labels.md)

9. [安全访问 Microsoft 团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)

 

 
