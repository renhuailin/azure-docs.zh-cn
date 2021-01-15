---
title: 通过 Azure Active Directory 发现外部协作的当前状态
description: 了解用于发现协作当前状态的方法。
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
ms.openlocfilehash: 7557985b23991f1a53d45f6f2d2283500c0d73f3
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222405"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>发现组织中外部协作的当前状态 

在发现外部协作的当前状态之前，应 [确定所需的安全状况](1-secure-access-posture.md)。 你将认为你的组织需要集中式与委托控制，以及任何相关的监管、法规和符合性目标。 

你的组织中的个人可能已经与其他组织的用户合作。 协作可以通过工作效率应用程序（例如 Microsoft 365、电子邮件）的功能，或通过其他方式与外部用户共享资源。 你的监管计划的支柱将在你发现 
*   启动外部协作的用户。
*   要与之协作的外部用户和组织。
*   要授予外部用户的访问权限。


## <a name="users-initiating-external-collaboration"></a>启动外部协作的用户

发起外部协作的用户最好了解最适用于外部协作的应用程序，以及何时应结束访问。 了解这些用户可以帮助你确定谁应委派邀请外部用户、创建访问包和完成访问评审的权限。

若要查找当前正在协作的用户，请查看 [Microsoft 365 审核日志以了解共享和访问请求活动](https://docs.microsoft.com/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance?view=o365-worldwide#sharing-and-access-request-activities)。 你还可以查看 [Azure AD 审核日志，以获取有关将 B2B 用户邀请到你的目录的人员的详细信息](../external-identities/auditing-and-reporting.md) 。

## <a name="find-current-collaboration-partners"></a>查找当前协作伙伴

外部用户可能是 [AZURE AD B2B 用户](../external-identities/what-is-b2b.md) (首选) 与合作伙伴管理的凭据或具有本地预配凭据的外部用户。 这些用户通常 (，但并不总是) 使用来宾的 UserType 进行标记。 可以通过 [MICROSOFT GRAPH API](https://docs.microsoft.com/graph/api/user-list?view=graph-rest-1.0&tabs=http)、 [PowerShell](https://docs.microsoft.com/graph/api/user-list?view=graph-rest-1.0&tabs=http)或 [Azure 门户](../enterprise-users/users-bulk-download.md)枚举来宾用户。

### <a name="use-email-domains-and-companyname-property"></a>使用电子邮件域和公司名称属性

外部组织可以通过外部用户电子邮件地址的域名来确定。 如果支持使用者标识提供者（如 Google），则可能无法做到这一点。 在这种情况下，我们建议你编写 "公司名称" 属性，以便清楚地识别用户的外部组织。

### <a name="use-allow-or-deny-lists"></a>使用允许或拒绝列表

考虑你的组织是否希望只允许与特定组织进行协作。 或者，如果你的组织想要阻止与特定组织的协作。  在租户级别，有一个 [允许或拒绝列表](../external-identities/allow-deny-list.md)，无论 (例如团队、SharePoint 和 Azure 门户) ，都可以使用该列表来控制总体 B2B 邀请和兑换次数。
如果你使用的是权利管理，则还可以使用特定的 "已连接组织" 设置将访问包的范围限定为你的合作伙伴的子集，如下所示。


![创建新访问包时允许拒绝列表的屏幕截图。](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>查找要授予外部用户的访问权限

当你有外部用户和组织的清单后，你可以使用 Microsoft Graph API 确定授予这些用户的访问权限，以确定 Azure AD 中 Azure AD [组成员身份](https://docs.microsoft.com/graph/api/resources/groups-overview?view=graph-rest-1.0) 或 [直接应用程序分配](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0) 。


### <a name="enumerate-application-specific-permissions"></a>枚举应用程序特定的权限

您还可以执行应用程序特定的权限枚举。 例如，你可以使用 [此脚本](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64)以编程方式生成 SharePoint Online 权限报表。

专门调查对所有业务敏感应用程序和业务关键应用程序的访问权限，以便完全了解所有外部访问权限。

### <a name="detect-ad-hoc-sharing"></a>检测即席共享
如果你的电子邮件和网络计划启用了此服务，则可以通过电子邮件或通过未经授权的软件即服务 (SaaS) 应用来调查共享的内容。 [Microsoft 365 数据丢失保护](https://docs.microsoft.com/microsoft-365/compliance/data-loss-prevention-policies?view=o365-worldwide) 有助于识别、阻止和监视跨 Microsoft 365 基础结构的敏感信息的意外共享。 [Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) 可以帮助你确定在你的环境中使用未经授权的 SaaS 应用。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出的顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. 在此处[发现当前状态](2-secure-access-current-state.md) (。 ) 

3. [创建调控计划](3-secure-access-plan.md)

4. [使用组进行安全保护](4-secure-access-groups.md)

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [使用权限管理的安全访问](6-secure-access-entitlement-managment.md)

7. [使用条件性访问策略的安全访问](7-secure-access-conditional-access.md)

8. [使用敏感度标签进行安全访问](8-secure-access-sensitivity-labels.md)

9. [安全访问 Microsoft 团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)
