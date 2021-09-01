---
title: 通过 Azure Sentinel 保护 Azure AD B2C
titleSuffix: Azure AD B2C
description: 本教程演示如何通过 Azure Sentinel 对 Azure AD B2C 进行安全性分析。
services: active-directory-b2c
author: agabrielle
manager: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: agher
ms.subservice: B2C
ms.date: 07/19/2021
ms.openlocfilehash: a405cb97a021c05a3b0c6aa004d3f92ce4657d24
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178071"
---
# <a name="tutorial-how-to-perform-security-analytics-for-azure-ad-b2c-data-with-azure-sentinel"></a>教程：如何通过 Azure Sentinel 对 Azure AD B2C 数据进行安全性分析

可以通过将日志和审核信息路由至 Azure Sentinel，进一步保护 Azure AD B2C 环境安全。 Azure Sentinel 是云原生安全信息事件管理 (SIEM) 和安全业务流程自动响应 (SOAR) 解决方案。 Azure Sentinel 为 Azure AD B2C 提供了警报检测、威胁可见性、主动搜寻和威胁响应等功能。

通过结合使用 Azure Sentinel 与 Azure AD B2C，可以完成以下操作：

- 使用 Microsoft 的分析和出色的威胁情报检测以前未检测到的威胁，并最大限度地减少误报。
- 借助人工智能调查威胁，结合 Microsoft 多年以来的网络安全工作经验大规模搜寻可疑活动。
- 通过内置的业务流程和常见任务自动化快速响应事件。
- 满足组织的安全性和合规性要求。

在本教程中，你将学习到：

1. 如何将 B2C 日志传输至 Azure Monitor 日志工作区。
1. 在 Log Analytics 工作区上启用 Azure Sentinel。
1. 在 Sentinel 中创建能够触发事件的示例规则。
1. 最后，配置一些自动响应。

## <a name="configure-aad-b2c-with-azure-monitor-logs-analytics"></a>通过 Azure Monitor Logs Analytics 对 AAD B2C 进行配置

接下来的步骤将在以下过程中完成，即在 Azure AD B2C 租户内的 Azure Active Directory 中启用诊断设置。
诊断设置定义要将资源的日志和指标发送到的位置。

按照[使用 Azure Monitor 监控 Azure AD B2C 环境](./azure-monitor.md)的步骤 1 到 5 来配置 Azure AD B2C，以将日志发送到 Azure Monitor。

## <a name="deploy-an-azure-sentinel-instance"></a>部署 Azure Sentinel 实例

> [!IMPORTANT]
> 若要启用 Azure Sentinel，需要获取 Azure Sentinel 工作区所在订阅的参与者权限。 若要使用 Azure Sentinel，需要获取工作区所属资源组的“参与者”或“读取者”权限。

在将 Azure AD B2C 来配置为向 Azure Monitor 发送日志后，就需要启用 Azure Sentinel 实例。

1. 登录到 Azure 门户。 确保选择了在上一步中创建的 LA（日志分析）工作区的订阅。

2. 搜索“Azure Sentinel”并将其选中。

3. 选择 **添加**。

   :::image type="content" source="./media/azure-sentinel/azure-sentinel-add.png" alt-text="在 Azure 门户搜索 Azure Sentinel":::

4. 选择在上一步骤中使用的工作区。

   :::image type="content" source="./media/azure-sentinel/create-new-workspace.png" alt-text="选择 sentinel 工作区":::

5. 选择“添加 Azure Sentinel”。

   > [!NOTE]
   > 可以在多个工作区上运行 Azure Sentinel，但将数据隔离到单个工作区。 有关启用 Sentinel 的其他详细信息，请参阅此[快速入门](../sentinel/quickstart-onboard.md)。

## <a name="create-a-sentinel-rule"></a>创建 sentinel 规则

> [!NOTE]
> Azure Sentinel 提供即开即用的内置模板，帮助你创建由 Microsoft 安全专家和分析师团队设计的威胁检测规则。 创建自这些模板的规则将自动搜索数据，检测任何可疑活动。 由于当前没有本机 Azure AD B2C，因此我们不会在示例中使用本机规则。 在本教程中，我们将创建自己的规则。

启用 Sentinel 后，你会希望在 B2C 租户中发生可疑情况时收到通知。

可以创建自定义分析规则，帮助发现环境中存在的威胁和异常行为。 这些规则将搜索特定事件或事件集，在达到特定事件阈值或条件时发出警报，然后为进一步调查生成事件。

> [!NOTE]
> 有关分析规则的详细评审，请参阅此[教程](/azure/active-directory-b2c/articles/sentinel/detect-threats-custom.md)。

在我们的方案中，我们希望在有人尝试强制访问我们的环境但访问不成功时收到通知，这可能意味着暴力攻击，我们希望在 60 秒内收到 2 次或 2 次以上登录失败的通知

1. 从 Azure Sentinel 导航菜单中，选择“分析”。
2. 在顶部的操作栏中，选择“+创建”，然后选择“计划查询规则”。 这样会打开“分析规则向导”。

   :::image type="content" source="./media/azure-sentinel/create-scheduled-rule.png" alt-text="选择创建计划查询规则":::

3. 分析规则向导 -“常规”选项卡

   - 提供唯一的名称和说明
     - 名称：B2C 未成功登录；说明：在 60 秒内出现两个或多个未成功登录时进行通知
   - 在“策略”字段中，可以从攻击类别中进行选择，对规则进行分类。 这些类别基于 [MITRE ATT&CK](https://attack.mitre.org/) 框架的策略生成。

     - 在示例中，我们将选择 PreAttack

       > [!Tip]
       > MITRE ATT&CK® 是基于现实认知的攻击者策略和技术知识库，支持全球访问。 将 ATT&CK 知识库用作开发特定威胁模型和方法的基础。

   - 根据需要设置警报“严重性”。
     - 由于这是我们的第一条规则，我们将选择“高”。 我们可在稍后更改规则
   - 在你创建规则时，默认情况下规则的“状态”为“已启用”，这表示当你完成创建后，它将立即运行。 如果你不需要让此规则立即运行，请选择“已禁用”，这样就会将它添加到“可用规则”选项卡中，当你需要运行此规则时，可从该选项卡中启用它。

     :::image type="content" source="./media/azure-sentinel/create-new-rule.png" alt-text="提供基本规则属性":::

4. 定义规则查询逻辑并配置设置。

   在设置规则逻辑选项卡中，我们将在规则查询字段中直接写入一个查询。 当 B2C 租户在 60 秒之内有两个或多个未成功登录时，此查询将发出警报，并且将按 UserPrincipalName 进行组织

   ```kusto
   SigninLogs
   | where ResultType != "0"
   | summarize Count = count() by bin(TimeGenerated, 60s), UserPrincipalName
   | project Count = toint(Count), UserPrincipalName
   | where Count >= 1
   ```

   :::image type="content" source="./media/azure-sentinel/rule-query.png" alt-text="在逻辑选项卡中输入规则查询":::

   在“查询计划”部分中，设置以下参数：

   :::image type="content" source="./media/azure-sentinel/query-scheduling.png" alt-text="设置查询计划参数":::

5. 在事件设置（预览）和自动响应中点击“下一步”。 稍后将配置并添加自动响应。

6. 点击“下一步”，转至“审阅和创建”，以审阅新警报规则的所有设置。 当系统显示“验证通过”消息时，选择“创建”，将警报规则初始化。

   :::image type="content" source="./media/azure-sentinel/review-create.png" alt-text="审阅和创建规则":::

7. 查看规则及其生成的事件。

   可在主“分析”屏幕上的“可用规则”选项卡下的表格中找到新创建的自定义规则（类型为“已计划”）。 你可以从此列表中编辑、启用、禁用或删除规则。

   :::image type="content" source="./media/azure-sentinel/rule-crud.png" alt-text="显示编辑、启用、禁用或删除规则的选项的分析屏幕":::

   若要查看新 B2C 未成功登录规则的结果，请转到事件页面，可以从此页面中会审、调查并修正威胁。

   一个事件可以包含多个警报。 它是用于特定调查的所有相关证据的聚合。 可以在事件级别中设置严重性和状态等属性。

   > [!NOTE]
   > 有关事件调查的详细评审，请参阅此[教程](/azure/active-directory-b2c/articles/sentinel/investigate-cases.md)

   若要开始调查，请选择一个特定的事件。 在右侧，可以查看此事件的详细信息，包括其严重性、涉及的实体、触发此事件的原始事件以及事件的唯一 ID。

   :::image type="content" source="./media/azure-sentinel/select-incident.png" alt-text="事件屏幕":::

   若要详细查看事件中的警报和实体，请在“事件”页中选择“查看完整详细信息”，并查看汇总事件信息的相关选项卡

   :::image type="content" source="./media/azure-sentinel/full-details.png" alt-text="规则 73":::

   若要进一步审阅有管此事件的详细信息，可以选择证据->事件或事件->到 Log Analytics 的链接

   结果将会显示试图记录尝试次数的标识的 UserPrincipalName。

   :::image type="content" source="./media/azure-sentinel/logs.png" alt-text="所选事件的详细信息":::

## <a name="automated-response"></a>自动响应

Azure Sentinel 还提供可靠的 SOAR 功能;有关其他信息，请参阅[此处](../sentinel/automation-in-azure-sentinel.md)的官方 Sentinel 文档。

可以将自动操作（在 Sentinel 中被称为 playbook）附加到 Analytics 规则中，以满足你的要求。

在示例中，我们将根据规则创建的事件添加电子邮件通知。

为了完成任务，我们将使用 Sentinel GitHub 存储库 [Incident-Email-Notification](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification) 中现有的 Playbook

配置 Playbook 后，只需编辑现有规则，并将 Playbook 选进自动化选项卡：

:::image type="content" source="./media/azure-sentinel/automation-tab.png" alt-text="与规则关联的自动响应的配置屏幕":::

## <a name="next-steps"></a>后续步骤

- 由于不存在完美的规则，因此你可以根据需要更新规则查询以排除误报。 有关详细信息，请参阅[在 Azure Sentinel 中处理误报](../sentinel/false-positives.md)

- 若要帮助分析数据以及创建丰富视觉对象报表，请从基于数据显示见解的由专家创建的工作簿库中选择并下载。 可以轻松地自定义[这些工作簿](https://github.com/azure-ad-b2c/siem#workbooks)来满足你的需要。

- 若要详细了解 Sentinel，请参阅 [Azure Sentinel 文档](../sentinel/index.yml)