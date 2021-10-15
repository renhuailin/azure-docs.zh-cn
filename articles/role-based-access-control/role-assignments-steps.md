---
title: 分配 Azure 角色的步骤 - Azure RBAC
description: 了解使用 Azure 基于角色的访问控制 (Azure RBAC) 将 Azure 角色分配给用户、组、服务主体或托管标识的步骤。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.openlocfilehash: 1f1b8f627a60a6e9f9b866ffb48324ecd146ffbe
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362086"
---
# <a name="steps-to-assign-an-azure-role"></a>分配 Azure 角色的步骤

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 本文介绍使用 [Azure 门户](role-assignments-portal.md)、[Azure PowerShell](role-assignments-powershell.md)、[Azure CLI](role-assignments-cli.md) 或 [REST API](role-assignments-rest.md) 分配 Azure 角色的概略性步骤。

## <a name="step-1-determine-who-needs-access"></a>步骤 1：确定哪一用户需要访问权限

首先需要确定哪一用户需要访问权限。 可将角色分配到用户、组、服务主体或托管标识。 这也称为“安全主体”。

![角色分配的安全主体](./media/shared/rbac-security-principal.png)

- 用户 - 在 Azure Active Directory 中具有配置文件的人员。 也可以将角色分配到其他租户中的用户。 有关其他组织中的用户的信息，请参阅 [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md)。
- 组 - 在 Azure Active Directory 中创建的一组用户。 将某个角色分配到某个组时，该组中的所有用户都拥有该角色。 
- 服务主体 - 应用程序或服务用来访问特定 Azure 资源的安全标识。 可将服务主体视为应用程序的用户标识（用户名和密码或证书）。
- 托管标识 - Azure Active Directory 中由 Azure 自动托管的标识。 在开发云应用程序时，通常使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)来管理用于向 Azure 服务进行身份验证的凭据。

## <a name="step-2-select-the-appropriate-role"></a>步骤 2：选择合适的角色

权限将组合到角色定义中。 它通常直接称为“角色”。 可以从多个内置角色的列表中选择。 如果内置角色不能满足组织的特定需求，则可自行创建自定义角色。

![角色分配的角色定义](./media/shared/rbac-role-definition.png)

下面列出了四个基本的内置角色。 前三个角色适用于所有资源类型。

- [所有者](built-in-roles.md#owner) - 拥有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。
- [参与者](built-in-roles.md#contributor) - 可以创建和管理所有类型的 Azure 资源，但无法将访问权限授予其他用户。
- [读取者](built-in-roles.md#reader) - 可以查看现有的 Azure 资源。
- [用户访问管理员](built-in-roles.md#user-access-administrator) - 可以管理用户对 Azure 资源的访问。

剩余的内置角色允许管理特定的 Azure 资源。 例如，[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色允许用户创建和管理虚拟机。

1. 请从综合性文章 [Azure 内置角色](built-in-roles.md)开始。 本文顶部的表是对本文后面的详细信息的索引。

1. 在该文中，导航到要向其授予权限的资源的服务类别（例如，计算、存储和数据库）。 查找所需内容的最简单方法通常是在页面上搜索相关的关键字，如“blob”、“虚拟机”等。

1. 查看服务类别列出的角色，并确定所需的特定操作。 同样，始终以最严格的角色开始。

    例如，如果安全主体需要读取 Azure 存储帐户中的 blob，但不需要写入访问权限，请选择[存储 Blob 数据读取者](built-in-roles.md#storage-blob-data-reader)而不是[存储 Blob 数据参与者](built-in-roles.md#storage-blob-data-contributor)（绝对不是管理员级别的[存储 Blob 数据所有者](built-in-roles.md#storage-blob-data-owner)角色）。 稍后，你可以根据需要随时更新角色分配。

1. 如果找不到合适的角色，可以创建[自定义角色](custom-roles.md)。

## <a name="step-3-identify-the-needed-scope"></a>步骤 3：识别所需的范围

范围是访问权限适用于的资源集。 在 Azure 中，可以在以下四个级别指定范围：[管理组](../governance/management-groups/overview.md)、订阅、[资源组](../azure-resource-manager/management/overview.md#resource-groups)和资源。 范围采用父子关系结构。 层次结构的每个级别都会使范围更具针对性。 可以在其中任何一个范围级别分配角色。 所选级别决定了角色的应用广泛程度。 较低级别继承较高级别的角色权限。 

![角色分配的范围](./media/shared/rbac-scope.png)

在父范围分配角色时，这些权限会继承到子范围。 例如： 。

- 如果将[读取者](built-in-roles.md#reader)角色分配给管理组范围内的用户，则该用户可以读取管理组的所有订阅中的所有内容。
- 如果在订阅范围向某个组分配了[账单读取者](built-in-roles.md#billing-reader)角色，则该组的成员可以读取订阅中每个资源组和资源的账单数据。
- 如果在资源组范围向某个应用程序分配了[参与者](built-in-roles.md#contributor)角色，则该应用程序可以管理该资源组中所有类型的资源，但不能管理订阅中其他资源组的资源。

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] 有关详细信息，请参阅[了解范围](scope-overview.md)。

## <a name="step-4-check-your-prerequisites"></a>步骤 4. 检查先决条件

若要分配角色，你必须在要尝试分配角色的范围内使用分配有一个角色（例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)，此角色拥有角色分配写入权限）的用户登录。 同样，若要删除角色分配，你必须具有角色分配删除权限。

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

如果用户帐户无权在订阅内分配角色，则将显示错误消息“你的帐户无权执行操作 'Microsoft.Authorization/roleAssignments/write'”。在这种情况下，请与你的订阅管理员联系，因为他们可以代表你分配权限。

如果使用服务主体来分配角色，可能会收到错误信息：“权限不足，无法完成操作”。 此错误很可能是因为 Azure 尝试在 Azure Active Directory (Azure AD) 中查找被分派人标识，但服务主体在默认情况下无法读取 Azure AD。 在这种情况下，你需要授予服务主体读取目录中数据的权限。 或者，如果你使用的是 Azure CLI，则可使用被分派人对象 ID 创建角色分配，以跳过 Azure AD 查找。 有关详细信息，请参阅 [Azure RBAC 疑难解答](troubleshooting.md)。

## <a name="step-5-assign-role"></a>步骤 5。 分配角色

了解安全主体、角色和范围后，便可以分配角色了。 可使用 Azure 门户、Azure PowerShell、Azure CLI、Azure SDK 或 REST API 来分配角色。 每个订阅中最多可以包含 **2000** 个角色分配。 此限制包括订阅、资源组和资源范围内的角色分配。 每个管理组中最多可以有 500 个角色分配。

请查看以下文章，了解有关如何分配角色的详细步骤。

- [使用 Azure 门户分配 Azure 角色](role-assignments-portal.md)
- [使用 Azure PowerShell 分配 Azure 角色](role-assignments-powershell.md)
- [使用 Azure CLI 分配 Azure 角色](role-assignments-cli.md)
- [使用 REST API 分配 Azure 角色](role-assignments-rest.md)

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure 门户授予用户对 Azure 资源的访问权限](quickstart-assign-role-user-portal.md)