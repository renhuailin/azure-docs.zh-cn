---
title: 在 Azure Sentinel 中通过用户和实体行为分析来识别高级威胁 (UEBA) |Microsoft Docs
description: 为 (用户、主机名、IP 地址) 的实体创建行为基线，并使用它们来检测异常行为并识别零天高级持久性威胁 (APT) 。
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
ms.date: 01/25/2021
ms.author: yelevin
ms.openlocfilehash: 458c801e1434832bf65da669ca89cb5c5eebe2e8
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807557"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>在 Azure Sentinel 中通过用户和实体行为分析来识别高级威胁 (UEBA) 

> [!IMPORTANT]
>
> - UEBA 和实体页功能现已在 **_所有_** Azure Sentinel 地理位置和区域 **公开上市**。

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>什么是用户和实体行为分析 (UEBA) ？

### <a name="the-concept"></a>概念

确定组织内的威胁及其潜在影响-无论是被泄露的实体还是恶意内幕，都始终是耗时且耗费人力的过程。 通过警报没完没了，连接点和活动搜寻全部增加了大量的时间和精力，并具有极小的回报，而复杂的威胁只是 evading 发现。 特别难实现的威胁（如零天、目标和高级持久性威胁）对于您的组织来说是最危险的，因此它们的检测能力都是最重要的。

Azure Sentinel 中的 UEBA 功能从分析师的工作负载和不确定性中消除了完成，提供了高保真、可操作的智能，使他们能够专注于调查和修正工作。

由于 Azure Sentinel 从其所有连接的数据源收集日志和警报，因此会对其进行分析，并构建组织实体的基线行为配置文件， (例如用户、主机、IP 地址和应用程序) 跨时间和对等组范围。 Azure Sentinel 可以使用各种技术和机器学习功能来识别异常活动，并帮助你确定资产是否已泄露。 此外，它还可以确定特定资产的相对敏感度，确定对等组资产，并评估 (") " 的任何已泄露资产的潜在影响。 掌握这些信息后，您可以有效地划分调查和事件处理的优先级。 

### <a name="architecture-overview"></a>体系结构概述

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="实体行为分析体系结构":::

### <a name="security-driven-analytics"></a>安全驱动的分析

根据 Gartner 的 UEBA 解决方案，Azure Sentinel 根据三个参考框架提供了 "外接程序" 方法：

- **用例：** 根据与 MITRE ATT&CK 框架的策略、技术和子方法的相关攻击向量和方案的优先级，它们将各种实体用作 kill 链中的受害者、perpetrators 或枢点;Azure Sentinel 专门针对每个数据源可以提供的最有价值的日志。

- **数据源：** 虽然首先支持 Azure 数据源，但 Azure Sentinel 周全选择第三方数据源来提供与我们的威胁方案相匹配的数据。

- **分析：** Azure Sentinel 使用各种机器学习 (ML) 算法来识别异常活动，并以上下文根据的形式清楚地呈现证据，其中显示了下面的示例。

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="行为分析外部方法":::

Azure Sentinel 提供的项目可帮助您的安全分析人员清楚地了解环境中的异常活动，并与用户的基准配置文件进行比较。 用户 (或主机执行的操作或地址) 的计算结果为根据上下文，其中 "true" 结果表示发现的异常：
- 跨地理位置、设备和环境。
- 与用户自己的历史记录) 相比，跨时间和频率视野 (。
- 与对等方的行为相比。
- 与组织的行为相同。

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="实体上下文":::


### <a name="scoring"></a>计分

每个活动都使用 "调查优先级评分" 评分，根据用户及其对等方的行为教训确定特定用户执行特定活动的概率。 最不正常的活动会收到 0-10) 的最高分 (。

有关其工作原理的示例，请参阅如何在 [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) 中使用行为分析。

## <a name="entities-in-azure-sentinel"></a>Azure Sentinel 中的实体

### <a name="entity-identifiers"></a>实体标识符

当向 Azure Sentinel 发送警报时，它们将包含 Azure Sentinel 标识并分类为实体的数据元素，例如用户帐户、主机、IP 地址和其他实体。 有时，如果警报不包含有关实体的足够信息，则此标识可能是一个难题。

例如，可以通过多种方式标识用户帐户：使用 Azure AD 帐户的数字标识符 (GUID) 或其用户主体名称 (UPN) 值，或者使用其用户名及其 NT 域名的组合。 不同数据源可以通过不同方式标识同一用户。 因此，只要有可能，Azure Sentinel 就会将这些标识符合并到单个实体中，以便能够正确地识别。

但可能会发生这种情况：其中一个资源提供程序创建了一个未充分标识实体的警报（例如，没有域名上下文的用户名）。 在这种情况下，不能将用户实体与相同用户帐户的其他实例进行合并，这会被标识为单独的实体，而这两个实体将保持独立，而不是统一。

为了最大限度地降低这种情况发生的风险，您应该验证所有警报提供程序是否都正确地识别了它们产生的警报中的实体。 此外，将用户帐户实体与 Azure Active Directory 同步可能会创建统一目录，从而能够合并用户帐户实体。

以下类型的实体当前在 Azure Sentinel 中标识：

- 用户帐户 (帐户) 
- 主机
- Ip 地址 (IP) 
- 恶意软件
- 文件
- 过程
- 云应用程序 (CloudApplication) 
- 域名 (DNS) 
- Azure 资源
- 文件 (Get-filehash) 
- 注册表项
- 注册表值
- 安全组
- URL
- IoT 设备
- 邮箱
- 邮件群集
- 邮件
- 提交邮件

### <a name="entity-pages"></a>实体页

如果你遇到当前限制为用户和主机在搜索、警报或调查中) 的任何实体 (，则可以选择该实体，并将其转到 **实体页**，这是有关该实体的有用信息的完整数据表。 你将在此页上看到的信息类型包括有关实体的基本信息、与此实体相关的明显事件的时间线以及有关实体行为的见解。
 
实体页由三个部分组成：
- 左侧面板包含实体的标识信息，这些信息从数据源（例如 Azure Active Directory、Azure Monitor、Azure 安全中心和 Microsoft Defender）收集。

- 中心面板显示与实体相关的明显事件的图形和文本时间线，如警报、书签和活动。 活动是 Log Analytics 中值得注意的事件的聚合。 检测这些活动的查询由 Microsoft 安全研究团队开发。

- 右侧面板显示有关实体的行为见解。 这些见解可帮助快速确定异常和安全威胁。 见解由 Microsoft 安全研究团队开发，并基于异常检测模型。

### <a name="the-timeline"></a>时间线

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="实体页时间线":::

时间线是实体页面在 Azure Sentinel 中对行为分析的贡献的主要部分。 它介绍了与实体相关的事件，帮助您了解特定时间范围内的实体活动。

你可以从多个预设选项中选择 **时间范围** (如 " *过去24小时* ") ，或将其设置为任何自定义的时间范围。 此外，您可以设置筛选器，以将时间线中的信息限制为特定类型的事件或警报。

时间线中包含以下类型的项：

- 警报-实体定义为 **映射实体** 的任何警报。 请注意，如果你的组织已 [使用分析规则创建了自定义警报](./tutorial-detect-threats-custom.md)，你应该确保规则的实体映射正确完成。

- 书签-包含页面上显示的特定实体的任何书签。

- 活动-聚合与实体相关的明显事件。 
 
### <a name="entity-insights"></a>Entity Insights
 
实体见解是由 Microsoft 安全研究人员定义的查询，旨在帮助分析师更有效、更有效地进行调查。 该见解作为实体页的一部分提供，并以表格格式的数据和图表形式提供有关主机和用户的重要安全信息。 此处的信息意味着无需 detour 到 Log Analytics。 该见解包括有关登录、组添加、异常事件等的数据，并包括用于检测异常行为的高级 ML 算法。 见解基于以下数据类型：
- Syslog
- SecurityEvent
- 审核日志
- 登录日志
- 办公室活动
- BehaviorAnalytics (UEBA)  
 
### <a name="how-to-use-entity-pages"></a>如何使用实体页

实体页旨在作为多种使用方案的一部分，可从事件管理、调查图、书签或直接从 Azure 标记主菜单中的 " **实体行为分析** " 下的实体搜索页访问。

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="实体页用例":::


## <a name="data-schema"></a>数据架构

### <a name="behavior-analytics-table"></a>行为分析表

| 字段                     | 说明                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | 租户的唯一 ID 号                                      |
| SourceRecordId            | EBA 事件的唯一 ID 号                                   |
| TimeGenerated             | 活动出现时间的时间戳                              |
| TimeProcessed             | EBA 引擎处理活动的时间戳            |
| ActivityType              | 活动的高级类别                                 |
| ActionType                | 活动的规范化名称                                     |
| UserName                  | 发起活动的用户的用户名                    |
| UserPrincipalName         | 启动活动的用户的完整用户名               |
| EventSource               | 提供原始事件的数据源                        |
| SourceIPAddress           | 从中启动活动的 IP 地址                        |
| SourceIPLocation          | 启动了活动的国家/地区，从 IP 地址 |
| SourceDevice              | 启动活动的设备的主机名                  |
| DestinationIPAddress      | 活动目标的 IP 地址                            |
| DestinationIPLocation     | 活动目标所在国家/地区的 IP 地址     |
| DestinationDevice         | 目标设备的名称                                           |
| **UsersInsights**         | 相关用户的上下文根据                            |
| **DevicesInsights**       | 相关设备的上下文根据                          |
| **ActivityInsights**      | 基于分析的活动的上下文分析              |
| **InvestigationPriority** | 异常分数，介于 0-10 (0 = 良性之间，10 = 非常异常)          |
|

可以在 [UEBA 根据 reference 文档](ueba-enrichments.md)中查看 **UsersInsights**、 **DevicesInsights** 和 **ActivityInsights** 中引用的所有上下文根据。

### <a name="querying-behavior-analytics-data"></a>查询行为分析数据

使用 [KQL](/azure/data-explorer/kusto/query/)，可以查询行为分析表。

例如，如果要查找无法登录到 Azure 资源的用户的所有情况，在这种情况下，用户第一次尝试从给定的国家/地区进行连接，并且来自该国家/地区的连接不常见，但对于用户的对等，我们可以使用以下查询：

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>用户对等元数据-表和笔记本

用户对等方的元数据在威胁检测中提供重要的上下文、调查事件，并用于寻找潜在的威胁。 安全分析师可以观察用户对等方的正常活动，以确定用户的活动与他或她的对等方的活动是否异常。

Azure Sentinel 根据用户的 Azure AD 安全组成员身份、邮件列表等，计算和排名用户的对等方，并在 **UserPeerAnalytics** 表中存储1-20 对等方。 下面的屏幕截图显示了 UserPeerAnalytics 表的架构，并显示用户肯德尔 Collins 的前八位对等方。 Azure Sentinel 使用 *术语 "频率"-"反转文档频率* " (TF-IDF) 算法来规范化用于计算排名的称：较小的组，权重越高。 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="用户对等元数据表的屏幕截图":::

可以使用 Azure Sentinel GitHub 存储库中提供的 [Jupyter 笔记本](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) 来直观显示用户对等元数据。 有关如何使用笔记本的详细说明，请参阅 [引导式分析-用户安全元数据](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) 笔记本。

### <a name="permission-analytics---table-and-notebook"></a>权限分析-表和笔记本

权限分析有助于确定攻击者对组织资产造成的潜在影响。 这种影响也称为资产的 "群发 radius"。 安全分析师可以使用此信息来确定调查和事件处理的优先级。

Azure Sentinel 通过评估用户可以直接或通过组或服务主体访问的 Azure 订阅，确定给定用户对 Azure 资源持有的直接和可传递访问权限。 此信息以及用户 Azure AD 安全组成员身份的完整列表将存储在 **UserAccessAnalytics** 表中。 下面的屏幕截图显示了 UserAccessAnalytics 表中用户 Alex Johnson 的示例行。 **源实体** 是用户或服务主体帐户， **目标实体** 是源实体有权访问的资源。 " **访问级别** " 和 " **访问类型** " 的值取决于目标实体的访问控制模型。 你可以看到，Alex 有权访问 Azure 订阅 *Contoso 酒店租户*。 订阅的访问控制模型是 Azure RBAC。   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="用户访问分析表的屏幕截图":::

你可以使用 [Jupyter 笔记本](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (前面提到的同一笔记本) Azure Sentinel GitHub 存储库中所述的相同笔记本，以可视化权限分析数据。 有关如何使用笔记本的详细说明，请参阅 [引导式分析-用户安全元数据](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) 笔记本。

### <a name="hunting-queries-and-exploration-queries"></a>搜寻查询和探索查询

Azure Sentinel 基于 BehaviorAnalytics 表提供现成的一组查找查询、探索查询和工作簿。 这些工具提供了更丰富的数据，重点用于特定用例，指示异常行为。 

详细了解 Azure Sentinel 中 [的搜寻和调查关系图](./hunting.md) 。

## <a name="next-steps"></a>后续步骤
本文档介绍了 Azure Sentinel 的实体行为分析功能。 有关实现的实用指导，并使用已获得的见解，请参阅以下文章：

- 在 Azure Sentinel 中[启用实体行为分析](./enable-entity-behavior-analytics.md)。
- [寻找安全威胁](./hunting.md)。