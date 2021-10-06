---
title: 如何规划 Privileged Identity Management 部署？ - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD 组织中部署 Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: BarbaraSelden
manager: martinco
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: baselden
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e00d6d6486ea5977a38e5e860f2bcd28dbe749fb
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230107"
---
# <a name="plan-a-privileged-identity-management-deployment"></a>规划 Privileged Identity Management 部署

Privileged Identity Management (PIM) 提供基于时间且基于审批的角色激活，用于缓解重要资源上出现的访问权限过度、不必要或滥用的风险。 这些资源包括 Azure Active Directory (Azure AD)、Azure 和其他 Microsoft Online Services（例如 Microsoft 365 或 Microsoft Intune）中的资源。

PIM 使你能够在特定范围内允许一组特定的操作。 主要功能包括：

* 提供对资源的实时特权访问

* 分配特权访问组的成员资格或所有权

* 使用开始和结束日期分配对资源的 **限时** 访问权限

* 要求获得 **批准** 才能激活特权角色

* 强制要求在激活任何角色时执行多重身份验证

* 使用 **理由** 来了解用户激活角色的原因

* 激活特权角色时获取 **通知**

* 开展 **访问评审**，以确保用户仍然需要角色

* 下载 **审核历史记录** 进行内部或外部审核

若要充分利用此部署计划，必须全面了解[什么是 Privileged Identity Management](pim-configure.md)。

## <a name="understand-pim"></a>了解 PIM

本部分中的 PIM 概念将帮助你了解组织的特权标识要求。

### <a name="what-can-you-manage-in-pim"></a>可在 PIM 中管理的内容

目前，PIM 可用于管理：

* **Azure AD 角色** – 有时称为目录角色，Azure AD 角色包括用于管理 Azure AD 和其他 Microsoft 365 Online Services 的内置和自定义角色。

* **Azure 角色** – Azure 中基于角色的访问控制 (RBAC) 角色，可授予对管理组、订阅、资源组和资源的访问权限。

* **特权访问组** – 用于向 Azure AD 安全组的成员和所有者角色设置实时访问权限。 特权访问组不仅为你提供一种为 Azure AD 角色和 Azure 角色设置 PIM 的替代方法，而且还允许你在 Microsoft Online Services（如 Intune、Azure Key Vault 和 Azure 信息保护）中为其他权限设置 PIM。 

可以将以下用户或组分配到这些角色或组： 

* 用户 - 用于获取对 Azure AD 角色、Azure 角色和特权访问组的实时访问权限。 

* 组 - 组中的任何人都可以实时访问 Azure AD 角色和 Azure 角色。 对于 Azure AD 角色，组必须是标记为可分配给某个角色的新建云组，而对于 Azure 角色，组可以是任何 Azure AD 安全组。 建议不要将组分配/嵌套到特权访问组。 

> [!NOTE] 
>不能将服务主体分配为符合 Azure AD 角色、Azure 角色和特权访问组的条件，但可以授予有时间限制的活动分配到这所有三者。

### <a name="principle-of-least-privilege"></a>最低权限原则

为用户分配具有[执行其任务所需的最低特权](../roles/delegate-by-task.md)的角色。 此做法会尽量减少全局管理员的数目，并使用适合特定方案的特定管理员角色。

> [!NOTE] 
> Microsoft 的全局管理员非常少。 在 [Microsoft 如何使用 Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access) 中了解详细信息。

### <a name="type-of-assignments"></a>分配类型 

有两种类型的分配 - 符合条件和活动。 如果用户符合某个角色的条件，则意味着他们在需要执行特权任务时可以激活该角色。 

还可以为每种类型的分配设置开始时间和结束时间。 这样一来，你就有了四种可能的分配类型：

* 永久符合条件

* 永久活动

* 限时符合条件，具有指定的分配开始和结束日期

* 限时活动，具有指定的分配开始和结束日期

如果角色过期，你可以延长或续订这些分配。 

建议不要为角色进行永久活动类型分配，但推荐的[两个不受限紧急访问帐户](../roles/security-emergency-access.md)除外，这两个帐户应具有永久的全局管理员角色。 

## <a name="plan-the-project"></a>规划项目

如果技术项目失败，通常是由于在影响、结果和责任方面不符合预期而导致的。 若要避免这些问题，[请确保吸引适当的利益干系人](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)并充分了解项目中的利益干系人角色。

### <a name="plan-a-pilot"></a>规划试点

在部署的每个阶段，请确保评估结果符合预期。 请参阅[关于试点的最佳做法](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)。

* 从少量的用户（试点组）着手，验证 PIM 的行为是否符合预期。

* 验证为角色或特权访问组设置的所有配置是否均正常工作。 

* 只有经过全面测试后才能将其投入生产。 

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功都至关重要。 主动与用户交流他们的体验将如何变化、何时会变化以及在遇到问题时如何获取支持。

安排时间与内部 IT 支持人员一起演练完成 PIM 工作流。 向他们提供适当的文档和你的联系信息。

## <a name="plan-testing-and-rollback"></a>规划测试和回滚

> [!NOTE] 
> 对于 Azure AD 角色，组织通常首先测试和推出全局管理员，而对于 Azure 资源，他们通常一次测试一个 Azure 订阅的 PIM。 

### <a name="plan-testing"></a>规划测试

创建测试用户，以验证 PIM 设置的工作方式是否符合预期，避免其影响实际用户并潜在性地干扰他们对应用和资源的访问。 生成测试计划，以在预期结果与实际结果之间进行比较。 

下表所示为测试用例示例： 

| 角色| 激活期间的预期行为| 实际结果 |
| --- | --- | --- |
|全局管理员角色| <li> 要求 MFA <br><li>  需要审批 <br><li>  审批者收到通知，并且可以批准 <br><li>  角色在预设时间后过期|

对于 Azure AD 和 Azure 资源角色，请确保你有要担任这些角色的用户。 此外，在临时环境中测试 PIM 时，请考虑以下角色：

| 角色| Azure AD 角色| Azure 资源角色| 特权访问组 |
| --- | --- | --- |--- |
| 组成员| | | x |
| 角色成员| x| x|  |
| IT 服务所有者| x| | x |
| 订阅或资源所有者| | x| x |
| 特权访问组所有者| | | x |

### <a name="plan-rollback"></a>规划回滚

如果 PIM 在生产环境中无法按预期工作，你可以再次将角色分配从“符合条件”更改为“活动”。 对于每个已配置的角色，为分配类型为“符合条件”的所有用户选择省略号 (...)。 然后，可以选择“设为活动”选项以便回来，将角色分配设置为“活动”。

## <a name="plan-and-implement-pim-for-azure-ad-roles"></a>为 Azure AD 角色规划和实施 PIM

按照以下任务准备 PIM，以管理 Azure AD 角色。 

### <a name="discover-and-mitigate-privileged-roles"></a>发现特权角色并减轻其风险

列出组织中有特权角色的人员。 查看分配的用户，确定不再需要该角色的管理员，并将其从分配中删除。 

可以使用 [Azure AD 角色访问评审](./pim-create-azure-ad-roles-and-resource-roles-review.md)来自动发现、评审、批准或删除分配。

### <a name="determine-roles-to-be-managed-by-pim"></a>确定要由 PIM 管理的角色

优先保护具有最多权限的 Azure AD 角色。 此外，请务必考虑哪些数据和权限对组织而言最为敏感。 

首先，确保所有全局和安全管理员角色都是使用 PIM 进行管理的，因为他们是受到攻击时可以造成最大损害的用户。 然后，考虑可能容易受到攻击而应加以管理的更多角色。

### <a name="configure-pim-settings-for-azure-ad-roles"></a>为 Azure AD 角色配置 PIM 设置

为组织使用的每个特权 Azure AD 角色[制定和配置 PIM 设置](pim-how-to-change-default-settings.md)。 

下表所示为设置示例：

| 角色| 要求 MFA| 通知| 事件工单| 需要审批| 审批者| 激活持续时间| 永久管理员 |
| --- | --- | --- |--- |--- |--- |--- |--- |
| 全局管理员| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| 其他全局管理员| 1 小时| 紧急访问帐户 |
| Exchange 管理员| :heavy_check_mark:| :heavy_check_mark:| :x:| :x:| 无| 2 小时| 无 |
| 支持管理员| :x:| :x:| :heavy_check_mark:| :x:| 无| 8 小时| 无 |


### <a name="assign-and-activate-azure-ad-roles"></a>分配和激活 Azure AD 角色 

对于 PIM 中的 Azure AD 角色，只有具有“特权角色管理员”或“全局管理员”角色的用户才能管理其他管理员的分配。 全局管理员、安全管理员、全局读取者和安全读取者也可在 PIM 中查看 Azure AD 角色的分配。 

请按以下链接中的说明进行操作：

1.[提供符合条件的分配](pim-how-to-add-role-to-user.md)。

2.[允许符合条件的用户实时激活 Azure AD 角色](pim-how-to-activate-role.md)

当角色即将到期时，请使用 [PIM 来延长或续订角色](pim-resource-roles-renew-extend.md)。 这两种用户启动的操作都需要全局管理员或特权角色管理员批准。

当 Azure AD 角色中发生这些重要事件时，PIM 会根据角色、事件和通知设置，向特权管理员[发送电子邮件通知和每周摘要电子邮件](pim-email-notifications.md)。 这些电子邮件可能还包含指向相关任务的链接，比如激活或续订角色。 

> [!NOTE] 
>你还可以[使用适用于 Azure AD 角色的 Microsoft Graph API](pim-apis.md) 来执行这些 PIM 任务。 

### <a name="approve-or-deny-pim-activation-requests"></a>批准或拒绝 PIM 激活请求 

有请求等待审批时，委派审批者会收到一封电子邮件通知。 请按照以下步骤[批准或拒绝关于激活 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)。

### <a name="view-audit-history-for-azure-ad-roles"></a>查看 Azure AD 角色的审核历史记录

查看 Azure AD 角色过去 30 天内[所有角色分配和激活操作的审核历史记录](pim-how-to-use-audit-log.md)。 全局管理员或特权角色管理员可以访问审核日志。 

建议至少让一名管理员每周阅读所有审核事件，并按月导出审核事件。

### <a name="security-alerts-for-azure-ad-roles"></a>Azure AD 角色的安全警报

[为 Azure AD 角色配置安全警报](pim-how-to-configure-security-alerts.md)，以在发生可疑和不安全活动时触发警报。

## <a name="plan-and-implement-pim-for-azure-resource-roles"></a>为 Azure 资源角色规划和实施 PIM

按照以下任务准备 PIM，以管理 Azure 资源角色。

### <a name="discover-and-mitigate-privileged-roles"></a>发现特权角色并减轻其风险

最大程度地减少附加到每个订阅或资源的所有者和用户访问管理员分配量，以及删除不必要的分配。

作为全局管理员，你可以[提升访问权限以管理所有 Azure 订阅](../../role-based-access-control/elevate-access-global-admin.md)。 然后可以找到每个订阅的所有者，与其协作，删除订阅内不必要的分配。

使用 [Azure 资源访问评审](./pim-create-azure-ad-roles-and-resource-roles-review.md)来审核和删除不必要的角色分配。 

### <a name="determine-roles-to-be-managed-by-pim"></a>确定要由 PIM 管理的角色

在决定应使用 PIM 为 Azure 资源管理哪些角色分配时，必须首先确定对组织而言最重要的[管理组](../../governance/management-groups/overview.md)、订阅、资源组和资源。 请考虑使用管理组来整理其组织内的所有资源。

建议使用 PIM 管理所有订阅所有者和用户访问管理员角色。 

与订阅所有者合作，记录每个订阅管理的资源，并对每个资源（如果受到威胁）的风险级别进行分类。 根据风险级别，优先使用 PIM 管理资源。 这还包括附加到订阅的自定义资源。

此外，建议与关键服务的订阅或资源所有者合作，为敏感订阅或资源中的所有角色设置 PIM 工作流。

对于非关键的订阅或资源，不需要为所有角色设置 PIM。 但是，仍应使用 PIM 保护所有者和用户访问管理员角色。

### <a name="configure-pim-settings-for-azure-resource-roles"></a>为 Azure 资源角色配置 PIM 设置

为计划使用 PIM 保护的 Azure 资源角色[制定和配置设置](pim-resource-roles-configure-role-settings.md)。 

下表所示为设置示例：

| 角色| 要求 MFA| 通知| 需要审批| 审批者| 激活持续时间| 活动管理员| 活动期限| 合格期限|
| --- | --- | --- |--- |--- |--- |--- |---|---|
| 关键订阅的所有者| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| 订阅的其他所有者| 1 小时| 无| 不适用| 3 个月 |
| 次要订阅的用户访问管理员| :heavy_check_mark:| :heavy_check_mark:| :x:| 无| 1 小时| 无| 不适用| 3 个月 |

### <a name="assign-and-activate-azure-resource-role"></a>分配和激活 Azure 资源角色

对于 PIM 中的 Azure 资源角色，只有所有者或用户访问管理员才能管理其他管理员的分配。 默认情况下，充当特权角色管理员、安全管理员或安全读取者的用户无权查看 Azure 资源角色的分配。

请按以下链接中的说明进行操作：

1.[提供符合条件的分配](pim-resource-roles-assign-roles.md)

2.[允许符合条件的用户实时激活 Azure 角色](pim-resource-roles-activate-your-roles.md)

当特权角色分配即将到期时，请[使用 PIM 来延长或续订角色](pim-resource-roles-renew-extend.md)。 这两种用户启动的操作都需要资源所有者或用户访问管理员批准。 

当 Azure 资源角色中发生这些重要事件时，PIM 会向所有者和用户访问管理员发送[电子邮件通知](pim-email-notifications.md)。 这些电子邮件可能还包含指向相关任务的链接，比如激活或续订角色。

>[!NOTE]
>你还可以[使用适用于 Azure 资源角色的 Microsoft Azure 资源管理器 API](pim-apis.md)来执行这些 PIM 任务。 

### <a name="approve-or-deny-pim-activation-requests"></a>批准或拒绝 PIM 激活请求

[批准或拒绝 Azure AD 角色的激活请求](azure-ad-pim-approval-workflow.md) - 有请求等待审批时，委派审批者会收到一封电子邮件通知。

### <a name="view-audit-history-for-azure-resource-roles"></a>查看 Azure 资源角色的审核历史记录

查看 Azure 资源角色过去 30 天内[所有分配和激活操作的审核历史记录](azure-pim-resource-rbac.md)。

### <a name="security-alerts-for-azure-resource-roles"></a>Azure 资源角色的安全警报

[为 Azure 资源角色配置安全警报](pim-resource-roles-configure-alerts.md)，以在发生任何可疑和不安全活动时触发警报。

## <a name="plan-and-implement-pim-for-privileged-access-groups"></a>为特权访问组规划和实施 PIM

按照以下任务准备 PIM，以管理特权访问组。

### <a name="discover-privileged-access-groups"></a>发现特权访问组

可能存在这样一种情况，一个人通过 PIM 具有五到六个有效分配的 Azure AD 角色。 他们将不得不单独激活每个角色，这会降低工作效率。 更糟糕的是，他们还可能为这些角色分配数十或数百个 Azure 资源，这会使问题更加严重。

在这种情况下，应使用特权访问组。 创建特权访问组，并向其授予对多个角色的永久活动访问权限。 请参阅[特权访问组管理功能](groups-features.md)。

若要将可分配 Azure AD 角色的组作为特权访问组进行管理，必须[在 PIM 中管理它](groups-discover-groups.md)。

### <a name="configure-pim-settings-for-privileged-access-groups"></a>为特权访问组配置 PIM 设置

为计划使用 PIM 保护的特权访问组[制定和配置设置](groups-role-settings.md)。

下表所示为设置示例：

| 角色| 要求 MFA| 通知| 需要审批| 审批者| 激活持续时间| 活动管理员| 活动期限| 合格期限 |
| --- | --- | --- |--- |--- |--- |--- |---|---|
| 所有者| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| 资源的其他所有者| 1 小时| 无| 不适用| 3 个月 |
| 成员| :heavy_check_mark:| :heavy_check_mark:| :x:| 无| 5 小时| 无| 不适用| 3 个月 |

### <a name="assign-eligibility-for-privileged-access-groups"></a>为特权访问组分配资格

可以[为特权访问组的成员或所有者分配资格](groups-assign-member-owner.md)。 只需一次激活，他们就可访问所有链接的资源。 

>[!NOTE] 
>可以按照向用户分配角色的方式，将特权组分配给一个或多个 Azure AD 和 Azure 资源角色。 在单个 Azure AD 组织（租户）中最多可以创建 250 个可分配角色的组。

![为特权访问组分配资格](media/pim-deployment-plan/privileged-access-groups.png)


当特权组分配即将到期时，请使用 [PIM 来延长或续订组分配](groups-renew-extend.md)。 需要获得组所有者的批准。

### <a name="approve-or-deny-pim-activation-request"></a>批准或拒绝 PIM 激活请求

将特权访问组成员和所有者配置为需要批准才能激活，并从 Azure AD 组织中选择用户或组作为委派审批者。 我们建议为每个组选择两个或更多审批者，以减少特权角色管理员的工作量。 

[批准或拒绝特权访问组的角色激活请求](groups-approval-workflow.md)。 有请求等待审批时，委派审批者会收到一封电子邮件通知。

### <a name="view-audit-history-for-privileged-access-groups"></a>查看特权访问组的审核历史记录

查看特权访问组过去 30 天内[所有分配和激活操作的审核历史记录](groups-audit.md)。

## <a name="next-steps"></a>后续步骤

* 如果存在与 PIM 相关的问题，请参阅[排查 PIM 问题](pim-troubleshoot.md)。

* [部署其他标识功能](../fundamentals/active-directory-deployment-plans.md)

