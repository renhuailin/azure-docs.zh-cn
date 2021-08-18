---
title: 使用安全策略 | Microsoft Docs
description: 本文介绍如何使用 Azure 安全中心的安全策略。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: memildin
ms.openlocfilehash: b7c8003bd40df21fb749fcb576afeaec5dacead9
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112236834"
---
# <a name="manage-security-policies"></a>管理安全策略

本文介绍如何配置安全策略，以及如何在安全中心查看这些策略。 

若要了解计划、策略和建议之间的关系，请参阅[什么是安全策略、计划和建议？](security-policy-concept.md)

## <a name="who-can-edit-security-policies"></a>谁可以编辑安全策略？

安全中心使用 Azure 基于角色的访问控制 (Azure RBAC)，后者提供可分配到 Azure 用户、组和服务的内置角色。 用户打开安全中心时，只能看到与他们可访问的资源相关的信息。 这意味着，已为用户分配了资源订阅的所有者、参与者或读取者角色。   还有两个特定的安全中心角色：

- **安全读取者**：有权查看安全中心项，例如建议、警报、策略和运行状况。 无法执行更改。
- **安全管理员**：与安全读取者具有相同的查看权限。 还可以更新安全策略并消除警报。

你可以在 Azure Policy 门户中通过 REST API 或 Windows PowerShell 编辑安全策略。

## <a name="manage-your-security-policies"></a>管理安全策略

要在安全中心内查看安全策略，请执行以下操作：

1. 在“安全中心”仪表板中，选择“安全策略” 。

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="策略管理页面。":::

   在“策略管理”屏幕中，可以看到管理组数、订阅数、工作区数以及管理组结构。

1. 选择想要查看其策略的订阅或管理组。

1. 此时会显示该订阅或管理组的安全策略页。 其中显示了可用和已分配的策略。

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="安全中心的“安全策略”页" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > 如果默认策略旁边有一个标签“MG 已继承”，则表示该策略已分配到某个管理组，并已由当前你正在查看的订阅继承。

1. 从此页提供的可用选项中进行选择：

    1. 要使用行业标准，请选择“添加更多标准”。 有关详细信息，请参阅[在监管合规仪表板中自定义标准集](update-regulatory-compliance-packages.md)。

    1. 要分配和管理自定义计划，请选择“添加自定义计划”。 有关详细信息，请参阅[使用自定义安全计划和策略](custom-security-policies.md)。

    1. 要查看并编辑默认计划，请选择“查看有效策略”并按如下所述继续操作。 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="有效策略屏幕。":::

       此“安全策略”屏幕反映在所选订阅或管理组中分配的策略所执行的操作。
       
       * 使用顶部的链接打开在订阅或管理组中应用的每个策略 **分配**。 可以使用这些链接访问分配，以及编辑或禁用策略。 例如，如果你发现特定的策略分配正在有效地拒绝终结点保护，可使用该链接来编辑或禁用该策略。
       
       * 在策略列表中，可以看到策略有效应用于订阅或管理组。 将考虑适用于该范围的每个策略的设置，并显示该策略执行的操作的累积效果。 例如，如果一个分配禁用此策略，而另一个设置为 AuditIfNotExist，则累积效果将应用 AuditIfNotExist。 更积极的效果始终优先。
       
       * 策略的效果可以是：追加、审核、AuditIfNotExists、拒绝、DeployIfNotExists 和禁用。 有关如何应用效果的详细信息，请参阅[了解策略效果](../governance/policy/concepts/effects.md)。

       > [!NOTE]
       > 查看已分配的策略时，可以看到多个分配并且可以看到每个分配如何自行配置。


## <a name="disable-security-policies-and-disable-recommendations"></a>禁用安全策略和禁用建议

如果安全计划触发与环境无关的建议，你可以阻止该建议再次出现。 若要禁用建议，请禁用生成该建议的特定策略。

如果已经使用安全中心的合规工具应用了法规标准，而你想要禁用的建议是这项法规标准所要求的，那么，你想要禁用的建议仍将会出现。 如果该建议对于合规性来说是必要的，那么，即使已在内置计划中禁用了策略，法规标准计划中的策略仍将触发该建议。 你无法禁用法规标准计划中的策略。

有关建议的详细信息，请参阅[管理安全建议](security-center-recommendations.md)。

1. 在安全中心的“策略和符合性”部分，选择“安全策略” 。

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="在 Azure 安全中心启动策略管理过程。":::

1. 选择要禁用其建议（和策略）的订阅或管理组。

   > [!NOTE]
   > 请牢记，管理组会将其策略应用到其订阅。 因此，如果禁用了某个订阅策略，而该订阅属于仍使用同一策略的管理组，则你将继续收到策略建议。 仍将从管理级别应用该策略，且仍将生成建议。

1. 从“安全中心默认策略”、“行业和监管标准”或“你的自定义计划”部分选择包含要禁用的策略的相关计划  。

1. 打开“参数”部分，搜索会对你要禁用的建议进行调用的策略。

1. 从下拉列表中，将相应策略的值更改为“已禁用”。

   ![禁用策略。](./media/tutorial-security-policy/disable-policy.png)

1. 选择“保存”。

   > [!NOTE]
   > 此更改最多可能需要 12 小时才能生效。


## <a name="enable-a-security-policy"></a>启用安全策略

计划中的有些策略可能会处于默认禁用状态。 例如，在 Azure 安全基准计划中，提供的有些策略只在满足组织的特定法规或符合性要求的情况下启用。 此类策略包括使用客户管理的密钥加密静态数据的建议，例如“应该使用客户管理的密钥(CMK)加密容器注册表”。

若要启用已禁用的策略并确保针对你的资源评估该策略，请执行以下操作：

1. 在安全中心的“策略和符合性”部分，选择“安全策略” 。

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="在 Azure 安全中心启动策略管理过程。":::

1. 选择要启用其建议（和策略）的订阅或管理组。

1. 从“安全中心默认策略”、“行业和监管标准”或“你的自定义计划”部分选择包含要启用的策略的相关计划  。

1. 打开“参数”部分，搜索会对你要禁用的建议进行调用的策略。

1. 从下拉列表中，将相应策略的值更改为“AuditIfNotExists”或“Enforce” 。

1. 选择“保存”。

   > [!NOTE]
   > 此更改最多可能需要 12 小时才能生效。


## <a name="next-steps"></a>后续步骤
此页介绍了安全策略。 如需相关信息，请参阅以下页面：

- [了解如何使用 PowerShell 设置策略](../governance/policy/assign-policy-powershell.md)
- [了解如何在 Azure Policy 中编辑安全策略](../governance/policy/tutorials/create-and-manage.md)
- [了解如何使用 Azure Policy 跨订阅或在管理组上设置策略](../governance/policy/overview.md)
- [了解如何对管理组中的所有订阅启用安全中心](onboard-management-group.md)