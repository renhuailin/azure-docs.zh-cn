---
title: 使用 Azure Active Directory 条件访问管理外部访问
description: 如何使用 Azure Active Directory 条件性访问策略来保护对资源的外部访问。
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
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222303"
---
# <a name="manage-external-access-with-conditional-access-policies"></a>使用条件性访问策略管理外部访问 

[条件性访问](../conditional-access/overview.md) 是 Azure AD 使用的工具，用于将信号组合在一起，并强制实施策略，并确定是否允许用户访问资源。 有关如何创建和使用条件性访问策略的详细信息 () 的条件性访问策略，请参阅 [规划条件性访问部署](../conditional-access/plan-conditional-access.md)。 

![条件性访问信号和决策示意图](media/secure-external-access//7-conditional-access-signals.png)



本文介绍如何将条件访问策略应用到外部用户，并假设你无权访问权限 [管理](../governance/entitlement-management-overview.md) 功能。 条件性访问策略可以与授权管理一起使用。

在本文档集的前面部分中，你 [创建了一个概述的安全计划](3-secure-access-plan.md) ：

* 应用程序和资源具有相同的安全要求，并可进行分组以进行访问。

* 外部用户的登录要求。

您将使用该计划创建用于外部访问的条件访问策略。 

> [!IMPORTANT]
> 创建几个外部用户测试帐户，以便您可以在将所创建的策略应用到所有外部用户之前对其进行测试。

## <a name="conditional-access-policies-for-external-access"></a>外部访问的条件性访问策略

下面是有关使用条件性访问策略管理外部访问权限的最佳实践。

* 如果无法在权利管理中使用连接的组织，请为你使用的每个合作伙伴组织创建一个 Azure AD 安全组或 Microsoft 365 组。 将该合作伙伴的所有用户分配到该组。 然后，你可以在条件访问策略中使用这些组。

* 创建尽可能少的条件性访问策略。 对于具有相同访问权限的应用程序，将它们全部添加到相同的策略。  
‎ 
   > [!NOTE]
   > 条件性访问策略最多可应用于250个应用程序。 如果超过250个应用具有相同的访问需求，请创建重复的策略。 策略 A 适用于应用1-250，策略 B 适用于应用251-500 等。

* 使用命名约定清楚地命名特定于外部访问的策略。 *ExternalAccess_actiontaken_AppGroup* 一种命名约定。 例如 ExternalAccess_Block_FinanceApps。

## <a name="block-all-external-users-from-resources"></a>阻止来自资源的所有外部用户

你可以阻止外部用户使用条件性访问策略访问特定的资源集。 确定要阻止访问的资源集后，请创建一个策略。

若要创建一个策略，阻止外部用户访问一组应用程序：

1. 访问 **Azure 门户**，选择 **Azure Active Directory**，选择 "**安全性**"，然后选择 " **条件访问**"。

2. 选择 " **新建策略**"，然后输入 **名称**，例如 ExternalAccess_Block_FinanceApps

3. 选择 " **用户和组**"。 在 "包括" 选项卡上，选择 " **选择用户和组**"，然后选择 " **所有来宾和外部用户**"。 

4. 选择 " **排除** "，并输入你的管理员组 (s) 以及任何紧急访问 ("防") 帐户。

5. 选择 " **云应用" 或 "操作**"，选择 " **选择应用**"，选择要阻止外部访问的所有应用，然后选择 " **选择**"。

6. 选择 **条件**，选择 **位置**，在 "配置" 下选择 **"是"**，然后选择 **任何位置**。

7. 在 "访问控制" 下，选择 " **授予**"，将切换切换为 " **阻止**"，然后选择 " **选择**"。

8. 确保将 "启用策略" 设置设置为 " **仅报告**"，然后选择 " **创建**"。

## <a name="block-external-access-to-all-except-specific-external-users"></a>阻止外部访问除特定外部用户之外的所有用户

有时，你可能希望阻止除特定组外的外部用户。 例如，你可能想要阻止来自财务应用程序的财务团队以外的所有外部用户。 要执行此操作：

1. 创建安全组以保存应访问财务组的外部用户。

2. 遵循上述资源中的 "阻止外部访问" 中的步骤1-3。

3. 在步骤4中，添加要从财务应用阻止的安全组。

4. 执行其余步骤。

## <a name="implement-conditional-access"></a>实现条件性访问

介绍了许多常见的条件性访问策略。 请参阅下面的，你可以为外部用户改编。

* [所有用户需要多重身份验证](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)

* [基于用户风险的条件访问](../conditional-access/howto-conditional-access-policy-risk-user.md)

* [对不受信任的网络进行访问时需要多重身份验证](../conditional-access/untrusted-networks.md) 

* [需要使用条款](../conditional-access/terms-of-use.md)

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何保护对资源的外部访问。 建议你按列出的顺序执行这些操作。

1. [确定外部访问所需的安全状况](1-secure-access-posture.md)

2. [发现当前状态](2-secure-access-current-state.md)

3. [创建调控计划](3-secure-access-plan.md)

4. [使用组进行安全保护](4-secure-access-groups.md)

5. [过渡到 Azure AD B2B](5-secure-access-b2b.md)

6. [使用权限管理的安全访问](6-secure-access-entitlement-managment.md)

7. [使用条件性访问策略进行安全访问](7-secure-access-conditional-access.md) (你在此处) 

8. [使用敏感度标签进行安全访问](8-secure-access-sensitivity-labels.md)

9. [安全访问 Microsoft 团队、OneDrive 和 SharePoint](9-secure-access-teams-sharepoint.md)
