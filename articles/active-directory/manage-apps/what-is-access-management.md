---
title: 管理对应用的访问权限
titleSuffix: Azure AD
description: 介绍 Azure Active Directory 如何使组织能够指定每个用户有权访问的应用。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 89acfa0c1074d170dd0b0b9fb19f227a4e0c21ab
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061041"
---
# <a name="manage-access-to-apps-in-azure-active-directory"></a>管理对 Azure Active Directory 中应用的访问

将应用集成到组织的标识系统之后，进行中的访问管理、使用评估和报告将持续成为一项挑战。 在许多情况下，IT 管理员或支持人员需要对应用访问管理持续采取主动措施。 有时，分配由一般或分部 IT 小组执行。 分配决策往往由业务决策人委派，在 IT 人员进行分配之前需要其批准。  

其他组织会投资来与现有自动化标识与访问管理系统集成，例如基于角色的访问控制 (RBAC) 或基于属性的访问控制 (ABAC)。 集成与规则开发往往是针对性的且费用高昂。 对任一管理方式进行监视或报告本身是一项独立、昂贵且复杂的投资。

## <a name="how-does-azure-active-directory-help"></a>Azure Active Directory 有何帮助？

Azure AD 针对已配置的应用程序支持广泛的访问管理，使组织能够轻松实现正确的访问策略，范围包括自动的基于分配的策略（ABAC 或 RBAC 方案）到委派和纳入管理员管理。 有了 Azure AD，可以轻松地实现复杂策略，结合单个应用程序的多个管理模型，甚至在具有相同受众的应用程序之间重复使用管理规则。

借助 Azure AD，使用和分配报告可完全集成，使管理员能够轻松报告分配状态、分配错误甚至使用情况。

### <a name="assigning-users-and-groups-to-an-app"></a>将用户和组分配到应用

Azure AD 的应用程序分配着重于两种主要分配模式：

* **单独分配**：具有目录全局管理员权限的 IT 管理员可以选择单个用户帐户并授予这些用户访问应用程序的权限。

* **基于组的分配（需要 Azure AD Premium P1 或 P2）** ：具有目录全局管理员权限的 IT 管理员可以将组分配给应用程序。 特定用户的访问权限取决于用户尝试访问应用程序时是否为组的成员。 换而言之，管理员可以有效地创建分配规则，指明“分配组的任何现有成员都有权访问应用程序”。 使用此分配选项，管理员可以受益于 Azure AD 组管理选项，包括[基于属性的动态组](../fundamentals/active-directory-groups-create-azure-portal.md)、外部系统组（例如，本地 Active Directory 或 Workday）、管理员管理的组或自助管理组。 可以轻松地将单个组分配给多个应用程序，确保具有分配关联性的应用程序可以共享分配规则，从而降低总体管理复杂性。

>[!NOTE]
>目前不支持使用组成员身份来对应用程序进行基于组的分配。

管理员可以使用这两种分配模式实现任何所需的分配管理方案。

### <a name="requiring-user-assignment-for-an-app"></a>要求为应用进行用户分配

对于某些类型的应用程序，你可以选择要求将用户分配给应用程序。 这样一来，可以防止除了你明确分配给应用程序的用户以外的人登录。 以下类型的应用程序支持此选项：

* 经配置后可以使用基于 SAML 的身份验证进行联合单一登录 (SSO) 的应用程序
* 使用 Azure Active Directory 预身份验证的应用程序代理应用程序
* 在 Azure AD 应用程序平台上生成且使用 OAuth 2.0/OpenID Connect 身份验证的应用程序（前提是用户或管理员已许可该应用程序）。 某些企业应用程序能够在更多方面控制允许登录的用户。

需要进行用户分配时，只有（通过直接用户分配或基于组成员身份）分配到应用程序的用户才能登录。 他们可以在“我的应用”门户上或者使用直接链接来访问该应用。

当不需要用户分配时，未分配的用户在其“我的应用”上看不到应用，但他们仍然可以登录到应用程序本身（也称为 SP 启动的登录），或者他们可以在应用程序的“属性”页（也称为 IDP 启动的登录）中使用“用户访问 URL”。  有关要求用户分配配置的详细信息，请参[阅配置应用程序](add-application-portal-configure.md)

应用程序是否显示在“我的应用”上不受此设置的影响。 将某个用户或组分配到应用程序后，应用程序会显示在用户的“我的应用”访问面板上。

> [!NOTE]
> 当应用程序要求分配时，不允许用户同意该应用程序。 即使允许用户同意该应用，也是如此。 请务必对要求分配的应用[授予租户范围的管理员同意](../manage-apps/grant-admin-consent.md)。

对于某些应用程序，要求用户分配的选项在应用程序的属性中不可用。 在这些情况下，可以使用 PowerShell 在服务主体上设置 appRoleAssignmentRequired 属性。

### <a name="determining-the-user-experience-for-accessing-apps"></a>确定访问应用的用户体验

Azure AD 提供[多种可自定义的方式来向组织中的最终用户部署应用程序](end-user-experiences.md)：

* Azure AD 我的应用
* Microsoft 365 应用程序启动器
* 直接登录联合应用 (service-pr)
* 联合、基于密码或现有的应用的深层链接

可以确定分配给企业应用的用户是否可以在我的应用和 Microsoft 365 应用程序启动器中看到它。

## <a name="example-complex-application-assignment-with-azure-ad"></a>示例：使用 Azure AD 进行复杂应用程序分配

以 Salesforce 之类的应用程序为例。 在许多组织中，Salesforce 主要由营销和销售团队使用。 通常，营销团队成员对 Salesforce 拥有较高的访问权限，而销售团队的访问权限则受限。 在许多情况下，大范围的信息工作者对应用程序的访问权限会受到限制。 这些规则存在的例外使情况变得复杂。 营销或销售领导团队通常有特权授予用户访问权限，或独立于这些常规规则更改其角色。

使用 Azure AD 可将 Salesforce 等应用程序预先配置为支持单一登录 (SSO) 和自动化预配。 配置应用程序后，管理员可以执行一次性的操作来创建和分配相应的组。 在本示例中，管理员可以执行以下分配：

* 可以定义[动态组](../fundamentals/active-directory-groups-create-azure-portal.md)，自动代表营销和销售团队的所有成员使用部门或角色之类的属性：
  
  * 为营销组的所有成员分配 Salesforce 中的“营销”角色
  * 为销售组的所有成员分配 Salesforce 中的“销售”角色。 进一步细化可以使用代表分配有不同 Salesforce 角色的区域销售团队的多个组。

* 若要启用例外机制，可为每个角色创建自助组。 例如，可以将“Salesforce 营销例外”组创建为自助服务组。 可以为该组分配 Salesforce 营销角色，而营销领导团队可成为所有者。 营销领导团队的成员可以添加或删除用户、设置加入策略甚至批准或拒绝单个用户的加入请求。 信息工作者只需有相应经验即可支持该机制，不需要对所有者或成员进行专门培训。

在这种情况下，所有已分配的用户都将自动预配到 Salesforce。 当这些用户被添加到不同的组时，他们的角色分配将在 Salesforce 中更新。 用户可以通过我的应用、Office Web 客户端或者通过浏览到其组织的 Salesforce 登录页来发现和访问 Salesforce。 管理员可以使用 Azure AD 报告轻松查看使用情况和分配状态。

管理员可以运用 [Azure AD 条件性访问](../conditional-access/concept-conditional-access-users-groups.md)为特定角色设置访问策略。 这些策略可能包括是否允许从企业环境外部访问、多重身份验证或设备要求，以在各种情况下实现访问。

## <a name="access-to-microsoft-applications"></a>对 Microsoft 应用程序的访问

Microsoft 应用程序（如 Exchange、SharePoint、Yammer 等）的分配和管理与集成到 Azure AD 进行单一登录的第三方 SaaS 应用程序或其他应用程序略有不同。

用户访问 Microsoft 发布的应用程序的方法主要有三种。

* 对于 Microsoft 365 或其他付费套件中的应用程序，可以通过 **许可证分配** 直接向用户的用户帐户授予访问权限，也可以使用基于组的许可证分配功能通过组来这样做。
* 对于 Microsoft 或第三方发布的可供任何人免费使用的应用程序，可以通过[用户许可](configure-user-consent.md)授予用户访问权限。 用户使用其 Azure AD 工作或学校帐户登录到应用程序，并允许它访问其帐户上一些受限制的数据集。

* 对于 Microsoft 或第三方发布的可供任何人免费使用的应用程序，还可以通过[管理员许可](manage-consent-requests.md)授予用户访问权限。 这意味着管理员已确定组织中的所有人都可以使用此应用程序，因此他们使用全局管理员帐户可以登录到应用程序并向组织中所有人授予访问权限。

某些应用程序合并这些方法。 例如，某些 Microsoft 应用程序是 Microsoft 365 订阅的一部分，但仍需要许可。

用户可以通过其 Office 365 门户访问 Microsoft 365 应用程序。 还可以通过目录的“用户设置”中的 [Office 365 可见性切换](hide-application-from-user-portal.md)，在“我的应用”中显示或隐藏 Microsoft 365 应用程序。

与企业应用一样，你可以通过 Azure 门户[分配用户](assign-user-or-group-access-portal.md)到某些 Microsoft 应用程序，或者如果门户方式不可用，则可以使用 PowerShell。

## <a name="next-steps"></a>后续步骤

* [使用条件访问保护应用](../conditional-access/concept-conditional-access-cloud-apps.md)
* [自助服务组管理/SSAA](../enterprise-users/groups-self-service-management.md)
