---
title: 通过 Azure Sentinel 保护 Azure AD B2C
titleSuffix: Azure AD B2C
description: 教程：通过 Azure Sentinel 对 Azure Active Directory B2C 数据进行安全性分析
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ec68ca976b52c50c09bf86c90c56304f05051b66
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740261"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-azure-sentinel"></a>教程：通过 Azure Sentinel 配置 Azure Active Directory B2C 数据的安全性分析

可以通过将日志和审核信息路由至 Azure Sentinel，进一步保护 Azure Active Directory (AD) B2C 环境安全。 Azure Sentinel 是云原生安全信息事件管理 (SIEM) 和安全业务流程自动响应 (SOAR) 解决方案。 Azure Sentinel 为 Azure AD B2C 提供了警报检测、威胁可见性、主动搜寻和威胁响应等功能。

将 Azure Sentinel 用于 Azure AD B2C，可以：

- 使用 Microsoft 的分析和出色的威胁情报检测以前未检测到的威胁，并最大限度地减少误报。
- 通过人工智能调查威胁。 大规模搜寻可疑活动，利用 Microsoft 多年来的网络安全相关工作成果。
- 通过内置的业务流程和常见任务自动化快速响应事件。
- 满足组织的安全性和合规性要求。

在本教程中，将了解如何：

1. [将 Azure AD B2C 日志传输至 Azure Monitor 日志工作区](#configure-azure-ad-b2c-with-azure-monitor-logs-analytics)
2. [在 Log Analytics 工作区上启用 Azure Sentinel](#deploy-an-azure-sentinel-instance)
3. [在 Azure Sentinel 中创建能够触发事件的示例规则](#create-an-azure-sentinel-rule)
4. [配置自动响应](#automated-response)

## <a name="configure-azure-ad-b2c-with-azure-monitor-logs-analytics"></a>通过 Azure Monitor Logs Analytics 配置 Azure AD B2C

在Azure AD B2C 租户中启用 Azure AD 中的诊断设置，以定义应将资源的日志和指标发送到的位置。

然后，[将 Azure AD B2C 配置为将日志发送到 Azure Monitor](./azure-monitor.md)。

## <a name="deploy-an-azure-sentinel-instance"></a>部署 Azure Sentinel 实例

>[!IMPORTANT]
>若要启用 Azure Sentinel，需要获取 Azure Sentinel 工作区所在订阅的参与者权限。 若要使用 Azure Sentinel，需要获取工作区所属资源组的“参与者”或“读取者”权限。

在将 Azure AD B2C 来配置为向 Azure Monitor 发送日志后，就需要启用 Azure Sentinel 实例。

1. 转到 [Azure 门户](https://portal.azure.com)。 选择在其中创建了 Log Analytics 工作区的订阅。

2. 搜索“Azure Sentinel”并将其选中。

3. 选择 **添加** 。

![显示在 Azure 门户搜索 Azure Sentinel 的图像](./media/azure-sentinel/azure-sentinel-add.png)

4. 选择新工作区。

![选择 sentinel 工作区的图像](./media/azure-sentinel/create-new-workspace.png)

5. 选择“添加 Azure Sentinel”。

>[!NOTE]
>可以在多个工作区上[运行 Azure Sentinel](../sentinel/quickstart-onboard.md)，但将数据隔离到单个工作区。

## <a name="create-an-azure-sentinel-rule"></a>创建 Azure Sentinel 规则

>[!NOTE]
>Azure Sentinel 提供即开即用的内置模板，帮助你创建由 Microsoft 安全专家和分析师团队设计的威胁检测规则。 创建自这些模板的规则将自动搜索数据，检测任何可疑活动。 目前没有本机 Azure AD B2C 连接器可用。 对于本教程中的示例，我们将创建自己的规则。

你已启用 Azure Sentinel，将在 Azure AD B2C 租户中发生可疑情况时收到通知。

可以创建[自定义分析规则](../sentinel/detect-threats-custom.md)，以发现环境中存在的威胁和异常行为。 这些规则将搜索特定事件或事件集，在达到特定事件阈值或条件时发出警报。 然后，生成事件供进一步调查。

在下面的示例中，我们将阐释这样一种情况：当有人尝试强制访问你的环境但不成功时，你收到通知。 这可能意味着暴力攻击。 你想要在 60 秒内收到两个或更多不成功的登录时收到通知。

1. 从 Azure Sentinel 导航菜单中，选择“分析”。

2. 在顶部的操作栏中，选择“+创建”，然后选择“计划查询规则”。 此时会打开“分析规则向导”。

![显示选择创建计划查询规则的图像](./media/azure-sentinel/create-scheduled-rule.png)

3. 在“分析规则向导”中，转到“常规”选项卡。

   | 字段 | 值 |
   |:--|:--|
   |名称 | B2C 不成功的登录 |
   |说明 | 在 60 秒之内有两个或更多不成功的登录时通知 |
   | 策略 | 从攻击类别中选择要将规则分到的类别。 这些类别基于 [MITRE ATT&CK](https://attack.mitre.org/) 框架的策略。<BR>对于我们的示例，我们将选择 `PreAttack` <BR> MITRE ATT&CK® 是基于现实认知的攻击者策略和技术知识库，支持全球访问。 将 ATT&CK 知识库用作开发特定威胁模型和方法的基础。
   | 严重性 | 根据需要 |
   | 状态 | 在你创建规则时，其“状态”默认为 `Enabled`，这表示当你完成创建后，它将立即运行。 如果你不想使其立即运行，请选择 `Disabled`，这样就会将它添加到“可用规则”选项卡中，当你需要运行此规则时，可从该选项卡中启用它。|

![提供基本规则属性的图像](./media/azure-sentinel/create-new-rule.png)

4.  若要定义规则查询逻辑并配置设置，请在“设置规则逻辑”选项卡中，直接在“规则查询”字段中编写查询。  当 Azure AD B2C 租户在 60 秒之内有两个或多个未成功登录时，此查询将发出警报，并且将按 `UserPrincipalName` 进行组织。

![在逻辑选项卡中输入规则查询的图像](./media/azure-sentinel/rule-query.png)

在“查询计划”部分中，设置以下参数：

![设置查询计划参数的图像](./media/azure-sentinel/query-scheduling.png)

5. 在完成时选择“下一步: 事件设置(预览)”。 稍后将配置并添加自动响应。

6. 转至“审阅和创建”，以审阅新警报规则的所有设置。 当系统显示“验证通过”消息时，选择“创建”，将警报规则初始化。 

![审阅和创建规则的图像](./media/azure-sentinel/review-create.png)

7. 查看规则及其生成的事件。 在主“分析”屏幕上的“可用规则”选项卡下的表格中找到新创建的自定义规则（类型为“已计划”）。   你可以从此列表中编辑、启用、禁用或删除规则。   

![显示编辑、启用、禁用或删除规则的选项的分析屏幕图像](./media/azure-sentinel/rule-crud.png)

8. 查看新的 Azure AD B2C 不成功登录规则的结果。 转到“事件”页，你可以在其中对威胁进行会审、调查和修正。 一个事件可以包含多个警报。 它是用于特定调查的所有相关证据的聚合。 可以在事件级别中设置严重性和状态等属性。

>[!Note]
>[事件调查](../sentinel/investigate-cases.md)是 Azure Sentinel 的一项关键功能。

9. 若要开始调查，请选择一个特定的事件。 在右侧，可以查看此事件的详细信息，包括其严重性、涉及的实体、触发此事件的原始事件以及事件的唯一 ID。

![image alt-text="incident screen](./media/azure-sentinel/select-incident.png)

10. 在“事件”页中选择“查看完整详细信息”，并查看汇总事件信息并提供更多详细信息的相关选项卡。

![图像规则 73](./media/azure-sentinel/full-details.png)

11. 选择“证据” > “事件” > “链接到 Log Analytics”。 结果将会显示试图登录的标识的 `UserPrincipalName` 以及尝试次数。

![所选事件的详细信息的图像](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>自动响应

Azure Sentinel 提供[可靠的 SOAR 功能](../sentinel/automation-in-azure-sentinel.md)。 可以将自动操作（在 Azure Sentinel 中被称为 playbook）附加到 Analytics 规则中，以满足你的要求。

在此示例中，我们要在出现规则创建的事件时添加电子邮件通知。 使用 [Sentinel GitHub 存储库中的现有 playbook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification) 来完成此任务。 配置 playbook 后，编辑现有规则，并将 playbook 选入“自动化”选项卡。

![与规则关联的自动响应的配置屏幕的图像](./media/azure-sentinel/automation-tab.png)

## <a name="next-steps"></a>后续步骤

- [处理 Azure Sentinel 中的误报](../sentinel/false-positives.md)

- [示例工作簿](https://github.com/azure-ad-b2c/siem#workbooks)

- [Azure Sentinel 文档](../sentinel/index.yml)