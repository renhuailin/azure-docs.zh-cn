---
title: 使用 Azure AD 组来管理角色分配 - Azure Active Directory
description: 使用 Azure AD 组来简化 Azure Active Directory 中的角色分配管理。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 09/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f85ab5960aea247a8b47b20d2552cec6e2e534f
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235319"
---
# <a name="use-azure-ad-groups-to-manage-role-assignments"></a>使用 Azure AD 组来管理角色分配

Azure Active Directory (Azure AD) 允许指定 Azure AD 组作为角色分配的目标。 向组分配角色可以简化 Azure AD 中的角色分配管理，并可以尽量减少全局管理员和特权角色管理员的工作量。

## <a name="why-assign-roles-to-groups"></a>为何要向组分配角色？

举例而言，Contoso 公司已在多个地理区域雇用人员，以便为其 Azure AD 组织中的员工管理和重置密码。 无需请求特权角色管理员或全局管理员分别为每个人分配“支持管理员”角色，他们可以创建 Contoso_Helpdesk_Administrators 组并向其分配角色。 当用户加入组时，将间接为他们分配角色。 然后，你现有的治理工作流可以处理审批过程和审核组成员身份，以确保只有合法用户才能成为该组成员，并因此为其分配“支持管理员”角色。

## <a name="how-role-assignments-to-groups-work"></a>如何向组分配角色

若要向组分配角色，必须在将 `isAssignableToRole` 属性设置为 `true` 的情况下，创建一个新的安全组或 Microsoft 365 组。 在 Azure 门户中，将“可将 Azure AD 角色分配到该组”选项设置为“是” 。 无论采用哪种方式，都可以像为用户分配角色那样，为组分配一个或多个 Azure AD 角色。

![“角色和管理员”页的屏幕截图](./media/groups-concept/role-assignable-group.png)

## <a name="restrictions-for-role-assignable-groups"></a>可分配角色的组的限制

可分配角色的组存在以下限制：

- 对于新组，只能设置 `isAssignableToRole` 属性或者“可将 Azure AD 角色分配到该组”选项。
- `isAssignableToRole` 属性是不可变的。 一旦创建一个设置了此属性的组，就不能更改此属性。
- 不能将现有组设为可分配角色的组。
- 在单个 Azure AD 组织（租户）中最多可以创建 400 个可分配角色的组。

## <a name="how-are-role-assignable-groups-protected"></a>如何保护可分配角色的组？

如果为组分配了角色，则任何可以管理组成员身份的 IT 管理员也可以间接管理该角色的成员身份。 例如，假设为名为 Contoso_User_Administrators 组的分配了“用户管理员”角色。 可以修改组成员身份的 Exchange 管理员可以将自己添加到 Contoso_User_Administrators 组中，从而成为用户管理员。 如你所见，管理员可以以你不希望的方式提升他们的特权。

只能为在创建时已将 `isAssignableToRole` 属性设置为 `true` 的组分配角色。 此属性是不可变的。 一旦创建一个设置了此属性的组，就不能更改此属性。 不能对现有组设置属性。

可分配角色的组旨在通过以下限制来帮助防止潜在的违规：

- 只有全局管理员和特权角色管理员能够创建可分配角色的组。
- 可分配角色的组的成员身份类型必须是“已分配”，不能是 Azure AD 动态组。 自动填充动态组可能会导致将不需要的帐户添加到该组，从而将其分配给该角色。
- 默认情况下，只有全局管理员和特权角色管理员可以管理可分配角色的组的成员身份，但可以通过添加组所有者来委托管理可分配角色的组。
- 需要 RoleManagement.ReadWrite.All Microsoft Graph 权限才能管理此类组的成员身份；Group.ReadWrite.All 不起作用。
- 为了防止特权提升，只有特权身份验证管理员或全局管理员才能为可分配角色的组成员及所有者更改凭据或重置 MFA。
- 不支持组嵌套。 不能将组添加为角色可分配的组的成员。

## <a name="use-pim-to-make-a-group-eligible-for-a-role-assignment"></a>使用 PIM 来使组符合角色分配的条件

如果你不希望组的成员长期拥有某个角色的访问权限，可以使用 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 使该组符合角色分配的条件。 然后，该组的每个成员便可以激活角色分配并在固定的一段时间内保持这种状态。

## <a name="scenarios-not-supported"></a>不支持的方案

不支持以下方案：  

- 向本地组分配 Azure AD 角色（内置或自定义）。

## <a name="known-issues"></a>已知问题

下面是可分配角色的组的已知问题：

- 仅适用于 Azure AD P2 授权客户：即使是删除组后，该组仍会在 PIM UI 中显示为相应角色的合格成员。 在功能上没有问题；这只是 Azure 门户中的缓存问题。  
- 使用新的 [Exchange 管理中心](https://admin.exchange.microsoft.com/)通过组成员身份进行角色分配。 旧的 Exchange 管理中心目前尚不支持此功能。 Exchange PowerShell cmdlet 将按预期方式工作。
- Azure 信息保护门户（经典门户）尚未通过组识别角色成员身份。 你可以[迁移到统一的敏感度标签平台](/azure/information-protection/configure-policy-migrate-labels)，然后使用 Office 365 安全与合规中心通过组分配来管理角色。
- [应用管理中心](https://config.office.com/)目前尚不支持此功能。 将用户直接分配到 Office 应用管理员角色。

## <a name="license-requirements"></a>许可要求

使用此功能需要 Azure AD Premium P1 许可证。 若要同时使用 Privileged Identity Management 进行即时角色激活，需要使用 Azure AD Premium P2 许可证。 若要根据需要查找合适的许可证，请参阅[比较免费版和高级版的正式发布功能](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

## <a name="next-steps"></a>后续步骤

- [创建可分配角色的组](groups-create-eligible.md)
- [向组分配 Azure AD 角色](groups-assign-role.md)
