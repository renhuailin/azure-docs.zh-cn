---
title: 通过 Azure Active Directory 发现外部协作的当前状态
description: 了解用于发现协作的当前状态的方法。
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
ms.openlocfilehash: 30858e9978f7e8857c5f8a2dcdfd7455f6e97b60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553419"
---
# <a name="discover-the-current-state-of-external-collaboration-in-your-organization"></a>发现组织中外部协作的当前状态 

在发现外部协作的当前状态之前，应[确定所需的安全状况](1-secure-access-posture.md)。 你将考虑组织对集中控制与委托控制的需求，以及任何相关治理、法规和合规性目标。 

组织中的个人可能正在与其他组织的用户协作。 协作可通过以下方式实现：使用 Microsoft 365 等生产力应用程序中的功能、使用电子邮件或与外部用户共享资源。 正如你所发现的，治理计划的要素将形成 
*   发起外部协作的用户。
*   你要与之协作的外部用户和组织。
*   要授予外部用户的访问权限。


## <a name="users-initiating-external-collaboration"></a>发起外部协作的用户

发起外部协作的用户最了解与外部协作最相关的应用程序以及应何时终止访问。 了解这些用户可以帮助确定应该向谁委派权限以邀请外部用户、创建访问包并完成访问评审。

若要查找当前正在协作的用户，请查看[用于共享和访问请求活动的Microsoft 365 审核日志](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#sharing-and-access-request-activities)。 还可以查看 [Azure AD 审核日志以详细了解谁邀请了 B2B](../external-identities/auditing-and-reporting.md) 用户访问你的目录。

## <a name="find-current-collaboration-partners"></a>查找当前的协作合作伙伴

外部用户可以是具有合作伙伴管理的凭据的 [Azure AD B2B 用户](../external-identities/what-is-b2b.md)（首选），也可以是具有本地预配凭据的外部用户。 通常（但不总是）使用 Guest 的 UserType 标记这些用户。 可以通过 [Microsoft Graph API](/graph/api/user-list?tabs=http)、[PowerShell](/graph/api/user-list?tabs=http) 或 [Azure 门户](../enterprise-users/users-bulk-download.md)来枚举来宾用户。

### <a name="use-email-domains-and-companyname-property"></a>使用电子邮件域和 companyName 属性

可以通过外部用户电子邮件地址的域名来确定外部组织。 如果支持使用者标识提供者（例如，Google），则可能无法实现这一点。 对于这种情况，我们建议编写 companyName 属性，以清楚地标识用户的外部组织。

### <a name="use-allow-or-deny-lists"></a>使用允许或拒绝列表

考虑你的组织是否希望仅允许与特定组织进行协作。 或者，考虑你的组织是否希望阻止与特定组织进行协作。  在租户级别，有一个[允许或拒绝列表](../external-identities/allow-deny-list.md)，可用于控制总体 B2B 邀请和兑换次数，而不考虑来源（如 Teams、SharePoint 和 Azure 门户）。
如果你使用的是权利管理，则还可以使用“特定的连接组织”设置将访问包的范围限定为你的合作伙伴的子集，如下所示。


![创建新访问包时允许/拒绝列表的屏幕截图。](media/secure-external-access/2-new-access-package.png)


## <a name="find-access-being-granted-to-external-users"></a>查找要授予外部用户的访问权限

拥有外部用户和组织的清单之后，可以使用 Microsoft Graph API 确定要授予这些用户的访问权限，以确定 Azure AD 中的 Azure AD [组成员身份](/graph/api/resources/groups-overview)或[直接应用程序分配](/graph/api/resources/approleassignment)。


### <a name="enumerate-application-specific-permissions"></a>枚举应用程序特定的权限

还可以执行应用程序特定的权限枚举。 例如，可以通过使用[此脚本](https://gallery.technet.microsoft.com/office/SharePoint-Online-c9ec4f64)以编程方式生成 SharePoint Online 权限报表。

专门调查对所有业务敏感型应用和业务关键型应用的访问权限，以完全了解所有外部访问权限。

### <a name="detect-ad-hoc-sharing"></a>检测临时共享
如果电子邮件和网络计划启用此功能，则可以通过电子邮件或未经授权的软件即服务 (SaaS) 应用来调查共享的内容。 [Microsoft 365 数据丢失防护](/microsoft-365/compliance/data-loss-prevention-policies)有助于确定、阻止和监视 Microsoft 365 基础结构中的敏感信息的意外共享。 [Microsoft Cloud App Security](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/cloud-app-security) 有助于确定环境中对未经授权的 SaaS 应用的使用。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. [了解当前状态](2-secure-access-current-state.md)（你的当前状况。）

3. [创建治理计划](3-secure-access-plan.md)

4. [使用组以确保安全性](4-secure-access-groups.md)

5. [转换到 Azure AD B2B](5-secure-access-b2b.md)

6. [通过权利管理实现安全访问](6-secure-access-entitlement-managment.md)

7. [通过条件访问策略实现安全访问](7-secure-access-conditional-access.md)

8. [通过敏感度标签实现安全访问](8-secure-access-sensitivity-labels.md)

9. [实现对 Microsoft Teams、OneDrive 和 SharePoint 的安全访问](9-secure-access-teams-sharepoint.md)