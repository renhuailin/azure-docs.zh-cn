---
title: 使用 Azure Active Directory 权利管理来管理外部访问
description: 如何将 Azure Active Directory 权利管理用作整体外部访问安全计划的一部分。
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
ms.openlocfilehash: ccd033ec224762fef984e98971334ec93bff931f
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113231667"
---
# <a name="manage-external-access-with-entitlement-management"></a>通过权利管理来管理外部访问 


[权利管理](../governance/entitlement-management-overview.md)是一种标识治理功能，通过自动执行访问请求工作流、访问分配、审核和过期，使组织能够大规模管理标识和访问生命周期。 权利管理允许委派的非管理员创建[访问包](../governance/entitlement-management-overview.md)，来自其他组织的外部用户可以请求访问该包。 可配置单阶段和多阶段的审批工作流来评估请求，并通过定期评审为用户[预配](../governance/what-is-provisioning.md)限时访问权限。 权利管理支持基于策略的外部帐户预配和撤销预配。

## <a name="key-concepts-for-enabling-entitlement-management"></a>启用权利管理的关键概念

以下关键概念对于了解权利管理非常重要。

### <a name="access-packages"></a>访问包

[访问包](../governance/entitlement-management-overview.md)是权利管理的基础。 访问包是对策略管控的资源的分组，用户需要这些资源以在项目中协作或执行其他任务。 例如，访问包可能包括：

* 对特定 SharePoint 站点的访问权限。

* 企业应用程序，包括自定义的内部应用和 SaaS 应用（如 Salesforce）。

* Microsoft Teams。

* Microsoft 365 组。 

### <a name="catalogs"></a>目录

访问包位于[目录](../governance/entitlement-management-catalog-create.md)中。 需要将相关的资源和访问包分组并委派管理它们的能力时，可以创建一个目录。 首先将资源添加到目录，然后可将这些资源添加到访问包。 例如，你可能希望创建一个“财务”目录，并[将其管理委派](../governance/entitlement-management-delegate.md)给财务团队的某个成员。 然后，该成员可以[添加资源](../governance/entitlement-management-catalog-create.md)、创建访问包并管理对这些包的访问审批。

下图显示了一个使外部用户能够访问具有过期时间的访问包的典型治理生命周期。

![外部用户治理周期的图示。](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>自助服务外部访问

可通过 [Azure AD“我的访问权限”门户](../governance/entitlement-management-request-access.md)呈现访问包，使外部用户能够请求访问。 策略确定谁可以请求访问包。 你指定允许谁请求访问包：

* 特定的[已连接组织](../governance/entitlement-management-organization.md)

* 所有配置的已连接组织

* 任何组织中的所有用户

* 已在你的租户中的成员或来宾用户

### <a name="approvals"></a>审批   
‎访问包可以包括强制的访问审批。 始终实现外部用户的审批过程。 审批可以是单阶段审批，也可以是多阶段审批。 审批由策略决定。 如果内部用户和外部用户都需要访问同一个包，可能会为不同类别的已连接组织以及内部用户设置不同的访问策略。

### <a name="expiration"></a>过期时间  
‎访问包可以包含到期日期。 可将过期时间设置为具体的某一天，或为用户提供具体的访问天数。 当访问包过期并且用户没有其他访问权限时，可以删除表示用户的 B2B 来宾用户对象或阻止其登录。 对于外部用户，建议对访问包强制实施过期时间。 并非所有访问包都有过期时间。 对于没有过期时间的访问包，请确保执行访问评审。

### <a name="access-reviews"></a>访问评审

访问包可能需要定期[访问评审](../governance/manage-guest-access-with-access-reviews.md)，这需要包所有者或受托人来证明用户的访问需求持续存在。 

在设置评审之前，请确定以下各项内容。

* 谁

   * 继续访问的条件是什么？

   * 谁是指定的审阅者？

* 计划评审的频率如何？

   * 内置选项包括每月、每季度、每两年或每年。 

   * 对于支持外部访问的包，建议每季度一次或更频繁地进行评审。 

 

> [!IMPORTANT]
> 访问包的访问评审仅评审通过权利管理授予的访问权限。 因此，你必须设置其他进程以评审在权利管理之外提供给外部用户的任何访问权限。

有关访问评审的详细信息，请参阅[规划 Azure AD 访问评审部署](../governance/deploy-access-reviews.md)。

## <a name="using-automation-in-entitlement-management"></a>在权利管理中使用自动化

可[使用 Microsoft Graph 执行权利管理功能](/graph/tutorial-access-package-api)，包括

* [管理访问包](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)

* [管理访问评审](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)

* [管理已连接组织](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true)

* [管理权利管理设置](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta&preserve-view=true)

## <a name="recommendations"></a>建议 

建议的做法是使用权利管理控制外部访问。

对于具有一个或多个业务合作伙伴的项目，[创建并使用访问包](../governance/entitlement-management-access-package-create.md)以加入这些合作伙伴的用户，并为其预配对资源的访问权限。 

* 如果目录中已有 B2B 用户，还可以直接向其分配相应的访问包。

* 可通过 [Azure 门户](../governance/entitlement-management-access-package-assignments.md)或 [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta&preserve-view=true) 分配访问权限。

如果用户的访问包过期，请使用标识治理设置从目录中删除这些用户。

![管理外部用户的生命周期的配置屏幕截图。](media/secure-external-access/6-manage-external-lifecycle.png)

这些设置仅适用于通过权利管理加入的用户。

[将目录和访问包的管理](../governance/entitlement-management-delegate.md)委派给业务负责人，他们拥有关于谁具有访问权限的详细信息。

![配置目录的屏幕截图。](media/secure-external-access/6-catalog-management.png)

‎[对外部用户有权访问的访问包强制实施过期时间](../governance/entitlement-management-access-package-lifecycle-policy.md)。


![配置访问包过期时间的屏幕截图。](media/secure-external-access/6-access-package-expiration.png)

* 如果知道基于项目的访问包的结束日期，请使用“过期日期”设置特定日期。 

* 否则，建议过期时间不要超过 365 天，除非已知是多年的约定。

* 允许用户延长访问权限期限。

* 需要审批才能授予延期。

[对包强制实施访问评审](../governance/manage-guest-access-with-access-reviews.md)，以避免来宾的不当访问。

![创建新访问包的屏幕截图。](media/secure-external-access/6-new-access-package.png)

* 强制每季度评审一次。

* 对于合规性敏感的项目，请将审阅者设置为特定审阅者，而不是让外部用户自行评审。 可选择具备访问包管理者身份的用户作为审阅者，这是一个不错的切入点。 

* 对于不太敏感的项目，让用户自行评审可减轻组织的负担，因为组织不再需要删除不再属于本组织的用户的访问权限。

有关详细信息，请参阅[在 Azure AD 权利管理中管理外部用户的访问权限](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. [了解当前状况](2-secure-access-current-state.md)

3. [创建治理计划](3-secure-access-plan.md)

4. [使用组以确保安全性](4-secure-access-groups.md)

5. [转换到 Azure AD B2B](5-secure-access-b2b.md)

6. [通过权利管理实现安全访问](6-secure-access-entitlement-managment.md)（本文。）

7. [通过条件访问策略实现安全访问](7-secure-access-conditional-access.md)

8. [通过敏感度标签实现安全访问](8-secure-access-sensitivity-labels.md)

9. [实现对 Microsoft Teams、OneDrive 和 SharePoint 的安全访问](9-secure-access-teams-sharepoint.md)

 

