---
title: 使用 Azure Active Directory 条件访问管理外部访问
description: 如何使用 Azure Active Directory 条件访问策略来保护对资源的外部访问。
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
ms.openlocfilehash: 64209a4d9ca200c69783a4ae317b38beef8ded89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222303"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>使用条件访问策略管理外部访问 

[条件访问](../conditional-access/overview.md)是 Azure AD 用于以下用途的工具：将信号放置在一起、强制实施策略、确定是否允许用户访问资源。 若要详细了解如何创建和使用条件访问策略（条件访问策略），请参阅[计划条件访问部署](../conditional-access/plan-conditional-access.md)。 

![条件访问信号和决策的示意图](media/secure-external-access//7-conditional-access-signals.png)



本文讨论了如何向外部用户应用条件访问策略，并假设你无权访问[权利管理](../governance/entitlement-management-overview.md)功能。 条件访问策略可以与权利管理一起使用，事实也是如此。

在本文档集的前面部分，你[创建了一个安全计划](3-secure-access-plan.md)，其中概述了以下内容：

* 应用程序和资源具有相同的安全要求，可以组合在一起进行访问。

* 对外部用户的登录要求。

你将使用该计划创建用于外部访问的条件访问策略。 

> [!IMPORTANT]
> 创建几个外部用户测试帐户，以便在将所创建的策略应用于所有外部用户之前对其进行测试。

## <a name="conditional-access-policies-for-external-access"></a>用于外部访问的条件访问策略

下面是与使用条件访问策略治理外部访问相关的最佳做法。

* 如果你无法在权利管理中使用连接的组织，请为你使用的每个合作伙伴组织创建一个 Azure AD 安全组或 Microsoft 365 组。 将来自该合作伙伴的所有用户分配到该组。 然后，你可以在条件访问策略中使用这些组。

* 创建尽可能少的条件访问策略。 对于具有相同访问需求的应用程序，请将它们全都添加到同一策略中。  
‎ 
   > [!NOTE]
   > 条件访问策略可以应用于最多 250 个应用程序。 如果多于 250 个应用具有相同的访问需求，请创建重复的策略。 策略 A 将应用于应用 1-250，策略 B 将应用于应用 251-500，依此类推。

* 使用一个命名约定清楚地为特定于外部访问的策略命名。 一个命名约定是 ExternalAccess_actiontaken_AppGroup。 例如 ExternalAccess_Block_FinanceApps。

## <a name="block-all-external-users-from-resources"></a>阻止所有外部用户访问资源

你可以使用条件访问策略阻止外部用户访问特定的资源集。 确定要阻止访问的资源集后，创建一个策略。

若要创建一个阻止外部用户访问一组应用程序的策略，请执行以下操作：

1. 访问 Azure 门户，然后依次选择“Azure Active Directory”、“安全性”、“条件访问”。

2. 选择“新建策略”，并输入一个名称，例如 ExternalAccess_Block_FinanceApps

3. 选择“用户和组”。 在“包括”选项卡上，选择“选择用户和组”，然后选择“所有来宾和外部用户” 。 

4. 选择“排除”并输入你的管理员组和任何紧急访问（不受限）帐户。

5. 选择“云应用或操作”，选择“选择应用”，选择要阻止外部访问的所有应用，然后选择“选择”。

6. 选择“条件”，选择“位置”，在“配置”下选择“是”，并选择“任何位置”。

7. 在“访问控制”下，选择“授权”，将开关切换到“阻止”，然后选择“选择”。

8. 确保“启用策略”设置设定为“仅限报告”，然后选择“创建”。

## <a name="block-external-access-to-all-except-specific-external-users"></a>阻止除特定外部用户外的所有外部用户的访问

有时，你可能希望阻止除特定组外的外部用户。 例如，你可能希望在财务应用程序中阻止除那些为财务团队工作的用户之外的所有外部用户。 要执行此操作：

1. 创建一个安全组来保存应当访问财务组的外部用户。

2. 按照上述阻止从外部访问资源的步骤中的第 1-3 步进行操作。

3. 在第 4 步中，添加在财务应用程序中进行阻止时要排除的安全组。

4. 执行剩余步骤。

## <a name="implement-conditional-access"></a>实施条件访问

许多常用的条件访问策略已编制文档。 请参阅你可以针对外部用户进行调整的下列策略。

* [要求所有用户进行多重身份验证](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [基于用户风险的条件访问](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [要求来自不受信任的网络的访问进行多重身份验证](../conditional-access/untrusted-networks.md) 

* [需要使用条款](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出顺序执行这些操作。

1. [确定外部访问所需的安全状况](1-secure-access-posture.md)

2. [了解当前状况](2-secure-access-current-state.md)

3. [创建治理计划](3-secure-access-plan.md)

4. [使用组以确保安全性](4-secure-access-groups.md)

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [通过权利管理实现安全访问](6-secure-access-entitlement-managment.md)

7. [通过条件访问策略实现安全访问](7-secure-access-conditional-access.md)（本文）

8. [通过敏感度标签实现安全访问](8-secure-access-sensitivity-labels.md)

9. [实现对 Microsoft Teams、OneDrive 和 SharePoint 的安全访问](9-secure-access-teams-sharepoint.md)
