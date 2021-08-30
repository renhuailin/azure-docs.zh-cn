---
title: 建议的安全做法
description: 使用 Azure Lighthouse 时，请务必考虑安全性和访问控制。
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 897f09c41d550d6200cc3d78743bb3b010f2c402
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768263"
---
# <a name="recommended-security-practices"></a>建议的安全做法

使用 [Azure Lighthouse](../overview.md) 时，请务必考虑安全性和访问控制。 你的租户中的用户将具有直接访问客户订阅和资源组的权限，因此你可能希望采取措施来维护你的租户的安全性。 可能还希望确保仅允许必需的访问权限，以有效管理客户的资源。 本主题提供相关建议，以帮助你实现此目的。

> [!TIP]
> 这些建议也适用于使用 Azure Lighthouse [管理多个租户的企业](enterprise.md)。

## <a name="require-azure-ad-multi-factor-authentication"></a>需要 Azure AD 多重身份验证

[Azure AD 多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)（也称为双重验证）通过要求进行多重身份验证步骤，以防止攻击者获取帐户的访问权限。 你应要求管理租户中的所有用户（包括任何将有权访问委托的客户资源的用户）都进行 Azure AD 多重身份验证。

建议你要求客户对其租户也实施 Azure AD 多重身份验证。

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>使用最低权限原则向组分配权限

若要更轻松地进行管理，建议为每个管理客户资源所需的角色都使用 Azure Active Directory (Azure AD) 组。 这样，你就可以根据需要向组添加或删除单个用户，而不是直接向每个用户分配权限。

> [!IMPORTANT]
> 若要为 Azure AD 组添加权限，“组类型”必须设置为“安全”。 此选项是在创建组时选择的。 有关详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

创建权限结构时，请确保遵循最低权限原则，以便用户仅具有完成作业所需的权限，这样有助于减少发生意外错误的几率。

例如，建议使用如下结构：

|组名称  |类型  |principalId  |角色定义  |角色定义 ID  |
|---------|---------|---------|---------|---------|
|架构师     |用户组         |\<principalId\>         |参与者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|评估     |用户组         |\<principalId\>         |读者         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM 专家     |用户组         |\<principalId\>         |VM 参与者         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|自动化     |服务主体名称 (SPN)         |\<principalId\>         |参与者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

创建这些组之后，即可根据需要分配用户。 仅添加真正需要访问权限的用户。 请确保定期查看组成员身份，并删除不再适合或不再需要的任何用户。

请记住，[通过公共托管服务产品载入客户](../how-to/publish-managed-services-offers.md)时，包含的任何组（或用户或服务主体）都将具有与每位购买计划的客户的相同权限。 若要分配不同的组以与每位客户合作，将需要发布专属于每位客户的单独专用计划，或使用 Azure 资源管理器单独载入客户。 例如，可以发布访问权限限制极少的公用计划，然后使用自定义 Azure 资源模板（可根据需要授予更多访问权限）直接与客户一起载入其资源以获取更多访问权限。

## <a name="next-steps"></a>后续步骤

- 查看[安全基线信息](../security-baseline.md)，了解 Azure 安全基准中的指导如何应用于 Azure Lighthouse。
- [部署 Azure AD 多重身份验证](../../active-directory/authentication/howto-mfa-getstarted.md)。
- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
