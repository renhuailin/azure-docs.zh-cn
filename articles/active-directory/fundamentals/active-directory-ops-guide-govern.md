---
title: Azure Active Directory 治理操作参考指南
description: 本操作参考指南介绍了为确保治理管理安全而应采取的检查和操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 065b0ca66b2e8d253ae798ce672638877ddceef8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732659"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure Active Directory 治理操作参考指南

本 [Azure AD 操作参考指南](active-directory-ops-guide-intro.md)部分介绍了应采取的检查和操作，以便于你评估和证明已授予非特权标识和特权标识的权限、审核和控制环境更改。

> [!NOTE]
> 这些建议截至发布之日为最新，但可能会随着时间而改变。 组织应持续评估其治理实践，因为 Microsoft 产品和服务会不断发展。

## <a name="key-operational-processes"></a>关键操作过程

### <a name="assign-owners-to-key-tasks"></a>将所有者分配到关键任务

管理 Azure Active Directory 需要持续执行关键操作任务和过程，这可能并不属于一个推出项目。 设置这些任务对于优化环境仍非常重要。 关键任务及其建议所有者包括：

| 任务 | 所有者 |
| :- | :- |
| 在 SIEM 系统中执行 Azure AD 审核日志存档 | InfoSec 运营团队 |
| 发现管理不合规的应用程序 | IAM 运营团队 |
| 定期评审对应用程序的访问权限 | InfoSec 体系结构团队 |
| 定期评审对外部标识的访问权限 | InfoSec 体系结构团队 |
| 定期评审具有特权角色的人员 | InfoSec 体系结构团队 |
| 定义用于激活特权角色的安检门 | InfoSec 体系结构团队 |
| 定期查看同意授权 | InfoSec 体系结构团队 |
| 为组织中的员工设计基于应用程序和资源的目录和访问包 | 应用所有者 |
| 定义用于将用户分配给访问包的安全策略 | InfoSec 团队和应用所有者 |
| 如果策略包含审批工作流，则定期查看工作流审核 | 应用所有者 |
| 查看安全策略（如条件访问策略、使用访问评审）中的异常情况 | InfoSec 运营团队 |

查看列表时，可能会发现需要为缺少所有者的任务分配所有者，或调整其所有者与上述建议不符的任务的所有权。

#### <a name="owner-recommended-reading"></a>所有者方面的推荐阅读内容

- [在 Azure Active Directory 中分配管理员角色](../roles/permissions-reference.md)
- [Azure 中的监管](../../governance/index.yml)

### <a name="configuration-changes-testing"></a>配置更改测试

在进行测试时，从推出目标用户子集之类的简单技术，到在并行测试租户中部署更改，有一些更改需要特别注意。 如果尚未实施测试策略，则应按照下表中的准则定义测试方法：

| 方案| 建议 |
|-|-|
|将身份验证类型从联合更改为 PHS/PTA，反之亦然| 使用[分阶段推出](../hybrid/how-to-connect-staged-rollout.md)来测试更改身份验证类型的影响。|
|推出新的条件访问 (CA) 策略或标识保护策略|创建新的条件访问策略并将其分配给测试用户。|
|载入应用程序的测试环境|将应用程序添加到生产环境，从“MyApps”面板中隐藏此应用程序，并在质量保证 (QA) 阶段将其分配给测试用户。|
|更改同步规则|在采用生产环境当前同种配置的测试 Azure AD Connect 中执行更改（这也称为“暂存模式”），并分析 CSExport 结果。 如果满意，则在准备就绪时切换到生产环境。|
|更改品牌|在单独的测试租户中进行测试。|
|推出新功能|如果该功能支持向目标用户群推出，则确定试点用户并逐步推出。例如，自助式密码重置和多重身份验证可以面向特定用户或组。|
|将应用程序从本地标识提供者 (IdP)（例如 Active Directory）转换到 Azure AD|如果应用程序支持多个 IdP 配置（如 Salesforce），则在更改时段配置这两者并测试 Azure AD（如果应用程序生成 HRD 页面）。 如果应用程序无法支持多个 IdP，则在更改控制时段和程序停机期间规划测试。|
|更新动态组规则|使用新规则创建并行动态组。 与计算出的结果进行比较，例如，使用相同的条件运行 PowerShell。<br>如果测试通过，则切换到使用旧组的位置（如果可行）。|
|迁移产品许可证|请参阅[为 Azure Active Directory 许可组中单个用户更改许可证](../enterprise-users/licensing-groups-change-licenses.md)。|
|更改授权、颁发、MFA 等 AD FS 规则|通过组声明将用户子集作为目标。|
|更改 AD FS 身份验证体验或类似服务器场范围的更改|使用主机文件、NLB 路由规则或类似路由创建具有相同主机名的并行服务器场、实现配置更改、从客户端进行测试。<br>如果目标平台不支持主机文件（例如移动设备），则控制更改。|

## <a name="access-reviews"></a>访问评审

### <a name="access-reviews-to-applications"></a>对应用程序的访问评审

由于用户会进入各种团队并担任不同职位，随着时间推移，他们可能会积累一些对资源的访问权限。 资源所有者务必要定期评审对应用程序的访问权限，并删除用户声明周期内不再需要的权限。 Azure AD [访问评审](../governance/access-reviews-overview.md)可以使组织有效地管理组成员身份、对企业应用程序的访问权限，以及角色分配。 资源所有者应该定期评审用户的访问权限，确保仅相应人员能够持续进行访问。 理想情况下，应考虑使用 Azure AD 访问评审执行此任务。

![访问评审起始页](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> 与访问评审交互的每个用户都必须拥有付费 Azure AD Premium P2 许可证。

### <a name="access-reviews-to-external-identities"></a>对外部标识的访问评审

只允许外部标识在必要时访问所需资源，这一点非常重要。 使用 Azure AD [访问评审](../governance/access-reviews-overview.md)，针对所有外部标识和应用程序访问创建定期自动化访问评审过程。 如果此过程已在本地存在，则考虑使用 Azure AD 访问评审。 一旦应用程序停用或不再使用，则删除所有有权访问此应用程序的外部标识。

> [!NOTE]
> 与访问评审交互的每个用户都必须拥有付费 Azure AD Premium P2 许可证。

## <a name="privileged-account-management"></a>特权帐户管理

### <a name="privileged-account-usage"></a>特权帐户使用

黑客通常以管理员帐户和具有特权的其他元素为目标，以便快速获取对敏感数据和系统的访问权限。 由于具有特权角色的用户往往会随时间而累积，因此务必要定期评审和管理管理员权限，并提供对 Azure AD 和 Azure 资源的实时特权访问。

如果组织中不存在管理特权帐户的过程，或者当前让管理员使用其常规用户帐户来管理服务和资源，则应该立即开始使用单独的帐户，例如，一个帐户用于定期执行日常活动；另一个帐户用于进行特权访问并配置 MFA。 如果组织具有 Azure AD Premium P2 订阅，则最好立即部署 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md#license-requirements) (PIM)。 同样，还应评审这些特权帐户，并尽可能[分配较少的特权角色](../roles/security-planning.md)。

关于特权帐户管理，应实现的另一个方面是，手动或[通过 PIM](../privileged-identity-management/pim-perform-azure-ad-roles-and-resource-roles-review.md) 自动定义对这些帐户的[访问评审](../governance/access-reviews-overview.md)。

#### <a name="privileged-account-management-recommended-reading"></a>特权帐户管理方面的推荐阅读内容

- [Azure AD Privileged Identity Management 中的角色](../privileged-identity-management/pim-roles.md)

### <a name="emergency-access-accounts"></a>紧急访问帐户

组织必须创建[紧急帐户](../roles/security-emergency-access.md)，以便用于在出现下类身份验证中断时管理 Azure AD：

- 身份验证基础结构（AD FS、本地 AD、MFA 服务）的组件发生故障
- 管理员流失

应创建两个或更多个紧急帐户，并确保这些帐户实施并且符合 [Microsoft 最佳实践](../roles/security-planning.md)和[“打破玻璃”流程](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency)，以防止偶然因无法以管理员身份登录或激活现有个人用户帐户而不能进入租户。

### <a name="privileged-access-to-azure-ea-portal"></a>对 Azure EA 门户的特权访问

通过 [Azure 企业协议 (Azure EA) 门户](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/)，可以根据主企业协议创建 Azure 订阅，该协议在企业中的角色非常强大。 甚至在 Azure AD 准备就绪前就开始创建此门户，这种做法都很常见。因此必须要使用 Azure AD 锁定此门户，从此门户中删除个人帐户，确保委派适当，并缓解锁定带来的风险。

要知道，如果 EA 门户授权级别当前设置为“混合模式”，则必须从 EA 门户的所有特权访问中删除所有的 [Microsoft 帐户](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account)，并将 EA 门户配置为只使用 Azure AD 帐户。 如果未配置 EA 门户委派角色，还应为部门和帐户找到并实现委派角色。

#### <a name="privileged-access-recommended-reading"></a>特权访问方面的推荐阅读内容

- [Azure Active Directory 中的管理员角色权限](../roles/permissions-reference.md)

## <a name="entitlement-management"></a>权利管理

通过[权利管理 (EM)](../governance/entitlement-management-overview.md)，应用所有者可以绑定资源并将这些资源分配给组织中的特定角色（包括内部和外部角色）。 通过 EM，可以实现自助注册并委派给企业所有者，同时使用治理策略授予访问权限、设置访问持续时间、允许批准工作流。 

> [!NOTE]
> Azure AD 权利管理需要 Azure AD Premium P2 许可证。

## <a name="summary"></a>总结

确保标识治理安全包括八个方面。 此列表有助于你确定应采取的操作，以便于评估和证明已授予非特权标识和特权标识的权限、审核和控制环境更改。

- 将所有者分配到关键任务。
- 实施测试策略。
- 使用 Azure AD 访问评审有效管理组成员身份、对企业应用程序的访问权限以及角色分配。
- 针对所有外部标识类型和应用程序访问创建定期自动化访问评审过程。
- 建立访问评审过程，以定期评审和管理管理员权限，并提供对 Azure AD 和 Azure 资源的实时特权访问。
- 预配紧急帐户，以在出现意外中断时用于管理 Azure AD。
- 锁定对 Azure EA 门户的访问。
- 实现权利管理，以提供并管理对资源集合的访问权限。

## <a name="next-steps"></a>后续步骤

开始了解 [Azure AD 运营检查和操作](active-directory-ops-guide-ops.md)。