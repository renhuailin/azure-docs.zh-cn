---
title: 从资源、订阅、管理组和安全分数豁免 Azure 安全中心建议
description: 了解如何创建规则以免除来自订阅或管理组的安全建议，并防止他们影响安全分数
author: memildin
ms.author: memildin
ms.date: 01/22/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 374ddaa088fba9ae7035f170562e06b7f07eae47
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709370"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>从安全分数豁免资源和建议 

每个安全团队的核心优先级是确保分析人员可以专注于涉及组织的任务和事件。 安全中心提供了许多用于自定义体验的功能，并确保安全分数反映了组织的安全优先级。 **豁免** 选项是一项功能。

调查 Azure 安全中心的安全建议时，所查看的第一条信息是受影响的资源的列表。

有时，将列出不应包含的资源。 或者，建议会在你认为它不属于的范围内显示。 安全中心未跟踪的进程可能已修正了资源。 该建议可能不适用于特定的订阅。 或者，组织可能只决定接受与特定资源或建议相关的风险。

在这种情况下，你可以创建例外以获取以下建议：

- 免除某资源，以确保其将来不会被列入运行不正常的资源，并且不会影响安全分数。 该资源将被列为不适用，原因将显示“已免除”以及你选择的特定理由。

- 免除某订阅或管理组，以确保建议不会影响安全分数，并且将来不会针对该订阅或管理组显示。 这与现有资源以及将来创建的任何资源相关。 建议将标记有你针对所选范围选择的特定理由。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|定价：|这是一种高级 Azure 策略功能，适用于无需额外费用的 Azure Defender 客户。 对于其他用户，以后可能会收取费用。|
|所需角色和权限：|用于创建例外的 **订阅所有者** 或 **策略参与者**<br>若要创建规则，需要在 Azure 策略中编辑策略的权限。<br>有关详细信息，请参阅 azure [策略中的 AZURE RBAC 权限](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)。|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||

## <a name="define-an-exemption"></a>定义例外

若要微调安全中心对订阅、管理组或资源进行的安全建议，可以创建例外规则来执行以下操作：

- 将特定 **建议** 标记为 "已缓解" 或 "风险已接受"。 可以为订阅、多个订阅或整个管理组创建建议例外。
- 针对特定建议，将 **一个或多个资源** 标记为 "已缓解" 或 "接受的风险"。

> [!TIP]
> 你还可以使用 API 创建例外。 有关示例 JSON 的详细说明，请参阅 [Azure 策略例外结构](../governance/policy/concepts/exemption-structure.md)。

若要创建例外规则：

1. 打开 "建议详细信息" 页面了解具体的建议。

1. 从页面顶部的工具栏中，选择 " **例外**"。

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="创建用于从订阅或管理组中免除的建议的免除规则。":::

1. 在 " **例外** " 窗格中：
    1. 选择此例外规则的作用域：
        - 如果选择管理组，则会从该组内的所有订阅中免除建议
        - 如果要创建此规则来免除建议中的一个或多个资源，请选择 "所选资源"，然后从列表中选择相关的资源

    1. 输入此例外规则的名称。
    1. （可选）设置到期日期。
    1. 选择例外的类别：
        - 如果你使用的是未标识安全中心的第三方服务，则 **通过第三方 (降低了)** 。 

            > [!NOTE]
            > 如果你免除了某个建议，则不会向你提供安全分数的分数。 但是，由于不会为不正常资源 *删除* 点，因此，分数将增加。

        - **弃权) 接受的风险** -如果决定接受不减轻此建议的风险，请 (
    1. 根据需要输入说明。
    1. 选择“创建”。

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="创建免除规则以从订阅或管理组中免除建议的步骤":::

    当例外生效时 (可能需要最多30分钟) ：
    - 建议或资源不会影响安全分数。
    - 如果你免除了特定资源，它们将列在 "建议详细信息" 页的 " **不适用** " 选项卡中。
    - 如果你免除了某个建议，则默认情况下，它将在安全中心的建议页上隐藏。 这是因为，该页上 **建议状态** 筛选器的默认选项是排除 **不适用** 的建议。 如果你在安全控制中免除了所有建议，也是如此。

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Azure 安全中心建议页上的默认筛选器隐藏不适用的建议和安全控件":::

    - "建议详细信息" 页顶部的信息栏更新了免除的资源的数目：
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="免除的资源数":::

1. 若要查看免除的资源，请打开 " **不适用** " 选项卡：

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="修改例外":::

    每个例外的原因包括在表 (1) 中。

    若要修改或删除例外，请选择省略号菜单 ( "..." ) 如 (2) 所示。

1. 若要查看订阅中的所有免除规则，请从信息栏选择 " **查看例外** "：

    > [!IMPORTANT]
    > 若要查看与某个建议相关的特定免除，请根据相关范围和建议名称筛选该列表。

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure 策略的豁免页":::

    > [!TIP]
    > 或者， [使用 Azure 资源关系图查找有关免除的建议](#find-recommendations-with-exemptions-using-azure-resource-graph)。

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>监视在你的订阅中创建的免除

如上文所述，例外规则是一个功能强大的工具，可对影响订阅和管理组中的资源的建议进行精细控制。 

为了跟踪用户如何行使此功能，我们创建了一个 Azure 资源管理器 (ARM) 模板，该模板部署逻辑应用操作手册和所有必要的 API 连接，以在创建例外时通知你。

- 若要了解有关操作手册的详细信息，请参阅技术社区博客文章 [如何在 Azure 安全中心跟踪资源例外](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- 你将在[Azure 安全中心 GitHub 存储库](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)中找到 ARM 模板
- 若要部署所有必要的组件，请 [使用此自动化过程](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>使用 Azure 资源关系图查找有关免除的建议

使用 Azure 资源关系图 (ARG) 可以通过强大的筛选、分组和排序功能，在你的云环境中即时访问资源信息。 这是以编程方式或从 Azure 门户中查询 Azure 订阅中的信息的一种快速且有效的方式。

若要查看所有具有免除规则的建议：

1. 打开“Azure Resource Graph 资源管理器”。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="启动 Azure Resource Graph Explorer 建议页面" :::

1. 输入以下查询并选择“运行查询”。

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


在以下页面中了解详细信息：
- [详细了解 Azure Resource Graph](../governance/resource-graph/index.yml)。
- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)
- [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/)





## <a name="exemption-rule-faq"></a>例外规则常见问题解答

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>如果在多个策略计划中有一个建议，会发生什么情况？

有时，一项安全建议会出现在多个策略计划中。 如果你有多个分配给同一订阅的相同建议的实例，并且你为该建议创建了例外，则将影响你有权编辑的所有计划。 

例如，建议的 * * * 是分配给 Azure 安全中心的所有 Azure 订阅的默认策略计划的一部分。 它也是 XXXXX。

如果尝试为此建议创建例外，将看到以下两条消息之一：

- 如果你具有编辑这两项计划所需的权限，你将看到：

    *此建议包含在多个策略计划中： [计划名称，用逗号分隔）。将在所有这些免除上创建例外。*  

- 如果对这两项计划没有足够的权限，则会看到以下消息：

    *你的权限有限，无法对所有策略计划应用例外，将仅针对具有足够权限的计划创建例外。*


## <a name="next-steps"></a>后续步骤

本文介绍了如何从建议中免除资源，使其不会影响安全分数。 有关安全分数的详细信息，请参阅：

- [Azure 安全中心的安全评分](secure-score-security-controls.md)