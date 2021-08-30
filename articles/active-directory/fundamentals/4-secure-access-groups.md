---
title: 使用 Azure Active Directory 和 Microsoft 365 中的组保护外部访问
description: 当外部用户访问资源时，可使用 Azure Active Directory 和 Microsoft 365 组来提高安全性。
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
ms.openlocfilehash: 2500d1872c2c70c8d39afa0b564c35a8bb9bcb9b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747871"
---
# <a name="securing-external-access-with-groups"></a>使用组保护外部访问 

组是任何访问控制策略的重要组成部分。 Azure Active Directory (Azure AD) 安全组和 Microsoft 365 组可用作对资源访问保护的基础。

组是用作以下访问控制机制的基础的最佳选项：

* 条件访问策略

* 权利管理访问包 

* 对 Microsoft 365 资源、Microsoft Teams 和 SharePoint 站点的访问权限

组具有以下角色：

* 所有者 - 组所有者管理组设置及其成员身份。

* 成员 - 成员继承分配给组的权限和访问权限。

* 来宾 - 来宾是组织外部的成员。 

## <a name="determine-your-group-strategy"></a>确定组策略

在制定组策略以保护对资源的外部访问时，请考虑[所需的安全状况](1-secure-access-posture.md)以确定以下选择。

* 谁应该能够创建组？ 你希望只有管理员能创建组，还是希望员工和/或外部用户也能创建这些组。

   * 默认情况下，任何租户成员均可创建 Azure AD 安全组。 

      * 你可[限制非管理员对门户的访问权限](../develop/howto-restrict-your-app-to-a-set-of-users.md)，并在 [PowerShell](../enterprise-users/groups-troubleshooting.md) 中禁用组创建功能。 

      * 还可[在 Azure Active Directory 中设置自助服务组管理](../enterprise-users/groups-self-service-management.md)。 

   * 默认情况下，所有用户都可以创建 Microsoft 365 组和其他组，以便租户中的所有（内部和外部）用户加入。 

      * 可以限制只有特定安全组的成员才可[创建 Microsoft 365 组](/microsoft-365/solutions/manage-creation-of-groups)。 使用 Windows PowerShell 配置此设置。 

* 谁应该能够邀请人员加入组？ 所有组成员都能添加其他成员，还是只有组所有者能添加成员？

* 谁可以被邀请进入组？ 默认情况下，可将外部用户添加到组。 

### <a name="assign-users-to-groups"></a>将用户分配到组

可根据用户对象中的用户属性或其他条件，手动将用户分配到组。 用户只能基于其属性动态分配到组。

例如，你可根据用户的以下信息将其分配到组：

* 特定职务或部门

* 其所属的合作伙伴组织（手动或通过已连接的组织）

* 用户类型（成员或来宾）

* 特定项目的参与（手动）

* location

动态组可以包含用户或设备，但不能同时包含两者。 添加基于用户属性的查询，以将用户分配到动态组。 下面的示例显示了一些查询，如果用户是成员（而非来宾）并且属于财务部门，这些查询会将这些用户添加到组。

![配置动态成员身份规则的屏幕截图。](media/secure-external-access/4-dynamic-membership-rules.png)

有关动态组的详细信息，请参阅[在 Azure Active Directory 中创建或更新动态组。](../enterprise-users/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>请勿使用组来实现多种目的

使用组实现安全性或进行资源访问时，这些组具有单一的功能，这一点很重要。 如果使用组来授予对资源的访问权限，则不应将其用于任何其他目的。 如果某个组用于常规用途（例如定义位置或团队成员身份），则它不应同时用于保护访问。 

建议使用一个目的明确的安全组命名约定。 例如：

* Secure_access_finance_apps

* Team_membership_finance_team

* Location_finance_building



### <a name="types-of-groups"></a>组的类型

Azure AD 安全组和 Microsoft 365 组均可从 Azure AD 门户或 Microsoft 365 管理门户中创建。 这两种类型都可用作保护外部访问的基础：

|注意事项 | Azure AD 安全组（手动和动态）| Microsoft 365 组 |
| - | - | - |
| 组可以包含哪些内容？| 用户<br>组<br>服务主体<br>设备| 仅用户 |
| 组在何处创建？| Azure AD 门户<br>Microsoft 365 门户（若要支持邮件）<br>PowerShell<br>Microsoft Graph<br>最终用户门户| Microsoft 365 门户<br>Azure AD 门户<br>PowerShell<br>Microsoft Graph<br>在 Microsoft 365 应用程序中 |
| 默认谁创建？| 管理员 <br>最终用户| 管理员<br>最终用户 |
| 默认情况下可添加谁？| 内部用户（成员）| 来自任何组织的租户成员和来宾 |
| 它授予什么内容的访问权限？| 仅分配给它的资源。| 所有组相关的资源：<br>（组邮箱、站点、团队、聊天和其他包含的 Microsoft 365 资源）<br>添加到组中的任何其他资源 |
| 可用于| 条件性访问<br>权利管理<br>组许可| 条件性访问<br>权利管理<br>敏感度标签 |



使用 Microsoft 365 组来创建和管理一组 Microsoft 365 资源，如团队及其关联的站点和内容。 它们是基于项目的工作的最佳选择。 

 

## <a name="azure-ad-security-groups"></a>Azure AD 安全组 

[Azure AD 安全组](./active-directory-manage-groups.md)可以包含用户或设备，并可用于管理对 Azure 资源的访问权限， 

* 例如 Microsoft 365 应用、自定义应用和软件即服务 (SaaS) 应用（如 Dropbox 的 ServiceNow）。

* Azure 数据和订阅。

* Azure 服务。

Azure AD 安全组还可用于：

* 为 Microsoft 365、Dynamics 365 和企业移动性和安全性等服务分配许可证。 有关详细信息，请参阅[基于组的许可](./active-directory-licensing-whatis-azure-portal.md)。

* 分配提升的权限。 有关详细信息，请参阅[使用 Azure AD 组来管理角色分配](../roles/groups-concept.md)。 

若要[在 Azure 门户中](./active-directory-groups-create-azure-portal.md)创建组，请导航到 Azure Active Directory，然后转到“组”。 还可使用 [PowerShell cmdlet](../enterprise-users/groups-settings-v2-cmdlets.md) 创建 Azure AD 安全组。 

> [!NOTE]
> 一个安全组可用于分配多达 1500 个应用程序，但不能超过此数量。 

![创建安全组的屏幕截图。](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> 若要创建支持邮件的安全组，请转到 [Microsoft 365 管理中心](https://admin.microsoft.com/)。 无法在 Azure AD 门户中创建它。 
<br>必须在创建时为安全组启用邮件功能。 之后无法启用它。

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>混合组织和 Azure AD 安全组

混合组织同时具有本地基础结构和 Azure AD 云基础结构。 许多使用 Active Directory 的混合组织在本地创建安全组，并将其同步到云。 使用此方法时，只能将本地环境中的用户添加到安全组。

保护本地基础结构免遭破坏，因为本地安全漏洞可被利用以访问你的 Microsoft 365 租户。 有关指导，请参阅[保护 Microsoft 365 免受本地攻击](./protect-m365-from-on-premises-attacks.md)。

## <a name="microsoft-365-groups"></a>Microsoft 365 组

[Microsoft 365 组](/microsoft-365/admin/create-groups/office-365-groups)是推动跨 Microsoft 365 的所有访问的基础成员身份服务。 可通过 [Azure 门户](https://portal.azure.com/)或 [Microsoft 365 门户](https://admin.microsoft.com/)来创建这些组。 创建 Microsoft 365 组后，你可授予对用于协作的一组资源的访问权限。 有关这些资源的完整列表，请参阅[面向管理员的 Microsoft 365 组概述](/microsoft-365/admin/create-groups/office-365-groups)。

Microsoft 365 组在角色方面有以下细微差别

* **所有者** - 组所有者可以添加或删除成员，并具有独特的权限，例如可从共享收件箱中删除会话或更改组设置。 组所有者可以重命名组、更新说明或图片等。

* **成员** - 成员可以访问组中的所有内容，但不能更改组设置。 默认情况下，组成员可以邀请来宾加入你的组，但你可[控制该设置](/microsoft-365/admin/create-groups/manage-guest-access-in-groups)。

* **来宾** - 组来宾是组织外部的成员。 默认情况下，来宾对 Teams 中的功能有一些限制。

 

### <a name="microsoft-365-group-settings"></a>Microsoft 365 组设置

在设置时，选择电子邮件别名、隐私以及是否为团队启用该组。 

![编辑 Microsoft 365 组设置的屏幕截图](media/secure-external-access/4-edit-group-settings.png)

设置完成后，添加成员并配置电子邮件使用设置等。

### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出顺序执行这些操作。

1. [确定外部访问所需的安全状况](1-secure-access-posture.md)

2. [了解当前状况](2-secure-access-current-state.md)

3. [创建治理计划](3-secure-access-plan.md)

4. [使用组以确保安全性](4-secure-access-groups.md)（本文。）

5. [转换到 Azure AD B2B](5-secure-access-b2b.md)

6. [通过权利管理实现安全访问](6-secure-access-entitlement-managment.md)

7. [通过条件访问策略实现安全访问](7-secure-access-conditional-access.md)

8. [通过敏感度标签实现安全访问](8-secure-access-sensitivity-labels.md)

9. [实现对 Microsoft Teams、OneDrive 和 SharePoint 的安全访问](9-secure-access-teams-sharepoint.md)