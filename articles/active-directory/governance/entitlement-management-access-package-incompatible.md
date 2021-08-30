---
title: 在 Azure AD 权利管理中针对访问包配置职责分离 - Azure Active Directory
description: 了解如何在 Azure Active Directory 权利管理中配置为对访问包请求强制实施职责分离。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/2/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 171901de8f9037ee6e77f83138ec7a3065c2d71f
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730153"
---
# <a name="configure-separation-of-duties-checks-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 权利管理中针对访问包配置职责分离检查（预览版）

在 Azure AD 权利管理中，可以配置多个策略，并为需要通过访问包访问的每个用户社区提供不同的设置。  例如，员工可能只需要经理批准即可访问某些应用程序，但来自其他组织的来宾可能同时需要发起人和资源团队部门经理的批准才能访问。 在针对目录中已有用户的策略中，可以指定可为其请求访问的特定用户组。 但是，你可能需要避免用户获得过多的访问权限。  为了满足此要求，将需要根据请求者已有的访问权限进一步限制谁可以请求访问。

通过访问包上的职责分离设置，可以配置为：属于组成员的用户或已分配到一个访问包的用户无法请求其他访问包。

![有关尝试请求不兼容访问权限的 myaccess 体验](./media/entitlement-management-access-package-incompatible/request-prevented.png)


## <a name="scenarios-for-separation-of-duties-checks"></a>有关职责分离检查的方案

例如，你有一个访问包“市场营销活动”，你的组织和其他组织的人员都可以请求访问该包，以便在活动进行时与你组织的营销部门合作。 由于营销部门的员工已有权访问该营销活动材料，因此你不希望他们请求访问包的访问权限。  或者，假设你已有一个动态组“营销部门员工”，其中包括了所有营销员工。 你可以指明该访问包与该动态组的成员身份不兼容。 然后，当营销部门的员工在查找要请求的访问包时，他们无法请求访问“营销活动”访问包。

类似地，假设你的应用程序具有两个角色（“西部销售”和“东部销售”），你想要确保某个用户在某个时间只属于一个销售管辖区 。  如果你有两个访问包：访问包“西部管辖区”提供“西部销售”角色，访问包“东部管辖区”提供“东部销售”角色，那么，可以将   
 - “西部管辖区”访问包配置为与“东部管辖区”访问包不兼容，并将 
 - “东部管辖区”访问包配置为与“西部管辖区”访问包不兼容 。

如果你一直在使用 Microsoft 标识管理器或其他本地标识管理系统来自动访问本地应用程序，则也可以将这些系统与 Azure AD 权利管理进行集成。  如果你将通过权利管理来控制对 Azure AD 集成应用的访问权限，并且想要防止用户进行不兼容的访问，则可以配置为访问包与组不兼容。 这可以是一个组，你的本地标识管理系统通过 Azure AD Connect 将其发送到 Azure AD。 此检查可确保用户无法请求访问包，前提是该访问包提供的访问权限与用户在本地应用中的访问权限不兼容。

## <a name="prerequisites"></a>必备条件

若要使用 Azure AD 权利管理并将用户分配到访问包，必须具有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5 许可证

## <a name="configure-another-access-package-or-group-membership-as-incompatible-for-requesting-access-to-an-access-package"></a>将一个访问包或组成员身份配置为不兼容，因此无法请求访问另一个访问包

必备角色：全局管理员、标识治理管理员、用户管理员、目录所有者或访问包管理员

按照以下步骤更改现有访问包的不兼容组或其他访问包的列表：

1.  登录 [Azure 门户](https://portal.azure.com)。

1.  依次单击“Azure Active Directory”、“标识治理” 。

1.  在左侧菜单中单击“访问包”，然后打开用户将会请求的访问包。

1.  在左侧菜单中，单击“职责分离(预览版)”。

1.  如果你要阻止已分配有另一个访问包的用户请求此访问包，请单击“添加访问包”，然后选择为该用户分配的访问包。


    ![不兼容的访问包的配置](./media/entitlement-management-access-package-incompatible/select-incompatible-ap.png)


1.  如果你要阻止具有现有组成员身份的用户请求此访问包，请单击“添加组”，然后选择该用户已是其成员的组。

### <a name="configure-incompatible-access-packages-programmatically"></a>以编程方式配置不兼容的访问包

还可以使用 Microsoft Graph 配置与访问包不兼容的组和其他访问包。  如果一个用户具有某个应用程序的适当角色，且该应用程序具有委托的 `EntitlementManagement.ReadWrite.All` 权限，或如果某个应用程序具有前述应用程序权限，则该用户或该应用程序可以调用 API 来添加、删除和列出[访问包](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)的不兼容组和访问包。


## <a name="view-other-access-packages-that-are-configured-as-incompatible-with-this-one"></a>查看已配置为与此访问包不兼容的其他访问包

必备角色：全局管理员、标识治理管理员、用户管理员、目录所有者或访问包管理员

按照以下步骤查看已指明与现有访问包不兼容的其他访问包列表：

1.  登录 [Azure 门户](https://portal.azure.com)。

1.  依次单击“Azure Active Directory”、“标识治理” 。

1.  在左侧菜单中单击“访问包”，然后打开访问包。

1.  在左侧菜单中，单击“职责分离(预览版)”。

1. 单击“不兼容”。

## <a name="monitor-and-report-on-access-assignments"></a>监视和报告访问权限分配

可以使用 Azure Monitor 工作簿来了解用户如何接收其访问权限。

1. 配置 Azure AD 以将[审核事件发送到 Azure Monitor](entitlement-management-logs-and-reporting.md)。

1. 名为“访问包活动”的工作簿显示与特定访问包相关的每个事件。

    ![查看访问包事件](./media/entitlement-management-logs-and-reporting/view-events-access-package.png)

1. 若要查看某个应用程序的角色分配（这些分配不是因访问包分配而创建）是否发生了更改，可以选择名为“应用程序角色分配活动”的工作簿。  如果选择忽略权利活动，则只显示不是由权利管理执行的应用程序角色更改。 例如，如果全局管理员直接将用户分配给应用程序角色，会显示一行信息。

    ![查看应用角色分配](./media/entitlement-management-access-package-incompatible/workbook-ara.png)


## <a name="next-steps"></a>后续步骤

- [查看、添加和删除访问包的分配](entitlement-management-access-package-assignments.md)
- [查看报表和日志](entitlement-management-reports.md)
