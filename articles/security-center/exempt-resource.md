---
title: 从资源、订阅、管理组和安全功能分数中免除 Azure 安全中心建议
description: 了解如何创建规则以免除订阅或管理组中的安全建议并防止其影响安全功能分数
author: memildin
ms.author: memildin
ms.date: 05/12/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: f69b7174ec37a38fd972f53daaaf09776a279cea
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745096"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>从安全功能分数中免除资源和建议 

每个安全团队的核心优先事项是确保分析人员可以专注于对组织至关重要的任务和事件。 安全中心具有许多功能，可用于自定义体验并确保安全功能分数反映组织的安全优先级。 免除选项就是此类功能。

在 Azure 安全中心中研究安全建议时，首先看到的信息是受影响的资源列表。

有时，其中会列出你认为不应包含的资源。 或者，建议会在你认为它不属于的范围内显示。 该资源可能已被安全中心未跟踪的进程修正。 该建议可能不适用于特定的订阅。 或者，组织可能只决定接受与特定资源或建议相关的风险。

在这种情况下，可以为建议创建免除，以实现以下目的：

- 免除某资源，以确保其将来不会被列入运行不正常的资源，并且不会影响安全分数。 该资源将被列为不适用，原因将显示“已免除”以及你选择的特定理由。

- 免除某订阅或管理组，以确保建议不会影响安全分数，并且将来不会针对该订阅或管理组显示。 这与现有资源以及将来创建的任何资源相关。 建议将标记有你针对所选范围选择的特定理由。

## <a name="availability"></a>可用性

| 方面                          | 详细信息                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 发布状态：                  | 预览<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| 定价：                        | 这是一项高级 Azure Policy 功能，Azure Defender 客户无需额外付费即可使用此功能。 对于其他用户，将来可能需要付费。                                                                                                                                                                 |
| 所需角色和权限： | 要创建免除的所有者或资源策略参与者 <br>若要创建规则，你需要在 Azure Policy 中编辑策略的权限。<br>若要了解详细信息，请参阅 [Azure Policy 中的 Azure RBAC 权限](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)。                                            |
| 的限制：                    | 只能为安全中心的默认计划、[Azure 安全基准](/security/benchmark/azure/introduction)或提供的任意法规标准计划中包含的建议创建免除。 不能免除从自定义计划中生成的建议。 详细了解[策略、计划和建议](security-policy-concept.md)之间的关系。 |
| 云：                         | :::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/no-icon.png":::国家/地区/主权（Azure 政府、Azure 中国世纪互联）                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>定义免除

若要优化安全中心针对订阅、管理组或资源提出的安全建议，可以创建免除规则来执行以下操作：

- 标记特定的建议，或者标记为“已缓解”或“已接受风险”。 可以为一个订阅、多个订阅或整个管理组创建建议免除。
- 针对特定建议，将一个或多个资源标记为“已缓解”或“已接受风险”。

> [!NOTE]
> 只能为安全中心的默认计划、Azure 安全基准或提供的任意法规标准计划中包含的建议创建免除。 不能免除从分配给订阅的任何自定义计划中生成的建议。 详细了解[策略、计划和建议](security-policy-concept.md)之间的关系。

> [!TIP]
> 还可以使用 API 创建免除。 若要了解 JSON 示例和相关结构的说明，请参阅 [Azure Policy 免除结构](../governance/policy/concepts/exemption-structure.md)。

若要创建免除规则，请执行以下操作：

1. 打开针对特定建议的“建议详细信息”页。

1. 在页面顶部的工具栏中，选择“免除”。

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="为要从订阅或管理组中免除的建议创建免除规则。":::

1. 在“免除”窗格中：
    1. 选择此免除规则的范围：
        - 如果选择管理组，则该组内所有订阅中将免除该建议
        - 如果要创建此规则以免除建议中的一个或多个资源，请选择“选择的资源”，然后选择列表中的相关资源

    1. 输入此免除规则的名称。
    1. （可选）设置到期日期。
    1. 选择免除的类别：
        - **通过第三方解决（已缓解）** - 如果使用的是安全中心未识别的第三方服务。 

            > [!NOTE]
            > 如果通过标记为“已缓解”来免除某项建议，安全功能分数不会因此增加。 但由于没有因运行不正常的资源而减分，因此分数将会增加。

        - **已接受风险（免除）** - 如果已决定接受不缓解此建议所面临的风险
    1. （可选）输入说明。
    1. 选择“创建”。

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="创建免除规则以从订阅或管理组中免除建议的步骤。":::

    免除生效（最多可能需要 30 分钟）后：
    - 建议或资源不会影响安全功能分数。
    - 如果免除了特定资源，则“建议详细信息”页的“不适用”选项卡中会列出这些资源。
    - 如果免除了某项建议，则默认情况下，安全中心的“建议”页中将隐藏该建议。 这是因为该页中的“建议状态”筛选器的默认选项将排除不适用的建议 。 如果免除安全控制措施中的所有建议，情况也是如此。

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Azure 安全中心的“建议”页中的默认筛选器隐藏不适用的建议和安全控制措施":::

    - “建议详细信息”页顶部的信息栏中会更新已免除资源的数量：
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="已免除资源的数量。":::

1. 若要查看已免除的资源，请打开“不适用”选项卡：

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="修改免除。":::

    表 (1) 中包含了每项免除的原因。

    若要修改或删除免除，请选择省略号菜单 ("...")，如 (2) 所示。

1. 若要查看订阅中的所有免除规则，请从信息栏中选择“查看免除”：

    > [!IMPORTANT]
    > 若要查看与某项建议相关的特定免除，请根据相关范围和建议名称筛选列表。

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy 的“免除”页":::

    > [!TIP]
    > 或者，[使用 Azure Resource Graph 查找具有免除的建议](#find-recommendations-with-exemptions-using-azure-resource-graph)。

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>监视订阅中创建的免除

如本页前面部分所述，免除规则是一种功能强大的工具，使用该工具可精细控制对订阅和管理组中的资源造成影响的建议。 

为跟踪用户如何使用此功能，我们创建了 Azure 资源管理器 (ARM) 模板，该模板可部署逻辑应用 Playbook 和所有必需的 API 连接，以便在创建免除后向你发送通知。

- 若要详细了解 playbook，请参阅技术社区博客文章[如何在 Azure 安全中心中跟踪资源免除](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- 可在 [Azure 安全中心 GitHub 存储库](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)中找到 ARM 模板
- 若要部署所有必需的组件，请[使用此自动化过程](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)

## <a name="use-the-inventory-to-find-resources-that-have-exemptions-applied"></a>使用清单查找已应用豁免的资源

Azure 安全中心的资产清单页提供了一个页面，用于查看已连接到安全中心的资源的安全状况。 有关详细信息，请参阅[利用资产清单浏览和管理资源](asset-inventory.md)。

“清单”页包含多个筛选器，你可以使用它们将资源列表缩小为任何给定方案中最感兴趣的资源。 “包含豁免”就是此类筛选器的一个例子。 使用此筛选器可查找已从一条或多条建议中豁免的所有资源。

:::image type="content" source="media/exempt-resource/inventory-filter-exemptions.png" alt-text="安全中心的“资产清单”页和用于查找包含豁免的资源的筛选器":::


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>使用 Azure Resource Graph 查找具有免除的建议

使用 Azure Resource Graph (ARG)，可以通过可靠的筛选、分组和排序功能，快速访问你的云环境中的资源信息。 这是以编程方式或从 Azure 门户中查询 Azure 订阅中的信息的一种快速且有效的方式。

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


通过以下页面了解详细信息：
- [详细了解 Azure Resource Graph](../governance/resource-graph/index.yml)。
- [如何使用 Azure Resource Graph 浏览器创建查询](../governance/resource-graph/first-query-portal.md)
- [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>常见问题解答 - 免除规则

- [如果一项建议属于多个策略计划，会发生什么情况？](#what-happens-when-one-recommendation-is-in-multiple-policy-initiatives)
- [是否有不支持豁免的建议？](#are-there-any-recommendations-that-dont-support-exemption)

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>如果一项建议属于多个策略计划，会发生什么情况？

有时，一项安全建议会出现在多个策略计划中。 如果将同一建议的多个实例分配给同一订阅，并为该建议创建免除，这将影响你可编辑的所有计划。 

例如，建议 **** 属于 Azure 安全中心为所有 Azure 订阅分配的默认策略计划。 它也在 XXXXX 中。

如果尝试为此建议创建免除，你会看到以下两条消息之一：

- 如果你有编辑这两个计划的权限，你会看到：

    此建议将包含在多个策略计划中：[以逗号分隔的计划名称]。将对所有这些计划创建豁免。  

- 如果对两个计划均没有足够权限，你会看到此消息：

    你的权限有限，无法在所有策略计划中应用免除，仅具有足够权限的计划中将创建免除。

### <a name="are-there-any-recommendations-that-dont-support-exemption"></a>是否有不支持豁免的建议？

以下建议不支持豁免：

- 应强制执行容器 CPU 和内存限制
- 应避免特权容器
- 应只从受信任的注册表中部署容器映像
- 容器应只侦听允许的端口
- 服务应只侦听允许的端口
- 应强制对容器使用最低特权 Linux 功能
- 应强制对容器使用不可变（只读）根文件系统
- 应避免使用特权提升的容器
- 应避免以根用户身份运行容器
- 应限制对主机网络和端口的使用
- 应避免使用共享敏感主机命名空间的容器
- 应限制为只有已知列表才能使用 Pod HostPath 卷装载，以限制来自遭入侵容器的节点访问
- 应限制替代或禁用容器 AppArmor 配置文件


## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何从建议中免除资源，以免影响安全功能分数。 有关安全功能分数的详细信息，请参阅：

- [Azure 安全中心的安全评分](secure-score-security-controls.md)