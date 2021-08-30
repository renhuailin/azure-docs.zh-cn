---
title: 了解 Azure Sentinel 中的威胁情报 | Microsoft Docs
description: 了解如何连接、管理并使用 Azure Sentinel 中的威胁情报源来分析数据、检测威胁和扩充警报。
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
ms.date: 07/12/2021
ms.author: yelevin
ms.openlocfilehash: 6ab9ecbe3b67ec933604ab1d8f6efdc7dbd8a5af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737522"
---
# <a name="understand-threat-intelligence-in-azure-sentinel"></a>理解 Azure Sentinel 中的威胁情报

## <a name="introduction-to-threat-intelligence"></a>威胁情报简介

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

网络威胁情报 (CTI) 是描述系统和用户的已知现有或潜在威胁的信息。 这类信息采用多种形式，包括详细描述特定威胁参与者的动机、基础结构和技术的书面报告，以及与已知网络威胁关联的 IP 地址、域名、文件哈希和其他项目的特定观察结果。 组织使用 CTI 为异常活动提供必要的背景信息，以便安全负责人可以快速采取措施来保护其人员、信息和其他资产。 CTI 的来源有很多，例如开源数据源、威胁情报共享社区、商业情报源，以及在组织内部安全调查中收集的本地情报。

在像 Azure Sentinel 这样的安全信息和事件管理 (SIEM) 解决方案中，最常用的 CTI 形式是威胁指标，也称为入侵指标 (IoC)。 威胁指标是将 URL、文件哈希或 IP 地址等观察项目与网络钓鱼、僵尸网络或恶意软件等已知威胁活动关联起来的数据。 这种形式的威胁情报通常称为战术性威胁情报，因为它可以大规模地应用于安全产品和自动化服务，以检测组织面临的潜在威胁并进行防范。 在 Azure Sentinel 中，你可以使用威胁指标来帮助检测在环境中观测到的恶意活动，并向安全调查人员提供上下文以帮助其决定如何响应。

可以通过以下活动将威胁情报 (TI) 集成到 Azure Sentinel 中：

- 通过启用面向各种 TI [平台](connect-threat-intelligence-tip.md)和[源](connect-threat-intelligence-taxii.md)的数据连接器，将威胁情报导入 Azure Sentinel 。
- 在“日志”和 Azure Sentinel 的“威胁情报”边栏选项卡中查看和管理导入的威胁情报  。
- 根据导入的威胁情报，使用内置的“分析”规则模板检测威胁并生成安全警报和事件 。
- 利用“威胁情报”工作簿直观显示 Azure Sentinel 中导入的威胁情报的关键信息 。

威胁情报还在其他 Azure Sentinel 体验（如“搜寻”和“Notebooks”）中提供有用的背景信息，虽然本文并未涉及，但 Ian Hellen 发布的关于 [Azure Sentinel 中的 Jupyter Notebook](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239) 的精彩博文中介绍了这些体验，其中包括 Notebooks 中 CTI 的使用 。

## <a name="import-threat-intelligence-with-data-connectors"></a>使用数据连接器导入威胁情报

就像 Azure Sentinel 中的所有其他事件数据一样，威胁指标使用数据连接器导入。 Azure Sentinel 专门提供了两个数据连接器用于获取威胁指标：一个是威胁情报 - TAXII，用于从行业标准 STIX/TAXII 源获取；另一个是威胁情报平台，用于从集成式和策展式 TI 源获取 。 你可以单独使用其中一个数据连接器，也可以两个连接器一起使用，这取决于你的组织从哪里获得威胁指标。 

请参阅 Azure Sentinel 提供的 [威胁情报集成](threat-intelligence-integration.md) 目录。

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>使用“威胁情报平台”数据连接器向 Azure Sentinel 添加威胁指标

许多组织使用威胁情报平台 (TIP) 解决方案从各种来源汇总威胁指标源，在平台内策展数据，然后选择要应用于各种安全解决方案（如网络设备、EDR/XDR 解决方案或像 Azure Sentinel 这样的 SIEM）的威胁指标。 如果组织使用[集成式 TIP 解决方案](connect-threat-intelligence-tip.md)，则可通过威胁情报平台数据连接器，使用 TIP 将威胁指标导入 Azure Sentinel。 

由于 TIP 数据连接器与 [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) 配合完成此操作，因此与 tiIndicators API 通信的任何自定义威胁情报平台也可以使用该连接器将指标发送到 Azure Sentinel（以及 Microsoft 365 Defender 等其他 Microsoft 安全解决方案）。

:::image type="content" source="media/understand-threat-intelligence/threat-intel-import-path.png" alt-text="威胁情报导入路径":::

有关与 Azure Sentinel 集成的 TIP 解决方案的详细信息，请参阅[集成式威胁情报平台产品](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products)。

需要按照以下主要步骤，从集成式 TIP 或自定义威胁情报解决方案向 Azure Sentinel 导入威胁指标：

1. 从 Azure Active Directory 获取应用程序 ID 和客户端密码 

1. 将此信息输入到 TIP 解决方案或自定义应用程序中

1. 在 Azure Sentinel 中启用“威胁情报平台”数据连接器

有关上述每个步骤的详细信息，请参阅[将威胁情报平台连接到 Azure Sentinel](connect-threat-intelligence-tip.md)。


### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>使用“威胁情报 - TAXII”数据连接器向 Azure Sentinel 添加威胁指标

威胁情报传输最广泛采用的行业标准是 [STIX 数据格式和 TAXII 协议的组合](https://oasis-open.github.io/cti-documentation/)。 如果组织从支持当前 STIX/TAXII 版本（2.0 或 2.1）的解决方案中获取威胁指标，则可以使用“威胁情报 - TAXII”数据连接器将威胁指标引入 Azure Sentinel。 “威胁情报 - TAXII”数据连接器使 Azure Sentinel 中的内置 TAXII 客户端可以从 TAXII 2.x 服务器导入威胁情报。

:::image type="content" source="media/understand-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII 导入路径":::
 
按照以下步骤将 STIX 格式的威胁指标从 TAXII 服务器导入到 Azure Sentinel：

1. 获取 TAXII 服务器 API 根和集合 ID

1. 在 Azure Sentinel 中启用“威胁情报 - TAXII”数据连接器

有关上述每个步骤的详细信息，请参阅[将 Azure Sentinel 连接到 STIX/TAXII 威胁情报源](connect-threat-intelligence-taxii.md)。

## <a name="view-and-manage-your-threat-indicators"></a>查看并管理威胁指标

可以在存储所有 Azure Sentinel 事件数据的日志的 ThreatIntelligenceIndicator 表（Azure Sentinel 组下）中查看成功导入的威胁指标，无论使用的是源还是连接器  。 该表是由其他 Azure Sentinel 功能（例如分析和工作簿）执行的威胁情报查询的基础。

结果应类似于下面所示的示例威胁指标：

:::image type="content" source="media/understand-threat-intelligence/threat-intel-sample-query.png" alt-text="示例查询数据":::

还可以在新的“威胁情报”边栏选项卡（可从 Azure Sentinel 主菜单访问）中查看和管理指标。 可以对导入的威胁指标进行排序、筛选和搜索，甚至无需编写 Log Analytics 查询。 借助这个新功能，你还可以直接在 Azure Sentinel 界面中创建威胁指标，以及执行两个最常见的威胁情报管理任务：指标标记和创建与安全调查相关的新指标。

通过标记威胁指标，可以轻松地对它们进行分组，使其更易于查找。 通常，你可以将标记应用于与特定事件相关的指标，或应用于表示来自某个已知参与者或已知攻击活动的威胁的指标。 你可以单独标记威胁指标，也可以选择多个指标，并同时对它们进行标记。 下面显示了使用事件 ID 标记多个指标的示例。 由于标记采用自由格式，建议的做法是为威胁指标标记创建标准命名约定。 可以将多个标记应用于每个指示器。

:::image type="content" source="media/understand-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="向威胁指标应用标记" lightbox="media/understand-threat-intelligence/threat-intel-tagging-indicators.png":::

有关查看和管理威胁指标的更多详细信息，请参阅[使用 Azure Sentinel 中的威胁指标](work-with-threat-indicators.md#view-your-threat-indicators-in-azure-sentinel)。

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>通过基于威胁指标的分析来检测威胁

在诸如 Azure Sentinel 之类的 SIEM 解决方案中，威胁指标最重要的用例是为威胁检测的分析规则提供支持。 这些基于指标的规则将数据源中的原始事件与威胁指标进行比较，以检测组织中的安全威胁。 在 Azure Sentinel 的“分析”中，你可以创建按计划运行的分析规则并生成安全警报。 规则由查询以及一些配置驱动，这些配置用于确定规则的运行频率、应生成安全警报和事件的查询结果类型，以及是否触发任何自动化作为响应。

你始终可以从头开始创建新的分析规则，不过，Azure Sentinel 提供了一组由 Microsoft 安全工程师创建的内置规则模板，你可以按原样使用这些模板，也可以根据需要进行修改。 你可以轻松识别使用威胁指标的规则模板，因为它们的标题都以“TI 将…”开头。 所有这些规则模板的运行方式都相似，唯一的区别在于所用的威胁指标类型（域、电子邮件、文件哈希、IP 地址或 URL）以及要匹配的事件类型。 每个模板都列出了规则起作用所需的数据源，因此你可以一目了然地看到是否已在 Azure Sentinel 中导入了必要的事件。 编辑并保存现有规则模板或创建新规则时，该规则会默认启用。

可以在 Azure Sentinel 的“分析”部分的“活动规则”选项卡中找到已启用的规则 。 你可以在其中编辑、启用、禁用、复制或删除活动规则。 新规则在激活后立即运行，并从那时起按定义的计划运行。

根据默认设置，规则每次按计划运行时，发现的任何结果都会生成安全警报。 可以在 Azure Sentinel 的“日志”部分，Azure Sentinel 组下的 SecurityAlert表中查看 Azure Sentinel 中的安全警报。

在 Azure Sentinel 中，根据分析规则生成的警报还会生成安全事件，这些事件可以在 Azure Sentinel 菜单的“威胁管理”下的“事件”中找到。 安全操作团队将对事件进行会审和调查，以确定适当的响应操作。 你可以在此[教程：使用 Azure Sentinel 调查事件](./investigate-cases.md)中找到详细信息。

有关在分析规则中使用威胁指标的更多详细信息，请参阅[使用 Azure Sentinel 中的威胁指标](work-with-threat-indicators.md#detect-threats-with-threat-indicator-based-analytics)。

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>工作簿提供有关威胁情报的见解

工作簿提供功能强大的交互式仪表板，让你能够深入了解 Azure Sentinel 的各个方面，威胁情报也不例外。 可以使用内置的威胁情报工作簿直观显示威胁情报的关键信息，并且可以根据业务需求轻松自定义工作簿。 甚至可以结合许多不同的数据源创建新的仪表板，以便通过独特的方式可视化数据。 Azure Sentinel 工作簿基于 Azure Monitor 工作簿，因此，系统已经提供大量文档以及更多模板。 介绍如何[使用 Azure Monitor 工作簿创建交互式报表](../azure-monitor/visualize/workbooks-overview.md)的这篇文章是一个很好的起点。 

GitHub 上还有一个内容丰富的 [Azure Monitor 工作簿社区](https://github.com/microsoft/Application-Insights-Workbooks)，你可以下载更多模板并贡献自己的模板。

有关使用和自定义威胁情报工作簿的更多详细信息，请参阅[使用 Azure Sentinel 中的威胁指标](work-with-threat-indicators.md#workbooks-provide-insights-about-your-threat-intelligence)。

## <a name="next-steps"></a>后续步骤
本文档介绍了 Azure Sentinel 的威胁情报功能，包括“威胁情报”边栏选项卡。 有关如何使用 Azure Sentinel 的威胁情报功能的实用指导，请参阅以下文章：

- 将 Azure Sentinel 连接到 [STIX/TAXII 威胁情报源](./connect-threat-intelligence-taxii.md)。
- [将威胁情报平台连接](./connect-threat-intelligence-tip.md)到 Azure Sentinel。
- 查看哪些 [TIP 平台、TAXII 源和扩充](threat-intelligence-integration.md)可轻松与 Azure Sentinel 集成。
- 在整个 Azure Sentinel 体验中[使用威胁指标](work-with-threat-indicators.md)。
- 使用 Azure Sentinel 中的[内置](./detect-threats-built-in.md)分析规则或[自定义](./detect-threats-custom.md)分析规则检测威胁
- 在 Azure Sentinel 中[调查事件](./investigate-cases.md)。
