---
title: Azure Sentinel 最佳做法
description: 了解管理 Azure Sentinel 工作区时应采用的最佳做法。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: 4a47a13405402b381c1a1b3682f5d42038218959
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689147"
---
# <a name="best-practices-for-azure-sentinel"></a>Azure Sentinel 最佳做法

本最佳做法集合提供了在部署、管理和使用 Azure Sentinel 时的指南，包括指向提供详细信息的其他文章的链接。

> [!IMPORTANT]
> 部署 Azure Sentinel 之前，请查看并完成[部署前活动和先决条件](prerequisites.md)。
>


## <a name="regular-soc-activities-to-perform"></a>要执行的常规 SOC 活动

定期计划以下 Azure Sentinel 活动，以确保持续实现最佳安全做法：

### <a name="daily-tasks"></a>每日任务

- 会审和调查事件。  查看 Azure Sentinel“事件”页，检查当前配置的分析规则生成的新事件，并开始调查任何新事件。 有关详细信息，请参阅[教程：通过 Azure Sentinel 调查事件](investigate-cases.md)。

- 浏览搜寻查询和书签。 浏览所有内置查询的结果，并更新现有的搜寻查询和书签。 手动生成新事件或更新旧事件（如果适用）。  有关详细信息，请参阅：

    - [自动根据 Microsoft 安全警报创建事件](create-incidents-from-alerts.md)
    - [使用 Azure Sentinel 搜寻威胁](hunting.md)
    - [在通过 Azure Sentinel 进行搜寻期间跟踪数据](bookmarks.md)

- 分析规则。  查看并启用新的分析规则（如果适用），包括最近连接的数据连接器中新发布或新提供的规则。

- 数据连接器。 查看从每个数据连接器接收的最后一个日志的状态、日期和时间，以确保数据在流动。 检查新的连接器，并查看引入以确保没有超过设置的限制。 有关详细信息，请参阅[数据集合最佳做法](best-practices-data.md)和[连接数据源](connect-data-sources.md)。

- Log Analytics 代理。 验证服务器和工作站是否主动连接到工作区，并对任何失败的连接进行排查和修复。   有关详细信息，请参阅 [Log Analytics 代理概述](../azure-monitor/agents/log-analytics-agent.md)。

- Playbook 故障。 验证 playbook 运行状态并排查任何故障。   有关详细信息，请参阅[教程：在 Azure Sentinel 中结合自动化规则使用 playbook](tutorial-respond-threats-playbook.md)。


### <a name="weekly-tasks"></a>每周任务

- 工作簿更新。 验证任何工作簿是否具有需要安装的更新。 有关详细信息，请参阅[常用的 Azure Sentinel 工作簿](top-workbooks.md)。

- Azure Sentinel GitHub 存储库评审。 查看 [Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel) 存储库，了解环境中是否有新的或更新后有价值的资源，例如分析规则、工作簿、搜寻查询或 playbook。

- Azure Sentinel 审核。 查看 Azure Sentinel 活动，了解谁更新或删除了资源（例如分析规则、书签等）。 有关详细信息，请参阅[审核 Azure Sentinel 查询和活动](audit-sentinel-data.md)。

### <a name="monthly-tasks"></a>每月任务

- 查看用户访问权限。 查看用户的权限并检查不活动的用户。 有关详细信息，请参阅 [Azure Sentinel 中的权限](roles.md)。

- Log Analytics 工作区评审。 查看 Log Analytics 工作区数据保留策略是否仍与组织的策略保持一致。  有关详细信息，请参阅[数据保留策略](/workplace-analytics/privacy/license-expiration)和[集成 Azure 数据资源管理器以实现长期日志保留](store-logs-in-azure-data-explorer.md)。


## <a name="integrate-with-microsoft-security-services"></a>与 Microsoft 安全服务集成

Azure Sentinel 由将数据发送到工作区的组件提供支持，并且它通过与其他 Microsoft 服务集成而变得更加强大。 引入产品（例如 Microsoft Cloud App Security、Microsoft Defender for Endpoint 和 Microsoft Defender for Identity）的任何日志都允许这些服务创建检测，并反过来向 Azure Sentinel 提供这些检测。 日志也可以直接引入 Azure Sentinel，从而为事件提供更全面的描述。

例如，下图显示了 Azure Sentinel 如何从其他 Microsoft 服务、多云和合作平台中引入数据，以实现对环境的覆盖：

:::image type="content" source="media/best-practices/azure-sentinel-and-other-services.png" alt-text="Azure Sentinel 与其他 Microsoft 和合作伙伴服务集成":::

除了引入其他源中的警报和日志，Azure Sentinel 还会：

- 通过[机器学习](bring-your-own-ml.md)使用引入的信息，从而实现更好的事件关联、警报聚合和匿名检测等操作。
- 通过[工作簿](get-visibility.md)生成和呈现交互式视觉对象，显示用于管理任务和调查的趋势、相关信息和关键数据。
- 运行 [playbook](tutorial-respond-threats-playbook.md) 以处理警报、收集信息、对项执行操作以及向各种平台发送通知。
- 与合作伙伴平台（例如 ServiceNow 和 Jira）集成，以便为 SOC 团队提供基本服务。
- 从[威胁情报平台](threat-intelligence-integration.md)引入和提取扩充源，以引入有价值的数据进行调查。

##  <a name="manage-and-respond-to-incidents"></a>管理和响应事件

下图展示了事件管理和响应过程中的建议步骤。

:::image type="content" source="media/best-practices/incident-handling.png" alt-text="事件管理过程：会审。准备。修复。根除。事件后活动。":::

以下部分提供有关如何在整个过程中使用 Azure Sentinel 功能进行事件管理和响应的一些简要说明。 有关详细信息，请参阅[教程：通过 Azure Sentinel 调查事件](investigate-cases.md)。

### <a name="use-the-incidents-page-and-the-investigation-graph"></a>使用“事件”页和“调查”图

在 Azure Sentinel 中 Azure Sentinel“事件”页和“调查”图中，启动对新事件的任何会审流程 。 

发现关键实体，例如帐户、URL、IP 地址、主机名、活动、时间线等。 使用此数据了解是否有[误报](false-positives.md)，在有误报的情况下，可以直接关闭事件。

生成的任何事件都显示在“事件”页上，该页充当会审和早期调查的中心位置。 “事件”页列出了标题、严重性和相关警报、日志以及任何有意义的实体。 事件还提供快速跳转到收集的日志和与事件相关的任何工具的功能。

“事件”页与“调查”图共同发挥作用。调查图是一种交互式工具，允许用户深入了解警报以显示攻击的完整范围 。 然后，用户可以构造事件的时间线，并发现威胁链的范围。

如果发现事件为真，请直接从“事件”页采取操作，调查日志、实体并浏览威胁链。 确定威胁并创建操作计划后，请使用 Azure Sentinel 中的其他工具和[其他 Microsoft 安全服务](best-practices.md#integrate-with-microsoft-security-services)继续进行调查。


### <a name="handle-incidents-with-workbooks"></a>使用工作簿处理事件

除了[可视化和显示信息以及趋势](get-visibility.md)外，Azure Sentinel 工作簿也是有价值的调查工具。

例如，使用[调查见解](top-workbooks.md#investigation-insights)工作簿来调查特定事件以及任何关联的实体和警报。 使用此工作簿可以显示相关的日志、操作和警报，从而深入了解实体。

### <a name="handle-incidents-with-threat-hunting"></a>使用威胁搜寻处理事件

在调查并搜寻根本原因时，请运行内置的威胁搜寻查询，并检查结果中是否有任何入侵迹象。

在调查期间，或在采取修复和消除威胁的步骤后，请使用 [livestream](livestream.md) 来实时监视是否存在任何拖延的恶意事件，或者恶意事件是否仍在继续。

### <a name="handle-incidents-with-entity-behavior"></a>使用实体行为处理事件

用户可以在 Azure Sentinel 中使用实体行为，查看和调查特定实体的操作和警报（例如调查帐户和主机名）。 有关详细信息，请参阅：

- [在 Azure Sentinel 中启用用户和实体行为分析 (UEBA)](enable-entity-behavior-analytics.md)
- [使用 UEBA 数据调查事件](investigate-with-ueba.md)
- [Azure Sentinel UEBA 扩充引用](ueba-enrichments.md)

### <a name="handle-incidents-with-watchlists-and-threat-intelligence"></a>使用监视列表和威胁情报处理事件

若要最大程度地利用基于威胁情报的检测，请确保使用[威胁情报数据连接器](connect-threat-intelligence-tip.md)来引入入侵指标：

- 连接[合成图](fusion.md)和 [TI 地图警报](./understand-threat-intelligence.md)所需的数据源
- 从 [TAXII 和 TIP 平台](./connect-threat-intelligence-tip.md)引入指标

在搜寻威胁、调查日志或生成更多事件时，在分析规则中使用入侵指标。

使用将来自引入数据和外部源（例如扩充数据）组合在一起的监视列表。 例如，创建组织或最近离职的员工使用的 IP 地址范围列表。 将监视列表与 playbook 一起用于收集扩充数据，例如，将恶意 IP 地址添加到监视列表以在检测、威胁搜寻和调查期间使用。

在事件发生期间，使用监视列表包含调查数据，然后在调查完成后将其删除，以确保敏感数据不会保留在视图中。


## <a name="additional-best-practice-references"></a>其他最佳做法参考

Azure Sentinel 文档提供了更多最佳做法指南，这些指南分散在我们的文章中。 有关详细信息，请参阅以下文章：

- **管理员用户**：

    - [部署 Azure Sentinel 的预先部署活动和先决条件](prerequisites.md)
    - [Azure Sentinel 工作区体系结构最佳做法](best-practices-workspace-architecture.md)
    - [设计 Azure Sentinel 工作区体系结构](design-your-workspace-architecture.md)
    - [Azure Sentinel 示例工作区设计](sample-workspace-designs.md)
    - [数据收集最佳做法](best-practices-data.md)
    - [Azure Sentinel 成本和账单](azure-sentinel-billing.md)
    - [Azure Sentinel 中的权限](roles.md)
    - [在 Azure Sentinel 中保护 MSSP 知识产权](mssp-protect-intellectual-property.md)
    - [Azure Sentinel 中的威胁情报集成](threat-intelligence-integration.md)
    - [审核 Azure Sentinel 查询和活动](audit-sentinel-data.md)

- **分析师**：

    - [建议的 playbook](automate-responses-with-playbooks.md#recommended-playbooks)
    - [处理 Azure Sentinel 中的误报](false-positives.md)
    - [使用 Azure Sentinel 搜寻威胁](hunting.md)
    - [常用的 Azure Sentinel 工作簿](top-workbooks.md)
    - [直接检测威胁](detect-threats-built-in.md)
    - [创建自定义分析规则以检测威胁](detect-threats-custom.md)
    - [使用 Jupyter Notebook 搜寻安全威胁](notebooks.md)

有关详细信息，另请参阅我们的视频：[构造 SecOps 以获得成功：部署 Azure Sentinel 的最佳做法](https://youtu.be/DyL9MEMhqmI)

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure Sentinel，请参阅以下文章：

- [加入 Azure Sentinel](quickstart-onboard.md)
- [了解警报](get-visibility.md)