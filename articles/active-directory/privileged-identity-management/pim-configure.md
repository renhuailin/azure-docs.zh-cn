---
title: 什么是 Privileged Identity Management？ - Azure AD | Microsoft Docs
description: 概述 Azure AD Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 06/25/2021
ms.author: curtand
ms.custom: pim,azuread-video-2020,contperf-fy21q3-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 697dd72ba72924a14501a1410255a817f50f6789
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738709"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>什么是 Azure AD Privileged Identity Management？

 Privileged Identity Management (PIM) 是 Azure Active Directory (Azure AD) 中用于管理、控制和监视对组织中重要资源的访问的服务。 这些资源包括 Azure AD、Azure 和其他 Microsoft Online Services（例如 Microsoft 365 或 Microsoft Intune）中的资源。 以下视频介绍了重要的 PIM 概念和功能。
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>使用原因

组织希望尽量减少有权访问安全信息或资源的人数，因为这样做可以减少出现以下情况的机会：

- 恶意参与者获得访问权限
- 授权用户无意中影响敏感资源

但是，用户仍需在 Azure AD、Azure、Microsoft 365 或 SaaS 应用中执行特权操作。 组织可以为用户提供对 Azure 和 Azure AD 资源的即时特权访问，并可以监督这些用户使用其特权访问权限执行哪些操作。

## <a name="license-requirements"></a>许可要求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

有关用户许可证的信息，请参阅[使用 Privileged Identity Management 所要满足的许可证要求](subscription-requirements.md)。

## <a name="what-does-it-do"></a>它有什么作用？

Privileged Identity Management 提供基于时间和基于审批的角色激活，用于缓解所关注资源上出现的访问权限过度、不必要或滥用的风险。 下面是 Privileged Identity Management 的一些重要功能：

- 提供对 Azure AD 和 Azure 资源的 **实时** 特权访问权限
- 使用开始和结束日期分配对资源的 **限时** 访问权限
- 要求获得 **批准** 才能激活特权角色
- 强制要求在激活任何角色时执行 **多重身份验证**
- 使用 **理由** 来了解用户激活角色的原因
- 激活特权角色时获取 **通知**
- 开展 **访问评审**，以确保用户仍然需要角色
- 下载 **审核历史记录** 进行内部或外部审核
- 阻止删除上一个活动全局管理员角色分配

## <a name="what-can-i-do-with-it"></a>它的作用是什么？

设置 Privileged Identity Management 时，左侧导航菜单中会显示“任务”、“管理”和“活动”选项。    作为管理员，你将在管理 Azure AD 角色，管理 Azure 资源角色或特权访问组等选项之间进行选择 。 选择要管理的选项时，将看到该选项的相应选项集。

![Azure 门户中 Privileged Identity Management 的屏幕截图](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>用户及其权限

对于 Privileged Identity Management 中的 Azure AD 角色，只有充当“特权角色管理员”或“全局管理员”角色的用户才能管理其他管理员分配。 全局管理员、安全管理员、全局读取者和安全读取者也可在 Privileged Identity Management 中查看 Azure AD 角色的分配。

对于 Privileged Identity Management 中的 Azure 资源角色，只有订阅管理员、资源所有者或资源用户访问管理员可以管理其他管理员的分配。 默认情况下，充当特权角色管理员、安全管理员或安全读取者的用户无权在 Privileged Identity Management 中查看 Azure 资源角色的分配。

## <a name="terminology"></a>术语

为了更好地理解 Privileged Identity Management 及其文档，应查看以下术语。

| 术语或概念 | 角色分配类别 | 说明 |
| --- | --- | --- |
| 符合条件 | 类型 | 要求用户在使用角色之前执行一项或多项操作的角色分配。 如果用户符合某个角色的条件，则意味着他们在需要执行特权任务时可以激活该角色。 用户无论具有永久角色分配还是合格角色分配，获得的访问权限并无差异。 唯一的差异在于，有些用户并不是一直需要该访问权限。 |
| 活动 | 类型 | 不要求用户在使用角色之前执行任何操作的角色分配。 分配为“活动”的用户拥有分配给该角色的特权。 |
| 激活 |  | 合格用户在使用角色之前执行一项或多项操作的过程。 操作可能包括执行多重身份验证 (MFA) 检查、提供业务理由或请求获得指定审批者的批准。 |
| 已分配 | 状态 | 具有活动角色分配的用户。 |
| 已激活 | 状态 | 具有符合条件的角色分配、已执行激活角色的操作且现在处于活动状态的用户。 激活后，用户即可使用该角色，使用期限是预先配置的，过期之后需重新激活。 |
| 永久符合条件 | Duration | 使用户始终有资格激活该角色的角色分配。 |
| 永久活动 | Duration | 使用户无需执行任何操作，始终可以使用该角色的角色分配。 |
| 限时符合条件 | 持续时间 | 通过这种角色分配方式，用户只有在开始和结束日期范围内才有资格激活角色。 |
| 限时处于活动状态 | 持续时间 | 通过这种角色分配方式，用户只能在开始和结束日期范围内使用角色。 |
| 实时 (JIT) 访问 |  | 一种访问模式。在此模式下，用户会收到执行特权任务的临时权限，防止恶意用户或未授权用户在权限过期后获得访问权限。 只有在用户需要的情况下，才会授予访问权限。 |
| 最低访问权限原则 |  | 一种建议的安全做法，仅为每个用户提供所需的最低权限，以便完成有权执行的任务。 此做法会尽量减少全局管理员的数目，并使用适合特定方案的特定管理员角色。 |

## <a name="extend-and-renew-assignments"></a>延期并续订分配

在设置了受时间限制的所有者或成员分配之后，你可能提出的第一个问题是在分配到期后会出现什么情况？ 在此新版本中，我们为此情形提供了两个选项：

- 延期 – 当角色分配接近到期时，用户可以使用 Privileged Identity Management 请求对角色分配进行延期
- 续订 – 当角色分配已到期时，用户可以使用 Privileged Identity Management 请求对角色分配进行续订

这两个用户发起的操作都需要获得全局管理员或特权角色管理员的批准。 管理员无需管理过期的分配。 只需等待延期或续订请求到达，然后进行简单批准或拒绝。

## <a name="scenarios"></a>方案

Privileged Identity Management 支持以下方案：

### <a name="privileged-role-administrator-permissions"></a>特权角色管理员权限

- 启用对特定角色的审批
- 指定要审批请求的审批者用户或组
- 查看所有特权角色的请求和审批历史记录

### <a name="approver-permissions"></a>审批者权限

- 查看挂起的审批（请求）
- 批准或拒绝角色提升请求（单个和批量）
- 提供批准或拒绝的理由

### <a name="eligible-role-user-permissions"></a>有资格的角色用户权限

- 请求激活需要审批的角色
- 查看要激活的请求的状态
- 批准激活后，在 Azure AD 中完成任务

## <a name="managing-privileged-access-azure-ad-groups-preview"></a>管理特权访问 Azure AD 组（预览版）

在 Privileged Identity Management (PIM) 中，现在可以针对特权访问组的成员资格或所有权分配资格。 从此预览版开始，可以将 Azure Active Directory (Azure AD) 内置角色分配给云组，并使用 PIM 管理组成员和所有者资格和激活。 有关 Azure AD 中可分配角色的组的详细信息，请参阅[使用 Azure AD 组管理角色分配](../roles/groups-concept.md)。

>[!Important]
> 若要将特权访问组分配到角色，以实现 Exchange、安全与合规中心或 SharePoint 的管理访问，请使用 Azure AD 门户“角色和管理员”体验，而不是使用“特权访问组”体验来允许将用户或组激活到组中。

### <a name="different-just-in-time-policies-for-each-group"></a>每个组不同的即时策略

某些组织使用 Azure AD 企业到企业 (B2B) 协作之类的工具邀请其合作伙伴作为来宾加入其 Azure AD 组织。 可以使用创建两个不同的特权访问组及其自己的策略，而不是为针对某个特权角色的所有分配使用单一的实时策略。 可以对受信任员工实施不太严格的要求，并在合作伙伴请求激活到其分配的组时对他们实施更严格的要求。

### <a name="activate-multiple-role-assignments-in-one-request"></a>在一个请求中激活多个角色分配

借助特权访问组预览版，可以为特定于工作负载的管理员提供通过单个实时请求快速访问多个角色的权限。 例如，第 3 层 Office 管理员可能需要实时访问 Exchange 管理员、Office 应用管理员、Teams 管理员和搜索管理员角色，以便每天彻底调查事件。 今天之前这需要四个连续请求，是一个需要花费一些时间的过程。 相反，可以创建一个名为“第 3 层 Office 管理员”的可分配角色的组，将它分配给前面提到的所有四个角色（或任何 Azure AD 内置角色），并在该组的“活动”部分中为特权访问启用相应角色。 为特权访问启用后，可以为组成员配置实时设置，并将管理员和所有者分配为符合条件。 当管理员提升到组中时，他们会成为所有四个 Azure AD 角色的成员。

## <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中邀请来宾用户并分配 Azure 资源角色

Azure Active Directory (Azure AD) 来宾用户是 Azure AD 中企业对企业 (B2B) 协作功能的一部分，因此你可以在 Azure AD 中将外部来宾用户和供应商作为来宾进行管理。 例如，可以将这些 Privileged Identity Management 功能用于来宾的 Azure 标识任务，例如分配对特定 Azure 资源的访问权限、指定分配持续时间和结束日期，或者要求对活动分配或激活进行双重验证。 若要详细了解如何邀请来宾加入组织并管理其访问权限，请参阅[在 Azure AD 门户中添加 B2B 协作用户](../external-identities/add-users-administrator.md)。

### <a name="when-would-you-invite-guests"></a>你何时会邀请来宾？

下面是说明你何时会为组织邀请来宾的几个示例：

- 允许仅拥有电子邮件帐户的外部个体私营供应商访问项目的 Azure 资源。
- 允许使用本地 Active Directory 联合身份验证服务的大型组织的外部合作伙伴访问费用应用程序。
- 允许组织外的支持工程师（如 Microsoft 支持部门）临时访问 Azure 资源，以解决问题。

### <a name="how-does-collaboration-using-b2b-guests-work"></a>使用 B2B 来宾的协作如何进行？

使用 B2B 协作时，可以将外部用户作为来宾邀请到你的组织。 可以将来宾作为你的组织中的用户进行管理，但来宾必须在其本组织中而不是在你的 Azure AD 组织中进行身份验证。 这意味着，如果来宾不再有权访问其本组织，他们也将失去对你的组织的访问权限。 例如，如果来宾离开其组织，则你无需执行任何操作，他们会自动失去对你在 Azure AD 中与他们共享的任何资源的访问权限。 有关 B2B 协作的详细信息，请参阅[什么是 Azure Active Directory B2B 中的来宾用户访问权限？](../external-identities/what-is-b2b.md)。

![显示了如何在来宾用户的本目录中对其进行身份验证的关系图](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="next-steps"></a>后续步骤

- [使用 Privileged Identity Management 的许可要求](subscription-requirements.md)
- [确保 Azure AD 中混合部署和云部署的特权访问安全性](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [部署 Privileged Identity Management](pim-deployment-plan.md)
