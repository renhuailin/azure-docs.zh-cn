---
title: 通过 Azure Active Directory B2B 协作转换到受管控的协作
description: 通过 Azure AD B2B 协作转换到受管控的协作。
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
ms.openlocfilehash: 4206ba7617032e34310682d1468e6b1b661b8c8a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648581"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>通过 Azure Active Directory B2B 协作转换到受管控的协作 

使协作受控是确保对资源进行的外部访问安全的关键。 继续阅读本文之前，请确保满足以下条件：

* [已确定你的安全状况](1-secure-access-posture.md)

* [已了解当前状况](2-secure-access-current-state.md)

* [已创建安全计划](3-secure-access-plan.md)

* [已了解组和安全性如何协同工作](4-secure-access-groups.md)

完成这些事项后，就已准备就绪，可以转换到受控协作了。 本文会指导你将所有外部协作转换到 [Azure Active Directory B2B 协作](../external-identities/what-is-b2b.md) (Azure AD B2B)。 Azure AD B2B 是 [Azure AD 外部标识](../external-identities/compare-with-b2c.md)的一项功能。

## <a name="control-who-your-organization-collaborates-with"></a>控制组织的协作方

你必须决定是否对用户可以与哪些组织协作以及组织中的哪些用户可以发起协作进行限制。 大多数组织采取的方法是允许业务单位决定与谁协作，并根据需要委托审批和监督权限。 例如，某些政府版、教育版和金融服务机构不允许开放协作。 你可能希望使用 Azure AD 功能来确定协作范围，如本节的其余部分所述。

### <a name="determine-collaboration-partners"></a>确定协作伙伴

首先，请确保你已记录当前的协作组织，以及这些组织的用户的域。 一个协作伙伴可能有多个域。 例如，一个伙伴可能有多个业务单位，这些业务单位的域各不相同。

接下来，确定是否要启用将来与以下域的协作： 

* 任何域（包容性最高）

* 除了明确拒绝的域之外的所有域 

* 仅特定域（限制性最强）

> [!NOTE]
> 协作设置的限制性越强，用户超出批准的协作框架的可能性越大。 建议你启用安全需求所允许的最广泛的协作，并密切审查该协作，而不必过于严格。 

另请注意，限于单一域可能会无意中阻止与组织进行的已授权协作，这些组织有其他用于用户的不相关域。 例如，如果与 Contoso 组织开展业务，则与 Contoso 的初始联系点可能是电子邮件域为“.com”的美国员工之一。 但是，如果只允许“.com”域，则可能会无意中忽略域为“.ca”的加拿大员工。 

在某些情况下，你希望只允许特定的协作伙伴。 例如，大学系统可能只希望允许自己的教职员工访问资源租户。 又比如，集团可能只希望允许特定的子公司按照所需框架互相协作。

#### <a name="using-allow-and-deny-lists"></a>使用允许列表和拒绝列表

可以使用允许列表或拒绝列表，以便[限制向特定组织中的 B2B 用户发送邀请](../external-identities/allow-deny-list.md)。 只能使用允许列表或拒绝列表，二者不能同时使用。

* [允许列表](../external-identities/allow-deny-list.md)将协作范围限制为仅列出的那些域；所有其他域实际上是在拒绝列表中。

* [拒绝列表](../external-identities/allow-deny-list.md)允许与不在拒绝列表中的任何域协作。

> [!IMPORTANT]
> 这些列表不适用于已在你的目录中的用户。 这些列表也不适用于 OneDrive for Business 和 SharePoint 的允许/拒绝列表，它们是独立的。

某些组织使用一个列表，其中包含托管安全提供程序为这些组织的拒绝列表提供的已知“恶意行动者”域。 例如，如果组织与使用 .com 域的 Contoso 合法开展业务，则可能存在一个不相关的组织，该组织一直在使用 Contoso 的 .org 域并尝试通过网络钓鱼攻击来模拟 Contoso 员工。 

## <a name="control-how-external-users-gain-access"></a>控制外部用户获取访问权限的方式

可以使用 Azure AD B2B 通过多种方式与外部合作伙伴协作。 若要开始协作，可以通过邀请的方式或其他方式让合作伙伴可以访问你的资源。 用户可通过对以下事项进行响应来获取访问权限：

* 兑换[通过电子邮件发送的邀请](../external-identities/redemption-experience.md)或[用于共享某项资源的直接链接](../external-identities/redemption-experience.md)。

* 通过创建 [的应用程序](../external-identities/self-service-sign-up-overview.md) 请求访问

* 通过[我的访问权限](../governance/entitlement-management-request-access.md)门户请求访问权限

启用 Azure AD B2B 后，默认情况下可以通过直接链接和电子邮件邀请来邀请来宾用户。 通过电子邮件 OTP 和自助服务门户进行的邀请目前为预览版，必须在 Azure AD 门户的“外部标识 | 外部协作设置”中启用。

### <a name="control-who-can-invite-guest-users"></a>控制谁可以邀请来宾用户

确定谁可以邀请来宾用户访问资源。

* 最严格的设置是仅允许管理员和那些被授予[来宾邀请者角色](../external-identities/delegate-invitations.md)的用户邀请来宾。

* 如果你的安全要求允许，我们建议你允许 userType 为“Member”的所有用户邀请来宾。

* 确定你是否希望 userType 为“Guest”（Azure AD B2B 用户的默认帐户类型）的用户能够邀请其他来宾。 

![来宾邀请设置的屏幕截图](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>收集有关外部用户的其他信息

如果使用 Azure AD 权利管理，则可配置要求外部用户回答的问题。 然后，会向审批者显示这些问题，让他们做出决定。 你可以为每项[访问包策略](../governance/entitlement-management-access-package-approval-policy.md)配置不同的问题集，以便审批者可以获取其审批的访问权限的相关信息。 例如，如果一个访问包用于供应商访问权限，则可要求请求者提供其供应商合同号。 另一个用于供应商的访问包可以要求供应商提供原国籍。

如果使用自助服务门户，则可以使用 [API 连接器](../external-identities/api-connectors-overview.md) 在用户注册时收集有关用户的其他属性。 然后，你可以使用这些属性来分配访问权限。 例如，如果在注册过程中收集其供应商 ID，则可以使用该属性将其动态分配给该供应商的组或访问包。 可以在 Azure 门户中创建自定义属性，并将它们用于自助注册用户流。 还可以使用 [Microsoft Graph API](../../active-directory-b2c/microsoft-graph-operations.md) 读写这些属性。 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>排查 Azure AD 用户的邀请兑换问题

在下面的三种情况下，通过 Azure AD 从协作伙伴处邀请的来宾用户会存在邀请兑换问题。

* 使用了允许列表，但用户的域未包含在允许列表中。

* 协作伙伴的主租户存在租户限制，导致无法与外部用户协作。

* 用户不在协作伙伴的 Azure AD 租户中。 例如，有 contoso.com 用户只存在于 Active Directory（或其他本地 IdP）中，他们只能通过电子邮件 OTP 过程兑换邀请。 有关详细信息，请参阅[邀请兑换流](../external-identities/redemption-experience.md)。

## <a name="control-what-external-users-can-access"></a>控制外部用户可以访问的内容

大多数组织不是单一的组织。 也就是说，有些资源可以与外部用户共享，有些资源不允许外部用户访问。 因此，必须控制外部用户可以访问哪些内容。 请考虑使用[权利管理和访问包来控制对特定资源的访问权限](6-secure-access-entitlement-managment.md)。

默认情况下，来宾用户可以查看有关租户成员和其他合作伙伴的信息和属性，包括组成员身份。 请考虑你的安全要求是否要求限制外部用户对该信息的访问。

![配置外部协作设置的屏幕截图。](media/secure-external-access/5-external-collaboration-settings.png)

建议对来宾用户进行以下限制。

* 将来宾访问权限限制为浏览目录中的组和其他属性

   * 使用外部协作设置来限制来宾读取他们不属于的组的能力。

* 阻止访问仅限员工访问的应用。

   * 创建条件访问策略，阻止访问那些仅适用于非来宾用户的 Azure AD 集成应用程序。 

* 阻止访问 Azure 门户。可以设置罕见但必要的例外。 

   * 创建一项包含所有来宾和外部用户的条件访问策略，然后[实施一项用于阻止访问的策略](../../role-based-access-control/conditional-access-azure-management.md)。

 

## <a name="remove-users-who-no-longer-need-access"></a>删除不再需要访问权限的用户

评估当前的访问权限，以便[评审和删除不再需要访问权限的用户](../governance/access-reviews-external-users.md)。 包括租户中身份为来宾的外部用户，以及那些有成员帐户的用户。 

某些组织添加了外部用户（如供应商、合作伙伴和承包商）作为成员。 这些成员可能有特定属性或以下列字符开头的用户名，例如：

* v-，代表供应商

* p-，代表合作伙伴

* c-，代表承包商

评估具有成员帐户的任何外部用户，以确定其是否仍然需要访问权限。 如果这些用户仍然需要访问权限，请按下一部分所述将他们转换为 Azure AD B2B 用户。

你可能还有未通过权利管理或 Azure AD B2B 邀请的来宾用户

若要查找这些用户，可执行以下操作：

* [查找未通过权利管理邀请的来宾用户](../governance/access-reviews-external-users.md)。

   * 我们提供了一个[示例 PowerShell 脚本](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)。

按下一部分所述将这些用户转换为 Azure AD B2B 用户。

## <a name="transition-your-current-external-users-to-b2b"></a>将当前外部用户转换为 B2B 用户

如果你一直未使用 Azure AD B2B，那么你的租户中可能有非员工用户。 建议将这些帐户转换为 Azure AD B2B 外部用户帐户，然后将其 UserType 更改为“Guest”。 这样你就可以利用 Azure AD 和 Microsoft 365 允许你使用的多种方法，以不同方式处理外部用户。 其中一些方法包括：

* 在条件访问策略中轻松地包括或排除来宾用户

* 在访问包和访问评审中轻松地包括或排除来宾用户

* 轻松地包括或排除对 Teams、SharePoint 等资源的外部访问权限。

若要在维护这些内部用户的当前访问、UPN 和组成员身份的同时对其进行转换，请参阅 [邀请外部用户使用 B2B 协作](../external-identities/invite-internal-users.md)。

## <a name="decommission-undesired-collaboration-methods"></a>停用不需要的协作方法

若要完成转换到受管控协作，应停用不需要的协作方法 停用哪些方法取决于你希望 IT 部门对协作施加的控制程度，以及你的安全状况。 有关 IT 部门与最终用户控制的信息，请参阅[确定与外部访问相关的安全状况](1-secure-access-posture.md)。

下面是你可能需要评估的协作工具。

### <a name="direct-invitation-through-microsoft-teams"></a>通过 Microsoft Teams 进行直接邀请

默认情况下，Teams 允许外部访问，这意味着组织可以与所有外部域进行通信。 如果只需针对 Teams 限制或允许特定域，可以在 [Teams 管理门户](https://admin.teams.microsoft.com/company-wide-settings/external-communications)中这样做。 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>通过 SharePoint 和 OneDrive 进行直接共享

通过 SharePoint 和 OneDrive 进行直接共享可以在权利管理流程之外添加用户。 若要深入了解这些配置，请参阅[使用 Microsoft Teams、SharePoint 和 OneDrive for Business 管理访问权限](9-secure-access-teams-sharepoint.md)。你也可以根据需要[阻止使用用户的个人 OneDrive](/office365/troubleshoot/group-policy/block-onedrive-use-from-office)。

### <a name="sending-documents-through-email"></a>通过电子邮件发送文档

用户会通过电子邮件将文档发送给外部用户。 考虑如何根据需要使用敏感度标签来限制和加密对这些文档的访问权限。 有关详细信息，请参阅“使用敏感度标签管理访问权限”。

### <a name="unsanctioned-collaboration-tools"></a>未批准的协作工具

协作工具的范围很广。 你的用户可能会使用许多非公务功能，包括 Google 文档、DropBox、Slack 或 Zoom 之类的平台。 对于组织管理的设备，可以在防火墙级别阻止在企业网络中使用此类工具，也可以通过移动应用程序管理来这样做。 但是，这也会阻止这些平台的所有批准的实例，并且不会阻止从非管理的设备进行的访问。 可以根据需要阻止那些你不希望使用的平台，并针对那些需要使用的平台的未批准的使用情况创建业务策略。 

若要详细了解如何管理未批准的应用程序，请参阅：

* [管理已连接的应用](/cloud-app-security/governance-actions)

* [批准和取消批准应用程序](/cloud-app-security/governance-discovery)。

 
### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. [了解当前状况](2-secure-access-current-state.md)

3. [创建治理计划](3-secure-access-plan.md)

4. [使用组进行安全保护](4-secure-access-groups.md)

5. [转换到 Azure AD B2B](5-secure-access-b2b.md)（你在这里。）

6. [通过权利管理实现安全访问](6-secure-access-entitlement-managment.md)

7. [通过条件访问策略实现安全访问](7-secure-access-conditional-access.md)

8. [通过敏感度标签实现安全访问](8-secure-access-sensitivity-labels.md)

9. [实现对 Microsoft Teams、OneDrive 和 SharePoint 的安全访问](9-secure-access-teams-sharepoint.md)