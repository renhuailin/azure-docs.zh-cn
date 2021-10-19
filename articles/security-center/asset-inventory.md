---
title: Azure 安全中心的资产库存
description: 了解 Azure 安全中心的资产管理体验，该体验提供对所有受安全中心监视的资源的完全可见性。
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 10/07/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 4175476bc655aa0be1a5377f3fada83cb30ac37e
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129715929"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory"></a>使用资产清单浏览和管理资源

Azure 安全中心的资产清单页提供了一个页面，用于查看已连接到安全中心的资源的安全状况。 

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后会提供有关如何消除这些安全漏洞的建议。

当任何资源具有未完成的建议时，它们将显示在清单中。

使用此视图及其筛选器可以解决以下这类问题：

- 我的哪些启用了 Azure Defender 的订阅具有重要建议？
- 我的哪些标记为“生产”的计算机缺少 Log Analytics 代理？
- 我的多少台标记有特定标记的计算机具有重要建议？
- 特定资源组中有多少资源具有漏洞评估服务提供的安全结果？

此工具的资产管理可能性很大，并且还在不断增长。 

> [!TIP]
> 资产库存页面上的安全建议与“建议”页面上的安全建议相同，但资产库存页面会根据受影响的资源进行显示。 有关如何解决建议的详细信息，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。


## <a name="availability"></a>可用性
|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|免费<br> 库存页的某些功能（例如[软件库存](#access-a-software-inventory)）需要付费解决方案到位|
|所需角色和权限：|所有用户|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png":::国家/地区/主权（Azure 政府、Azure 中国世纪互联）|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>资产库存的主要功能是什么？
库存页提供以下工具：

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Azure 安全中心内资产清单页的主要功能。" lightbox="media/asset-inventory/highlights-of-inventory.png":::


### <a name="1---summaries"></a>1 - 摘要
在定义任何筛选器之前，会显示库存视图顶部突出的值条带：

- **资源总数**：连接到安全中心的资源总数。
- **不正常的资源**：具有有效安全建议的资源。 [详细了解安全建议](security-center-recommendations.md)。
- **未受监视的资源**：有代理监视问题的资源 - 已部署 Log Analytics 代理，但代理没有发送数据或有其他运行状况问题。
- **未注册的订阅**：所选作用域中尚未连接到 Azure 安全中心的任何订阅。

### <a name="2---filters"></a>2 - 筛选器
页面顶部的多个筛选器提供一种根据你尝试回答的问题快速优化资源列表的方法。 例如，如果你想回答问题“我的哪台带有“生产”标记的计算机缺少 Log Analytics 代理？”，可以将“代理监视”筛选器与“标记”筛选器组合在一起。 

应用筛选器后，摘要值就会更新为与查询结果相关的值。 

### <a name="3---export-and-asset-management-tools"></a>3 - 导出和资产管理工具

**导出选项** - 库存提供了将所选筛选器选项的结果导出到 CSV 文件的选项。 还可以将查询本身导出到 Azure Resource Graph 资源管理器，以进一步优化、保存或修改 Kusto 查询语言 (KQL) 查询。

> [!TIP]
> KQL 文档为数据库提供一些示例数据以及一些简单的查询，以获取相应语言的体验。 [通过此 KQL 教程了解详细信息](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)。

资产管理选项 - 通过库存可以执行复杂的发现查询。 找到与查询匹配的资源后，库存将提供诸如以下操作的快捷方式：

- 将标签分配给经过筛选的资源 - 选中要标记的资源旁边的复选框。
- 在安全中心中加入新服务器 - 使用“添加非 Azure 服务器”工具栏按钮。
- 使用 Azure 逻辑应用自动执行工作负载 - 使用“触发逻辑应用”按钮可在一个或多个资源上运行逻辑应用。 逻辑应用必须提前准备好，并接受相关的触发器类型（HTTP 请求）。 [详细了解逻辑应用](../logic-apps/logic-apps-overview.md)。


## <a name="how-does-asset-inventory-work"></a>资产库存的工作方式？

资产库存利用 [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)，这种 Azure 服务提供跨多个订阅查询安全中心的安全状况数据的功能。

ARG 用于提供高效资源探索，并具有大规模查询的功能。

资产库存可以使用 [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/)，通过将 ASC 数据与其他资源属性进行交叉引用来快速生成深度见解。


## <a name="how-to-use-asset-inventory"></a>如何使用资产库存

1. 从安全中心的边栏选择“库存”。

1. 使用“按名称筛选”框可显示特定资源，或以如下所述的方式使用筛选器。

1. 在筛选器中选择相关选项，以创建要执行的特定查询。

    默认情况下，资源按有效安全建议的数量排序。

    > [!IMPORTANT]
    > 每个筛选器中的选项特定于当前选择的订阅中的资源和你在其他筛选器中的选择。
    >
    > 例如，如果你仅选择了一个订阅，并且该订阅没有要修正的具有重要安全建议的资源（0 个运行不正常的资源），则“建议”筛选器将没有选项。 

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="使用 Azure 安全中心的资产清单中的筛选器选项来筛选不受监视的生产资源":::

1. 若要使用“安全发现包含”筛选器，请通过漏洞发现的 ID、安全检查或 CVE 名称输入自由文本以筛选受影响的资源：

    ![“安全发现包含”筛选器](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > “安全发现包含”和“标记”筛选器仅接受一个值 。 若要使用多个筛选器，请使用“添加筛选器”。

1. 若要使用“Azure Defender”筛选器，请选择一个或多个选项（“关”、“开”或“部分”）：

    - 关 - 不受 Azure Defender 计划保护的资源。 可以右键单击其中任意一些资源并对其进行升级：

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="通过右键单击将资源升级到 Azure Defender。" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - 开 - 受 Azure Defender 计划保护的资源
    - 部分 - 此选项应用于禁用了某些（但不是全部）Azure Defender 计划的订阅 。 例如，以下订阅已禁用五个 Azure Defender 计划。 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="部分开启 Azure Defender 的订阅。":::

1. 若要进一步检查查询结果，请选择你感兴趣的资源。

1. 若要在 Resource Graph Explorer 中以查询的形式查看当前选定的筛选器选项，请选择“打开查询”。

    ![ARG 中的库存查询。](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. 如果已经定义了一些筛选器并使页面保持打开状态，则安全中心不会自动更新结果。 除非手动重新加载页面或选择“刷新”，否则对资源的任何更改都不会影响显示的结果。

## <a name="access-a-software-inventory"></a>访问软件清单

如果已启用与 Microsoft Defender for Endpoint 的集成并为服务器启用了 Azure Defender，则将有权访问软件清单。

:::image type="content" source="media/asset-inventory/software-inventory-filters.gif" alt-text="如果已启用威胁和漏洞解决方案，安全中心的资产清单会提供一个筛选器，用于按已安装的软件选择资源。":::

> [!NOTE]
> “空白”选项显示没有 Microsoft Defender for Endpoint（或没有适用于服务器的 Azure Defender）的计算机。

除了资产清单页中的筛选器，还可从 Azure Resource Graph 资源管理器浏览软件清单数据。

使用 Azure Resource Graph 资源管理器访问和浏览软件清单数据的示例：

1. 打开“Azure Resource Graph 资源管理器”。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="启动 Azure Resource Graph Explorer 建议页面" :::

1. 选择以下订阅范围：securityresources/softwareinventories

1. 输入以下任何查询（或自定义或编写你自己的查询！），然后选择“运行查询”。

    - 生成已安装软件的基本列表：

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=properties.version
        ```

    - 按版本号筛选：

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=tostring(properties.    version)
        | where Software=="windows_server_2019" and parse_version(Version)<=parse_version("10.0.17763.1999")
        ```

    - 使用软件产品组合查找计算机：

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = properties.azureVmId
        | where properties.softwareName == "apache_http_server" or properties.softwareName == "mysql"
        | summarize count() by tostring(vmId)
        | where count_ > 1
        ```

    - 软件产品与其他 ASC 建议的组合：

        （在本例中 - 安装了 MySQL 并公开管理端口的计算机）

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = tolower(properties.azureVmId)
        | where properties.softwareName == "mysql"
        | join (
        securityresources
        | where type == "microsoft.security/assessments"
        | where properties.displayName == "Management ports should be closed on your virtual machines" and properties.status.code == "Unhealthy"
        | extend vmId = tolower(properties.resourceDetails.Id)
        ) on vmId
        ```



## <a name="faq---inventory"></a>常见问题解答 - 库存

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>为什么未显示我的所有订阅、计算机、存储帐户等？

库存视图从云安全状况管理 (CSPM) 角度列出了安全中心连接的资源。 筛选器不会返回你环境中的所有资源；只会返回那些具有重要（或“有效”）建议的资源。 

例如，以下屏幕截图显示了一个有权访问 38 个订阅但只有 10 个订阅现在有建议的用户。 因此，当它们按“资源类型 = 订阅”进行筛选时，库存中仅显示具有有效建议的那 10 个订阅：

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="在没有有效建议的情况下，并非所有子项都返回。":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>为什么我的一些资源在 Azure Defender 或代理监视列中显示空值？

并非所有受安全中心监视的资源都有代理。 例如，Azure 存储帐户或 PaaS 资源（如磁盘、逻辑应用、Data Lake Analysis 和事件中心）。

当定价或代理监视与资源无关时，库存的这些列中将不会显示任何内容。

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="某些资源在代理监视或 Azure Defender 列中显示空白信息。":::

## <a name="next-steps"></a>后续步骤

本文介绍 Azure 安全中心的资产库存页面。

有关相关工具的详细信息，请参阅以下页面：

- [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)
- [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/)