---
title: Azure Active Directory B2B 协作过渡到管控协作
description: 利用 Azure Ad B2B 协作，迁移到共同协作。
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
ms.openlocfilehash: 2c0d63df3e5525b200723db209d05e5eb5013e23
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222354"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Azure Active Directory B2B 协作过渡到管控协作 

在控制下实现协作是保护对资源的外部访问权限的关键所在。 在继续本文之前，请确保：

* [确定了您的安全状况](1-secure-access-posture.md)

* [已发现当前状态](2-secure-access-current-state.md)

* [创建安全计划](3-secure-access-plan.md)

* [了解组和安全性如何协同工作](4-secure-access-groups.md)

完成这些操作后，就可以迁移到受控协作。 本文将指导你将所有外部协作迁移到 [AZURE ACTIVE DIRECTORY b2b 协作](../external-identities/what-is-b2b.md) (Azure AD b2b) 。 Azure Ad B2B 是 [Azure AD 外部标识](../external-identities/compare-with-b2c.md)的一项功能。

## <a name="control-who-your-organization-collaborates-with"></a>控制组织的协作

你必须决定是限制你的用户可以与哪些组织进行协作，并且组织中的谁可以发起协作。 大多数组织采取一种方法，即允许业务单位决定他们的协作，并根据需要委派批准和监督。 例如，某些政府版、教育版和金融服务机构不允许开放协作。 你可能希望使用 Azure AD 功能来确定协作范围，如本部分的其余部分所述。

### <a name="determine-collaboration-partners"></a>确定协作伙伴

首先，请确保已记录当前与之合作的组织，以及这些组织的用户的域。 一个协作合作伙伴可能有多个域。 例如，一个合作伙伴可能有多个具有不同域的业务单位。

接下来，确定是否要启用将来的协作 

* 任何域 (最含) 

* 所有域（明确拒绝的域除外） 

* 只有特定域 (最严格的) 

> [!NOTE]
> 协作设置的限制更强，用户的可能性就越大。 建议启用您的安全需求所允许的最广泛的协作，并密切审查这一协作，而不是过于严格。 

另请注意，限制为单一域可能会无意中阻止与组织进行授权协作，这些组织对其用户具有其他不相关的域。 例如，如果与组织 Contoso 开展业务，则与 Contoso 的初始联系点可能是其电子邮件中包含 ".com" 域的一名基于美国的员工。 但是，如果只允许 ".com" 域，可能会无意中忽略其 "ca" 域的加拿大员工。 

在某些情况下，你只希望允许特定的协作伙伴。 例如，大学系统可能只希望允许自己的教职员访问资源租户。 或者，集团可能只希望允许特定的子公司彼此合作，以实现与所需框架的符合性。

#### <a name="using-allow-and-deny-lists"></a>使用允许列表和拒绝列表

你可以使用允许列表或拒绝列表限制特定组织中的 [B2B 用户邀请](../external-identities/allow-deny-list.md) 。 您只能使用允许或拒绝列表，而不能同时使用两者。

* [允许列表](../external-identities/allow-deny-list.md)限制仅协作到列出的域;所有其他域在拒绝列表中都有效。

* [拒绝列表](../external-identities/allow-deny-list.md)允许与不在拒绝列表中的任何域进行协作。

> [!IMPORTANT]
> 这些列表不适用于已在你的目录中的用户。 它们也不适用于独立的 OneDrive for business 和 SharePoint 允许拒绝列表。

某些组织使用其托管安全提供程序为其拒绝列表提供的已知 "错误参与者" 域的列表。 例如，如果组织使用 Contoso 和 .com 域合法开展业务，则可能存在一个不相关的组织，其中使用了 Contoso. org 域，并尝试通过网络钓鱼攻击来模拟 Contoso 员工。 

## <a name="control-how-external-users-gain-access"></a>控制外部用户如何获取访问权限

可以通过多种方式使用 Azure AD B2B 与外部合作伙伴协作。 若要开始协作，可以邀请或以其他方式允许合作伙伴访问资源。 用户可以通过以下方法获取访问权限：

* 兑换 [通过电子邮件发送的邀请](../external-identities/redemption-experience.md)或共享资源的 [直接链接](../external-identities/redemption-experience.md) 。

* 通过创建 [的应用程序](../external-identities/self-service-sign-up-overview.md) 请求访问

* 通过 "我的 [访问](../governance/entitlement-management-request-access.md) 门户" 请求访问

启用 Azure AD B2B 后，便可以在默认情况下通过直接链接和电子邮件邀请邀请来宾用户。 通过电子邮件 OTP 和自助服务门户的邀请目前处于预览状态，并且必须在外部标识中启用 |Azure AD 门户中的外部协作设置。

### <a name="control-who-can-invite-guest-users"></a>控制谁可以邀请来宾用户

确定谁可以邀请来宾用户访问资源。

* 最严格的设置是仅允许管理员和这些用户授予 [来宾邀请者角色](../external-identities/delegate-invitations.md) 来邀请来宾。

* 如果你的安全要求允许，则建议你允许所有具有 userType 成员的用户邀请来宾。

* 确定是否希望用户使用来宾的 userType （Azure AD B2B 用户的默认帐户类型）来邀请其他来宾。 

![来宾邀请设置的屏幕截图。](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>收集有关外部用户的其他信息

如果你使用 Azure AD 的权利管理，则可以为外部用户配置问题的答案。 然后，会向审批者显示这些问题，让他们做出决定。 你可以为每个 [访问包策略](../governance/entitlement-management-access-package-approval-policy.md) 配置不同的问题集，以便审批者可以获取他们要审批的访问权限的相关信息。 例如，如果一个访问包用于供应商访问，则可能会要求请求者提供其供应商合同号。 用于供应商的不同访问包可能要求提供其原国家/地区。

如果使用自助服务门户，则可以使用 [API 连接器](../external-identities/api-connectors-overview.md) 在用户注册时收集有关用户的其他属性。 然后，你可以使用这些属性来分配访问权限。 例如，如果在注册过程中收集其供应商 ID，则可以使用该属性将其动态分配给该供应商的组或访问包。 可以在 Azure 门户中创建自定义属性，并将它们用于自助注册用户流。 还可以使用 [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api) 读写这些属性。 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>对 Azure AD 用户的邀请兑换进行故障排除

有三个实例：使用 Azure AD 的合作伙伴的受邀来宾用户在兑换邀请时将遇到问题。

* 如果使用允许列表并且用户的域未包含在允许列表中。

* 如果协作合作伙伴的 home 租户具有阻止与外部用户协作的租户限制，则为。

* 如果用户不属于合作伙伴的 Azure AD 租户。 例如，位于 contoso.com 的用户只有 Active Directory (或其他本地 IdP) ，他们才能通过电子邮件 OTP 过程兑换邀请。 有关详细信息，请参阅 [邀请兑换流](../external-identities/redemption-experience.md)。

## <a name="control-what-external-users-can-access"></a>控制外部用户可以访问的内容

大多数组织并不是单一的。 这就是，有一些资源与外部用户共享很好，一些您不想访问外部用户。 因此，你必须控制外部用户的访问权限。 请考虑使用 [权利管理和访问包来控制](6-secure-access-entitlement-managment.md) 对特定资源的访问权限。

默认情况下，来宾用户可以查看有关租户成员和其他合作伙伴的信息和属性，其中包括组成员身份。 考虑安全要求是否需要对此信息的外部用户访问进行限制。

![配置外部协作设置的屏幕截图。](media/secure-external-access/5-external-collaboration-settings.png)

建议为来宾用户提供以下限制。

* **限制来宾访问目录中的浏览组和其他属性**

   * 使用外部协作设置来限制来宾读取其不是其成员的组的能力。

* **阻止访问仅限员工的应用**。

   * 创建条件性访问策略以阻止访问仅适用于非来宾用户的 Azure AD 集成的应用程序。 

* **阻止对 Azure 门户的访问。您可以做出极少的必要例外**。 

   * 创建包括所有来宾和外部用户的条件性访问策略，然后 [实施策略来阻止访问](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)。

 

## <a name="remove-users-who-no-longer-need-access"></a>删除不再需要访问权限的用户

评估当前的访问权限，以便您可以 [查看和删除不再需要访问的用户](../governance/access-reviews-external-users.md)。 将租户中的外部用户作为来宾，并将其与成员帐户一起包括在内。 

某些组织添加了外部用户（如供应商、合作伙伴和承包商）作为成员。 这些成员可能有特定属性或以开头的用户名，例如

* v-供应商

* p-合作伙伴

* c-用于承包商

使用成员帐户评估任何外部用户，以确定它们是否仍需要访问权限。 如果是这样，请按照下一节中所述，将这些用户转换为 Azure AD B2B。

你还可能有未通过授权管理或 Azure AD B2B 邀请的来宾用户

若要查找这些用户，可以：

* [查找未通过授权管理邀请的来宾用户](../governance/access-reviews-external-users.md)。

   * 我们提供了一个 [示例 PowerShell 脚本。](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

将这些用户转换为 Azure AD B2B 用户，如下一节中所述。

## <a name="transition-your-current-external-users-to-b2b"></a>将当前外部用户转换为 B2B

如果尚未使用 Azure AD B2B，则可能是租户中的非员工用户。 建议将这些帐户转换为 Azure AD B2B 外部用户帐户，然后将其 UserType 更改为 "来宾"。 这使您可以利用多种方法 Azure AD 和 Microsoft 365 使您能够以不同方式处理外部用户。 其中一些方法包括：

* 在条件访问策略中轻松包含或排除来宾用户

* 在访问包和访问评审中轻松包含或排除来宾用户

* 轻松包括或排除对团队、SharePoint 和其他资源的外部访问权限。

若要在维护这些内部用户的当前访问、UPN 和组成员身份的同时对其进行转换，请参阅 [邀请外部用户使用 B2B 协作](../external-identities/invite-internal-users.md)。

## <a name="decommission-undesired-collaboration-methods"></a>解除意外的协作方法

若要完成到管控协作的过渡，应取消不需要的协作方法。 停用的是基于你希望它对协作进行的控制程度，以及安全状况。 有关其与最终用户控制的信息，请参阅 [确定外部访问的安全状况](1-secure-access-posture.md)。

下面是你可能想要评估的协作工具。

### <a name="direct-invitation-through-microsoft-teams"></a>通过 Microsoft 团队直接邀请

默认情况下，团队允许外部访问，这意味着组织可以与所有外部域通信。 如果希望仅为团队限制或允许特定的域，可以在 [团队管理员门户](https://admin.teams.microsoft.com/company-wide-settings/external-communications)中执行此操作。 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>通过 SharePoint 和 OneDrive 进行直接共享

通过 SharePoint 和 OneDrive 的直接共享可以在权限管理流程之外添加用户。 有关这些配置的详细介绍，请参阅使用 [Microsoft 团队、SharePoint 和 OneDrive for Business 管理访问权限](9-secure-access-teams-sharepoint.md) 如果需要，还可以 [阻止使用用户的个人 OneDrive](https://docs.microsoft.com/office365/troubleshoot/group-policy/block-onedrive-use-from-office) 。

### <a name="sending-documents-through-email"></a>通过电子邮件发送文档

用户会通过电子邮件将文档发送到外部用户。 考虑如何使用敏感度标签来限制和加密对这些文档的访问权限。 有关详细信息，请参阅使用敏感度标签管理访问权限。

### <a name="unsanctioned-collaboration-tools"></a>未批准协作工具

协作工具的前景很广泛。 你的用户可能会使用其官方职责之外的许多功能，包括 Google 文档、DropBox、时差或缩放等平台。 可以阻止在防火墙级别使用企业网络中的此类工具，并使用组织管理的设备的移动应用程序管理。 但是，这也会阻止这些平台的所有批准实例，并且不会阻止从非托管设备进行访问。 如果需要，阻止平台使用，并为需要使用的平台创建无未批准使用的业务策略。 

有关管理未批准应用程序的详细信息，请参阅：

* [管理已连接的应用](https://docs.microsoft.com/cloud-app-security/governance-actions)

* [批准和取消批准应用程序。](https://docs.microsoft.com/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出的顺序执行这些操作。

1. [确定外部访问的安全状况](1-secure-access-posture.md)

2. [发现当前状态](2-secure-access-current-state.md)

3. [创建调控计划](3-secure-access-plan.md)

4. [使用组进行安全保护](4-secure-access-groups.md)

5. [过渡到 AZURE AD B2B](5-secure-access-b2b.md) (。 ) 

6. [使用权限管理的安全访问](6-secure-access-entitlement-managment.md)

7. [使用条件性访问策略的安全访问](7-secure-access-conditional-access.md)

8. [使用敏感度标签进行安全访问](8-secure-access-sensitivity-labels.md)

9. [安全访问 Microsoft 团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)