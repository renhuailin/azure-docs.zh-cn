---
title: Azure Active Directory 和 Microsoft 365 中的组进行安全的外部访问
description: 当外部用户访问资源时，可以使用 Azure Active Directory 和 Microsoft 365 组来提高安全性。
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
ms.openlocfilehash: f83e5584f8f9c6823e1259cb5e6034d8b13ae3a6
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222371"
---
# <a name="securing-external-access-with-groups"></a>使用组保护外部访问 

组是任何访问控制策略的重要组成部分。 Azure Active Directory (Azure AD) 安全组和 Microsoft 365 (M365) 组可用作保护对资源的访问的基础。

组是用作以下访问控制机制基础的最佳选项：

* 条件访问策略

* 授权管理访问包 

* 对 M365 资源、Microsoft 团队和 SharePoint 站点的访问权限

组具有下列角色：

* 所有者–组所有者管理组设置及其成员身份。

* 成员–继承分配给组的权限和访问权限的成员。

* 来宾–来宾是组织外部的成员。 

## <a name="determine-your-group-strategy"></a>确定组策略

开发组策略以保护资源的外部访问权限时，请考虑 [所需的安全状况](1-secure-access-posture.md) ，以确定以下选择。

* **谁应能够创建组？** 你只希望管理员创建组，还是希望员工和或外部用户也创建这些组。

   * *默认情况下，任何租户成员均可创建 Azure AD 安全组*。 

      * 可以 [限制非管理员访问门户](../develop/howto-restrict-your-app-to-a-set-of-users.md) ，并在 PowerShell 中禁用组创建功能 [。](../users-groups-roles/groups-troubleshooting.md) 

      * 你还可以 [在 Azure Active Directory 中设置自助服务组管理](../users-groups-roles/groups-self-service-management.md)。 

   * *默认情况下，所有用户都可以为租户中的所有 (内部和外部) 用户创建 M365 组和组，以便加入*。 

      * [可以将 Microsoft 365 组创建限制](https://docs.microsoft.com/microsoft-365/solutions/manage-creation-of-groups?view=o365-worldwide) 为特定安全组的成员。 使用 Windows PowerShell 配置此设置。 

* **谁应能够邀请人员加入组？** 所有组成员都能添加其他成员，还是只能组所有者添加成员？

* **谁可以邀请到组？** 默认情况下，可将外部用户添加到组。 

### <a name="assign-users-to-groups"></a>将用户分配到组

可以根据用户对象中的用户属性或其他条件，手动将用户分配到组。 用户只能基于其属性动态分配到组。

例如，可以根据用户的以下内容将用户分配到组：

* 特定职务或部门

* 他们所属的合作伙伴组织 (手动或通过连接的组织) 

* 用户类型 (成员或来宾) 

* 手动参与特定项目 () 

* location

动态组可以包含用户或设备，但不能同时包含两者。 添加基于用户属性的查询，以将用户分配到动态组。 下面的示例显示了向组添加用户的查询（如果这些用户是 (不是来宾的成员）) 和财务部门。

![配置动态成员身份规则的屏幕截图。](media/secure-external-access/4-dynamic-membership-rules.png)

有关动态组的详细信息，请参阅 [在 Azure Active Directory 中创建或更新动态组。](../users-groups-roles/groups-create-rule.md)

### <a name="do-not-use-groups-for-multiple-purposes"></a>请勿使用组来实现多种目的

当使用组进行安全或资源访问时，必须有一个函数。 如果使用组来授予对资源的访问权限，则不应将其用于任何其他目的。 如果某个组用于常规用途（例如）定义位置或团队成员身份，则它也不应用于保护访问。 

我们建议使用安全组的命名约定，这会使目的更清晰。 例如：

* *Secure_access_finance_apps*

* *Team_membership_finance_team*

* *Location_finance_building*



### <a name="types-of-groups"></a>组的类型

Azure AD 安全组和 Microsoft 365 组都可以从 Azure AD 门户或 M365 管理门户创建。 这两种类型都可用作保护外部访问的基础：

|注意事项 | Azure AD 安全组 (手动和动态) | Microsoft 365 组 |
| - | - | - |
| 组可以包含哪些内容？| 用户<br>组<br>服务主体<br>设备| 仅用户 |
| 组创建在何处？| Azure AD 门户<br>若要启用邮件，请 (M365 门户) <br>PowerShell<br>Microsoft Graph<br>最终用户门户| M365 门户<br>Azure AD 门户<br>PowerShell<br>Microsoft Graph<br>在 Microsoft 365 应用程序中 |
| 谁默认创建？| Administrators <br>最终用户| Administrators<br>最终用户 |
| 默认情况下，谁可以添加？| 内部用户 (成员) | 来自任何组织的租户成员和来宾 |
| 它授予访问权限的内容是什么？| 仅分配给它的资源。| 所有组相关资源：<br> (组邮箱、站点、团队、聊天以及其他包含的 M365 资源) <br>向其中添加组的任何其他资源 |
| 可用于| 条件性访问<br>权利管理<br>组许可| 条件性访问<br>权利管理<br>敏感度标签 |



使用 Microsoft 365 组来创建和管理一组 Microsoft 365 资源，如团队及其关联的网站和内容。 它们是基于项目的工作的最佳选择。 

 

## <a name="azure-ad-security-groups"></a>Azure AD 安全组 

[Azure AD 安全组](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) 可以包含用户或设备，并可用于管理对的访问权限 

* Azure 资源（例如 Microsoft 365 应用、自定义应用和软件即服务 (SaaS) 应用，如 Dropbox 的 ServiceNow）。

* Azure 数据和订阅。

* Azure 服务。

Azure AD 安全组还可用于：

* 为 M365、Dynamics 365 和企业移动性和安全性等服务分配许可证。 有关详细信息，请参阅 [基于组的许可](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)。

* 分配提升的权限。 有关详细信息，请参阅 [使用云组管理角色分配 (预览](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept)) 。 

若要 [在 Azure 门户中](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) 创建一个组，请导航到 Azure Active Directory，然后导航到 "组"。 你还可以使用 [PowerShell cmdlet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)创建 Azure AD 安全组。 

> [!NOTE]
> 安全组可用于分配最多1500个应用程序，但不能更多。 

![创建安全组的屏幕截图。](media/secure-external-access/4-create-security-group.png)

> [!IMPORTANT]
> **若要创建启用邮件的安全组，请前往 [Microsoft 365 管理中心](https://admin.microsoft.com/)**。 无法在 Azure AD 门户中创建它。 
<br>创建时，必须为邮件启用安全组。 以后不能再启用它。

### <a name="hybrid-organizations-and-azure-ad-security-groups"></a>混合组织和 Azure AD 安全组

混合组织具有本地基础结构和 Azure AD 云基础结构。 许多使用 Active Directory 的混合组织在本地创建安全组，并将其同步到云。 使用此方法时，只能将本地环境中的用户添加到安全组。

**保护本地基础结构免遭破坏，因为本地安全漏洞可用于访问你的 Microsoft 365 租户**。 有关指导，请参阅 [保护 Microsoft 365 本地攻击](https://aka.ms/protectm365) 。

## <a name="microsoft-365-groups"></a>Microsoft 365 组

[Microsoft 365 组](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) 是一种基础成员身份服务，可促进跨 M365 的所有访问。 可以通过 [Azure 门户](https://portal.azure.com/)或 [M365 门户](https://admin.microsoft.com/)来创建它们。 创建 M365 组后，你可以授予对用于协作的一组资源的访问权限。 有关这些资源的完整列表，请参阅 [管理员的 Microsoft 365 组概述](https://docs.microsoft.com/microsoft-365/admin/create-groups/office-365-groups?view=o365-worldwide) 。

对于角色，M365 组具有以下细微差别

* **所有者** -组所有者可以添加或删除成员，并且具有独特的权限，例如从共享收件箱中删除会话或更改组设置。 组所有者可以重命名组、更新说明或图片等。

* **成员** -成员可以访问组中的所有内容，但不能更改组设置。 默认情况下，组成员可以邀请来宾加入你的组，但你可以 [控制该设置](https://docs.microsoft.com/microsoft-365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide)。

* **来宾** -组来宾是来自你的组织外部的成员。 默认情况下，来宾对团队中的功能有一些限制。

 

### <a name="m365-group-settings"></a>M365 组设置

选择 "电子邮件别名"、"隐私"，以及在设置时是否为团队启用组。 

![编辑 Microsoft 365 组设置的屏幕截图](media/secure-external-access/4-edit-group-settings.png)

安装完成后，添加成员，并配置电子邮件使用情况等的设置。

### <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出的顺序执行这些操作。

1. [确定外部访问所需的安全状况](1-secure-access-posture.md)

2. [发现当前状态](2-secure-access-current-state.md)

3. [创建调控计划](3-secure-access-plan.md)

4. 在此处[使用安全](4-secure-access-groups.md) (的组。 ) 

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [使用权限管理的安全访问](6-secure-access-entitlement-managment.md)

7. [使用条件性访问策略的安全访问](7-secure-access-conditional-access.md)

8. [使用敏感度标签进行安全访问](8-secure-access-sensitivity-labels.md)

9. [安全访问 Microsoft 团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)