---
title: 在 Azure Sentinel 中通过用户和实体行为分析 (UEBA) 来识别高级威胁 | Microsoft Docs
description: 为实体（用户、主机名、IP 地址）创建行为基线，然后使用基线来检测异常行为和识别零日高级持久性威胁 (APT)。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: bf7a17d96d31fd4214d5465a5739acc9ce9a9d53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455495"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>在 Azure Sentinel 中通过用户和实体行为分析 (UEBA) 来识别高级威胁

> [!IMPORTANT]
>
> - UEBA 和实体页功能现已在所有 Azure Sentinel 地理位置和区域正式发布 。

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>什么是用户和实体行为分析 (UEBA)？

### <a name="the-concept"></a>概念

识别组织内的威胁及其潜在影响（无论是被入侵的实体还是恶意内部因素）始终是一个耗时耗力的过程。 筛选警报、连接各点和主动搜寻都需要大量的时间和精力，却只有极少回报，而复杂的潜在威胁却很难发现。 特别难以发现的威胁（如零日威胁、针对性威胁和高级持久性威胁）对组织来说是最危险的，因此能够检测这些威胁则变得更加重要。

Azure Sentinel 中的 UEBA 功能消除了分析师工作中的繁琐部分和不确定性，并提供了高保真的可操作情报，这样他们便可以专注于调查和补救。

Azure Sentinel 在从所有其连接的数据源中收集日志和警报的同时，还会跨时间和对等组对收集内容进行分析并生成组织实体（例如用户、主机、IP 地址和应用程序）的基线行为概况。 通过使用各种技术和机器学习功能，Azure Sentinel 可以识别异常活动，帮助分析师确定资产是否已遭入侵。 此外，Azure Sentinel 还可以确定特定资产的相对敏感度、识别对等组资产以及评估任何已遭入侵资产的潜在影响（“爆炸半径”）。 掌握这些信息后，分析师便可有效划分调查和事件处理的优先级。 

### <a name="architecture-overview"></a>体系结构概述

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="实体行为分析体系结构":::

### <a name="security-driven-analytics"></a>安全驱动的分析

受 UEBA 解决方案的 Gartner 范例的启发，Azure Sentinel 基于三个参考框架提供了一种“由外而内”的方法：

- 用例：MITRE ATT&CK 策略、技术和分解技术框架将各种实体作为受害者、作恶者或枢轴点放入杀伤链中，Azure Sentinel 根据按照此框架开展的安全性研究对相关攻击途径和方案划分优先级，从而特别专注于每个数据源可以提供的最有价值的日志。

- 数据源：尽管首要支持 Azure 数据源，但出于周全性考虑，Azure Sentinel 选择第三方数据源来提供与我们的威胁方案相匹配的数据。

- 分析：通过使用各种机器学习 (ML) 算法，Azure Sentinel 识别异常活动并以上下文扩充的形式清晰、准确地呈现证据，这方面的示例如下。

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="由外而内的行为分析方法":::

Azure Sentinel 提供的项目可帮助安全分析师结合上下文并通过对比用户的基线概况来清楚地了解环境中的异常活动。 用户（或主机、地址）执行的操作在上下文中进行评估，“true”结果则表示发现异常：
- 跨地理位置、设备和环境。
- 跨时间和频率（与用户自己的历史记录相比）。
- 与对等方的行为进行比较。
- 与组织的行为进行比较。

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="实体上下文":::


### <a name="scoring"></a>计分

每个活动都使用“调查优先级评分”进行评分，该评分根据对用户及其对等方的行为学习来确定特定用户执行特定活动的概率。 被识别为最不正常的活动会获得最高分（分数范围为 0-10 分）。

有关行为分析工作原理的示例，请参阅 [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) 中的行为分析使用方式。

## <a name="entity-pages"></a>实体页

详细了解 [Azure Sentinel 中的实体](entities-in-azure-sentinel.md)，并查看[支持的实体和标识符](entities-reference.md)的完整列表。

如果在搜索、警报或调查中遇到任何实体，可以选择该实体，随后将转到实体页，即包含该实体的所有有用信息的数据表。 在此页面上，可看到的信息类型包括有关实体的基本信息、与此实体相关的重要事件的时间线以及有关实体行为的见解。
 
实体页由 3 个部分组成：
- 左侧面板包含实体的标识信息，这些信息是从 Azure Active Directory、Azure Monitor、Azure 安全中心和 Microsoft Defender 等数据源中收集的。

- 中间面板显示与实体相关的重要事件（例如警报、书签和活动）的图形和文本时间线。 活动是 Log Analytics 中的重要事件的聚合。 检测这些活动的查询是由 Microsoft 安全研究团队开发的。

- 右侧面板显示关于该实体的行为见解。 这些见解可帮助快速识别异常和安全威胁。 见解是由 Microsoft 安全研究团队开发的，它们基于异常检测模型。

### <a name="the-timeline"></a>时间线

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="实体页时间线":::

时间线是实体页显示 Azure Sentinel 中的行为分析情况的主要部分。 它会显示与实体相关的事件，帮助你了解特定时间范围内的实体活动。

可在多个预设选项中选择时间范围（例如“过去 24 小时”），也可将其设置为任何自定义时间范围。 此外，你还可设置筛选器，来将时间线中的信息限制为特定类型的事件或警报。

时间线中包含以下类型的项：

- 警报 - 将实体定义为“映射实体”的所有警报。 请注意，如果你的组织已创建[使用分析规则的自定义警报](./tutorial-detect-threats-custom.md)，那么你应确保规则的实体映射已正确完成。

- 书签 - 包含页面上显示的特定实体的所有书签。

- 活动 - 与实体相关的重要事件的聚合。 
 
### <a name="entity-insights"></a>实体见解
 
实体见解是 Microsoft 安全研究人员定义的查询，可帮助你的分析师更高效、更有效地进行调查。 见解显示在实体页的区域中，以表格数据和图表形式提供有关主机和用户的重要安全信息。 在此处显示信息意味着你无需访问 Log Analytics。 见解包含与登录、组添加、异常事件等内容相关的数据，还包含用于检测异常行为的高级 ML 算法。 

见解基于以下数据源：
- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Heartbeat（Azure Monitor 代理）
- CommonSecurityLog (Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>如何使用实体页

根据设计，实体页是多种使用方案的一部分，你可从事件管理、调查图和书签来访问它，也可从 Azure Sentinel 主菜单中“实体行为分析”下的实体搜索页来直接访问它。

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="实体页用例":::

## <a name="data-schema"></a>数据架构

### <a name="behavior-analytics-table"></a>行为分析表

| 字段                     | 说明                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | 租户的唯一 ID 编号                                      |
| SourceRecordId            | EBA 事件的唯一 ID 编号                                   |
| TimeGenerated             | 活动发生时的时间戳                              |
| TimeProcessed             | EBA 引擎处理活动时的时间戳            |
| ActivityType              | 活动的高级类别                                 |
| ActionType                | 活动的规范化名称                                     |
| UserName                  | 发起活动的用户的用户名                    |
| UserPrincipalName         | 发起活动的用户的完整用户名               |
| EventSource               | 提供原始事件的数据源                        |
| SourceIPAddress           | 发起活动的 IP 地址                        |
| SourceIPLocation          | 发起活动的国家/地区，从 IP 地址进行扩充 |
| SourceDevice              | 发起活动的设备的主机名                  |
| DestinationIPAddress      | 活动目标的 IP 地址                            |
| DestinationIPLocation     | 活动目标所在国家/地区，从 IP 地址进行扩充     |
| DestinationDevice         | 目标设备的名称                                           |
| **UsersInsights**         | 相关用户的上下文扩充                            |
| **DevicesInsights**       | 相关设备的上下文扩充                          |
| **ActivityInsights**      | 活动的上下文分析（基于我们的分析）              |
| **InvestigationPriority** | 异常分数，介于 0-10（0=良性，10=高度异常）         |
|

可以在 [UEBA 扩充引用文档](ueba-enrichments.md)中查看 UsersInsights、DevicesInsights 和 ActivityInsights 中引用的所有上下文扩充  。

### <a name="querying-behavior-analytics-data"></a>查询行为分析数据

使用 [KQL](/azure/data-explorer/kusto/query/) 可以查询行为分析表。

例如，如果要查找用户无法登录 Azure 资源的所有情况，其中用户是第一次尝试从指定国家/地区进行连接，而来自该国家/地区的连接在对等用户中甚至都不常见，则可以使用以下查询：

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>用户对等元数据 - 表和笔记本

对等用户的元数据可为威胁检测、事件调查和寻找潜在威胁提供重要的上下文。 安全分析师可以观察对等用户的正常活动，以相较于对等用户的活动确定该用户的活动是否异常。

Azure Sentinel 根据用户的 Azure AD 安全组成员身份、邮寄列表等进行计算并对对等用户进行排名，然后在 UserPeerAnalytics 表中存储前 1-20 名对等用户。 下方屏幕截图显示了 UserPeerAnalytics 表的架构，还显示了用户 Kendall Collins 排名前八位的对等用户。 Azure Sentinel 使用“词频-逆文本频率”(TF-IDF) 算法规范化排名计算的权重：组越小，权重越高。 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="对等用户元数据表的屏幕截图":::

可以使用 Azure Sentinel GitHub 存储库中提供的 [Jupyter 笔记本](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata)来以可视化方式显示对等用户元数据。 有关如何使用该笔记本的详细说明，请参阅[引导式分析 - 用户安全元数据](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb)笔记本。

### <a name="permission-analytics---table-and-notebook"></a>权限分析 - 表和笔记本

权限分析有助于确定攻击者入侵组织资产造成的潜在影响。 这种影响也称为资产的“爆炸半径”。 安全分析师可以使用此信息来确定调查和事件处理的优先级。

通过评估用户可以直接或通过组或服务主体访问的 Azure 订阅，Azure Sentinel 确定给定用户对 Azure 资源持有的直接和可传递访问权限。 此信息以及用户的 Azure AD 安全组成员身份的完整列表将存储在 UserAccessAnalytics 表中。 下面的屏幕截图显示了用户 Alex Johnson 的 UserAccessAnalytics 表中的示例行。 源实体是用户或服务主体帐户，目标实体是源实体有权访问的资源 。 “访问级别”和“访问类型”的值取决于目标实体的访问控制模型 。 可以看到，Alex 对 Azure 订阅 Contoso Hotels Tenant 具有“参与者”访问权限。 订阅的访问控制模型是 Azure RBAC。   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="用户访问权限分析表的屏幕截图":::

可以使用 Azure Sentinel GitHub 存储库中的 [Jupyter 笔记本](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata)（与上文相同的笔记本）以可视化方式显示权限分析数据。 有关如何使用该笔记本的详细说明，请参阅[引导式分析 - 用户安全元数据](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb)笔记本。

### <a name="hunting-queries-and-exploration-queries"></a>搜寻查询和探索查询

Azure Sentinel 基于 BehaviorAnalytics 表提供一组现成的搜寻查询、探索查询和一个工作簿。 这些工具提供了针对特定用例的丰富数据，可指示异常行为。 

详细了解 Azure Sentinel 中的[搜寻和调查图](./hunting.md)。

## <a name="next-steps"></a>后续步骤
本文档介绍了 Azure Sentinel 的实体行为分析功能。 若要获得有关实现的实用指南，或要使用已获得的见解，请参阅以下文章：

- 在 Azure Sentinel 中[启用实体行为分析](./enable-entity-behavior-analytics.md)。
- [搜寻安全威胁](./hunting.md)。
