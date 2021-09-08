---
title: Azure Sentinel 中的新增功能
description: 本文介绍了过去几个月内 Azure Sentinel 中的新增功能。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 08/09/2021
ms.openlocfilehash: ebb1ddeea3c19ae87320c6156a5a6af60c7a2d9c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434531"
---
# <a name="whats-new-in-azure-sentinel"></a>Azure Sentinel 中的新增功能

本文列出了 Azure Sentinel 中添加的最新功能，以及相关服务中提供增强 Azure Sentinel 用户体验的新功能。

若要查找 6 个月之前的项目，可查看 [Azure Sentinel 的新增功能存档](whats-new-archive.md)。 若要了解所提供的更早的功能，请参阅[技术社区博客](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)。

> [!IMPORTANT]
> 请注意，这些功能目前为预览版。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!TIP]
> Microsoft 中的威胁搜寻团队会向 [Azure Sentinel 社区](https://github.com/Azure/Azure-Sentinel)贡献查询、playbook、工作簿和笔记本，包括你的团队可调整和使用的特定[搜寻查询](https://github.com/Azure/Azure-Sentinel)。
>
> 你也可贡献内容！ 加入我们的 [Azure Sentinel 威胁猎人 GitHub 社区](https://github.com/Azure/Azure-Sentinel/wiki)吧。
>

## <a name="september-2021"></a>2021 年 9 月

- [文档中的新增内容：扩展数据连接器文档](#new-in-docs-scaling-data-connector-documentation)
- [Azure 存储帐户连接器更改](#azure-storage-account-connector-changes)

### <a name="new-in-docs-scaling-data-connector-documentation"></a>文档中的新增内容：扩展数据连接器文档

随着我们不断为 Azure Sentinel 添加越来越多的内置数据连接器，我们已重新整理数据连接器文档，以反映这一扩展。

对于大多数数据连接器，我们已将介绍单个连接器的完整文章替换为一系列通用过程以及所有当前支持的连接器的完整参考信息。

请查看 [Azure Sentinel 连接器参考](data-connectors-reference.md)，了解有关连接器的详细信息，包括相关通用过程的参考信息以及所需的额外信息和配置。

有关详细信息，请参阅：

- **概念信息**：[连接数据源](connect-data-sources.md)

- **通用操作指南文章**：

   - [连接到 Azure、Windows、Microsoft 和 Amazon 服务](connect-azure-windows-microsoft-services.md)
   - [将数据源连接到 Azure Sentinel 的数据收集器 API 以引入数据](connect-rest-api-template.md)
   - [将 CEF 格式的日志从设备获取到 Azure Sentinel](connect-common-event-format.md)
   - [使用 Syslog 从基于 Linux 的源收集数据](connect-syslog.md)
   - [使用 Log Analytics 代理将数据以自定义日志格式收集到 Azure Sentinel](connect-custom-logs.md)
   - [使用 Azure Functions 将数据源连接到Azure Sentinel](connect-azure-functions-template.md)
   - [用于创建 Azure Sentinel 自定义连接器的资源](create-custom-connector.md)

### <a name="azure-storage-account-connector-changes"></a>Azure 存储帐户连接器更改

由于 Azure 存储帐户资源配置本身发生了一些更改，因此还需要重新配置连接器。
存储帐户（父）资源具有每种存储类型的其他（子）资源：文件、表、队列和 blob。

为存储帐户配置诊断时，必须依次选择和配置以下内容：
- 父帐户资源，用于导出“事务”指标。
- 每种子存储类型的资源，用于导出所有日志和指标（请参见上表）。

你将只能看到实际为其定义了资源的存储类型。

:::image type="content" source="media/whats-new/storage-diagnostics.png" alt-text="Azure 存储诊断配置的屏幕截图。":::

## <a name="august-2021"></a>2021 年 8 月

- [高级事件搜索（公共预览版）](#advanced-incident-search-public-preview)
- [对勒索软件的融合检测（公共预览版）](#fusion-detection-for-ransomware-public-preview)
- [UEBA 数据的监视列表模板](#watchlist-templates-for-ueba-data-public-preview)
- [文件事件规范化架构（公共预览版）](#file-event-normalization-schema-public-preview)
- [文档新增内容：最佳做法指南](#new-in-docs-best-practice-guidance)

### <a name="advanced-incident-search-public-preview"></a>高级事件搜索（公共预览版）

默认情况下，事件搜索仅跨“事件 ID”、“标题”、“标记”、“所有者”和“产品名称”值运行。 Azure Sentinel 现在提供[高级搜索选项](investigate-cases.md#search-for-incidents)，可搜索更多数据，包括警报详细信息、说明、实体、策略等。

例如：

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="“事件”页面高级搜索选项的屏幕截图。":::

有关详细信息，请参阅[搜索事件](investigate-cases.md#search-for-incidents)。

### <a name="fusion-detection-for-ransomware-public-preview"></a>对勒索软件的融合检测（公共预览版）

Azure Sentinel 现在为可能的勒索软件活动提供新的融合检测，并生成标题为“可能与检测到的勒索软件活动相关的多个警报”的事件。

对于可能与勒索软件活动关联的警报，如果它们发生在特定的时间范围内，并与攻击的执行和防御规避阶段相关联，则会生成事件。 你可以使用事件中列出的警报，来分析攻击者可能会利用哪些技术来破坏主机/设备并规避检测。

支持的数据连接器包括：

- [Azure Defender（Azure 安全中心）](connect-azure-security-center.md)
- [用于终结点的 Microsoft Defender](connect-microsoft-defender-advanced-threat-protection.md)
- [Microsoft Defender for Identity](connect-azure-atp.md)
- [Microsoft Cloud App Security](connect-cloud-app-security.md)
- [Azure Sentinel 计划分析规则](detect-threats-built-in.md#scheduled)

有关详细信息，请参阅[可能与检测到的勒索软件活动相关的多个警报](fusion.md#multiple-alerts-possibly-related-to-ransomware-activity-detected-public-preview)。

### <a name="watchlist-templates-for-ueba-data-public-preview"></a>UEBA 数据的监视列表模板（公共预览版）

Azure Sentinel 现在为 UEBA 数据提供内置监视列表模板，你可以根据自己的环境自定义这些模板并在调查期间使用。

在 UEBA 监视列表中填充数据后，可以将该数据与分析规则相关联，在实体页面和调查图表中将其作为见解查看，创建自定义用途（例如跟踪 VIP 或敏感用户），等等。

监视列表模板目前包含：

- **VIP 用户**。 对组织影响最大的员工的用户帐户列表。
- **离职员工**。 已经或即将离职的员工的用户帐户列表。
- **服务帐户**。 服务帐户及其所有者的列表。
- **标识相关**。 属于同一个人的相关用户帐户的列表。
- **高价值资产**。 组织中具有关键价值的设备、资源或其他资产的列表。
- **网络映射**。 IP 子网及其各自组织上下文的列表。

有关详细信息，请参阅[使用模板新建监视列表](watchlists.md#create-a-new-watchlist-using-a-template-public-preview)和[内置监视列表架构](watchlist-schemas.md)。



### <a name="file-event-normalization-schema-public-preview"></a>文件事件规范化架构（公共预览版）

Azure Sentinel 信息模型 (ASIM) 现在支持文件事件规范化架构，该架构用于描述文件活动，例如创建、修改或删除文件或文档。 文件事件由操作系统、文件存储系统（如 Azure 文件存储）和文档管理系统（如 Microsoft SharePoint）报告。

有关详细信息，请参阅：

- [Azure Sentinel 文件事件规范化架构参考（公共预览版）](file-event-normalization-schema.md)
- [规范化和 Azure Sentinel 信息模型 (ASIM)](normalization.md)


### <a name="new-in-docs-best-practice-guidance"></a>文档新增内容：最佳做法指南

应客户和支持团队的多次要求，我们在文档中添加了一系列最佳做法指南。

有关详细信息，请参阅：

- [部署 Azure Sentinel 的先决条件](prerequisites.md)
- [Azure Sentinel 最佳做法](best-practices.md)
- [Azure Sentinel 工作区体系结构最佳做法](best-practices-workspace-architecture.md)
- [设计 Azure Sentinel 工作区体系结构](design-your-workspace-architecture.md)
- [Azure Sentinel 示例工作区设计](sample-workspace-designs.md)
- [数据收集最佳做法](best-practices-data.md)

> [!TIP]
> 你可以在相关的概念和操作方法文章中找到我们在文档中添加的更多指南。 有关详细信息，请参阅[其他最佳做法参考](best-practices.md#additional-best-practice-references)。
>

## <a name="july-2021"></a>2021 年 7 月

- [Microsoft 威胁情报匹配分析（公共预览版）](#microsoft-threat-intelligence-matching-analytics-public-preview)
- [将 Azure AD 数据与 Azure Sentinel 的 IdentityInfo 表结合使用（公共预览版）](#use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview)
- [通过 API 使用地理位置数据来扩充实体（公共预览版）](#enrich-entities-with-geolocation-data-via-api-public-preview)
- [支持 ADX 跨资源查询（公共预览版）](#support-for-adx-cross-resource-queries-public-preview)
- [监视列表正式发布](#watchlists-are-in-general-availability)
- [支持更多地区的数据驻留](#support-for-data-residency-in-more-geos)
- [Azure Defender 连接器中的双向同步（公共预览版）](#bidirectional-sync-in-azure-defender-connector-public-preview)


### <a name="microsoft-threat-intelligence-matching-analytics-public-preview"></a>Microsoft 威胁情报匹配分析（公共预览版）

Azure Sentinel 现在提供内置的“Microsoft 威胁情报匹配分析”规则，可将 Microsoft 生成的威胁情报数据与你的日志进行匹配。 此规则生成高保真警报和事件，并根据检测到的日志上下文设置适当的严重性。 检测到匹配项后，该指标还会发布到 Azure Sentinel 威胁情报存储库。

“Microsoft 威胁情报匹配分析”规则目前将域指标与以下日志源进行匹配：

- [CEF](connect-common-event-format.md)
- [DNS](connect-dns.md)
- [Syslog](connect-syslog.md)

有关详细信息，请参阅[使用匹配分析检测威胁（公共预览版）](work-with-threat-indicators.md#detect-threats-using-matching-analytics-public-preview)。

### <a name="use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview"></a>将 Azure AD 数据与 Azure Sentinel 的 IdentityInfo 表结合使用（公共预览版）

由于攻击者经常使用组织自己的用户和服务帐户，因此，在调查过程中，有关这些用户帐户的数据（包括用户标识和权限）对分析师而言至关重要。

现在，在 Azure Sentinel 工作区中[启用 UEBA](enable-entity-behavior-analytics.md) 还会将 Azure AD 数据同步到 Log Analytics 的新 IdentityInfo 表中。 Azure AD 与 IdentifyInfo 表之间的同步会创建用户配置文件数据的快照，其中包括用户元数据、组信息和分配给每个用户的 Azure AD 角色。

在调查期间以及在为组织微调分析规则以减少误报时使用 IdentityInfo 表。

有关详细信息，请参阅 UEBA 扩充参考中的 [IdentityInfo 表](ueba-enrichments.md#identityinfo-table-public-preview)和[使用 UEBA 数据分析误报](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)。

### <a name="enrich-entities-with-geolocation-data-via-api-public-preview"></a>通过 API 使用地理位置数据来扩充实体（公共预览版）

Azure Sentinel 现在提供了一个 API，可以使用地理位置信息来扩充数据。 然后可以使用地理位置数据来分析和调查安全事件。

有关详细信息，请参阅[通过 REST API 用地理位置数据扩充 Azure Sentinel 中的实体（公共预览版）](geolocation-data-api.md)和[在 Azure Sentinel 中使用实体对数据进行分类和分析](entities-in-azure-sentinel.md)。


### <a name="support-for-adx-cross-resource-queries-public-preview"></a>支持 ADX 跨资源查询（公共预览版）

Azure Sentinel 中的搜寻体验现在支持 [ADX 跨资源查询](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer)。
 
尽管 Log Analytics 仍然是使用 Azure Sentinel 执行分析的主要数据存储位置，但在某些情况下，由于成本、保留期或其他因素，需要使用 ADX 来存储数据。 此功能使客户能够搜寻更广泛的数据集并在 [Azure Sentinel 搜寻体验](hunting.md)中查看结果，包括搜寻查询、[Livestream](livestream.md) 和 Log Analytics 搜索页面。

若要查询存储在 ADX 群集中的数据，请使用 adx() 函数指定 ADX 群集、数据库名称和所需的表。 然后，可以像查询任何其他表一样查询输出。 有关详细信息，请参阅上面链接的页面。




### <a name="watchlists-are-in-general-availability"></a>监视列表正式发布

[监视列表](watchlists.md)功能现已正式发布。 借助监视列表，可使用业务数据扩充警报，创建允许列表或阻止列表以检查访问事件，并帮助调查威胁和缓解警报疲劳。

### <a name="support-for-data-residency-in-more-geos"></a>支持更多地区的数据驻留

Azure Sentinel 现在支持以下附加地区的完整数据驻留：

巴西、挪威、南非、韩国、德国、阿拉伯联合酋长国 (UAE) 和瑞士。

有关数据驻留，请参阅[受支持地区的完整列表](quickstart-onboard.md#geographical-availability-and-data-residency)。

### <a name="bidirectional-sync-in-azure-defender-connector-public-preview"></a>Azure Defender 连接器中的双向同步（公共预览版）

Azure Defender 连接器现在支持 Defender 和 Azure Sentinel 之间警报状态的双向同步。 当你关闭包含 Defender 警报的 Sentinel 事件时，该警报也将在 Defender 门户中自动关闭。

请参阅[更新的 Azure Defender 连接器的完整说明](connect-azure-security-center.md)。

## <a name="june-2021"></a>2021 年 6 月

- [规范化和 Azure Sentinel 信息模型的升级](#upgrades-for-normalization-and-the-azure-sentinel-information-model)
- [更新的服务到服务连接器](#updated-service-to-service-connectors)
- [导出和导入分析规则（公共预览版）](#export-and-import-analytics-rules-public-preview)
- [警报扩充：警报详细信息（公共预览版）](#alert-enrichment-alert-details-public-preview)
- [更多有关 playbook 的帮助！](#more-help-for-playbooks)
- [新的文档改编](#new-documentation-reorganization)

### <a name="upgrades-for-normalization-and-the-azure-sentinel-information-model"></a>规范化和 Azure Sentinel 信息模型的升级

借助 Azure Sentinel 信息模型，你可以使用和创建与源无关的内容，简化对 Azure Sentinel 工作区中数据的分析。

在本月的更新中，我们增强了规范化文档，即，提供更高级别的详细信息以及完整的 DNS、进程事件和身份验证规范化架构。

有关详细信息，请参阅：

- [规范化和 Azure Sentinel 信息模型 (ASIM)](normalization.md)（已更新）
- [Azure Sentinel 身份验证规范化架构参考（公共预览版）](authentication-normalization-schema.md)（新增！）
- [Azure Sentinel 数据规范化架构参考](normalization-schema.md)
- [Azure Sentinel DNS 规范化架构参考（公共预览版）](dns-normalization-schema.md)（新增！）
- [Azure Sentinel 进程事件规范化架构参考（公共预览版）](process-events-normalization-schema.md)（新增！）
- [Azure Sentinel 注册表事件规范化架构参考（公共预览版）](registry-event-normalization-schema.md)（新增！）


### <a name="updated-service-to-service-connectors"></a>更新的服务到服务连接器

我们最常用的两个连接器已经成为重大升级的受益者。

- [Windows 安全事件连接器（公共预览版）](connect-windows-security-events.md)现在基于新的 Azure Monitor 代理 (AMA)，让你可以更加灵活地选择要引入的数据，并以最低的成本为你提供最大的可见性。

- [Azure 活动日志连接器](connect-azure-activity.md)现在基于诊断设置管道，可提供更完整的数据，大大减少引入延迟，提高性能和可靠性。

升级不是自动的。 建议这些连接器的用户启用新版本。

### <a name="export-and-import-analytics-rules-public-preview"></a>导出和导入分析规则（公共预览版）

你现在可以将分析规则导出到 JSON 格式的 Azure 资源管理器 (ARM) 模板文件，并从这些文件导入规则，作为以代码形式管理和控制 Azure Sentinel 部署的一部分。 任何类型的[分析规则](detect-threats-built-in.md)（不只是计划的规则）都可以导出到 ARM 模板。 模板文件包含规则的所有信息，从查询到分配的 MITRE ATT&CK 策略。

有关详细信息，请参阅[在 ARM 模板中导出和导入分析规则](import-export-analytics-rules.md)。

### <a name="alert-enrichment-alert-details-public-preview"></a>警报扩充：警报详细信息（公共预览版）

除了使用实体映射和自定义详细信息扩充警报内容外，现在还可以根据警报乃至事件的特定内容，定制其呈现和显示方式。 与其他警报扩充功能一样，此功能可在[分析规则向导](detect-threats-custom.md)中配置。

有关详细信息，请参阅[在 Azure Sentinel 自定义警报的详细信息](customize-alert-details.md)。


### <a name="more-help-for-playbooks"></a>更多有关 playbook 的帮助！

有两个新文档可帮助你开始或更轻松地创建和使用 playbook。
- [向 Azure Sentinel 验证 playbook](authenticate-playbooks-to-sentinel.md) 可帮助你了解基于逻辑应用的 playbook 可以通过哪些不同的身份验证方法连接和访问 Azure Sentinel 中的信息，以及每种方法的适用场景。
- [在 playbook 中使用触发器和操作](playbook-triggers-actions.md)解释了事件触发器和警报触发器之间的区别及其适用场景，并介绍了你在 playbook 中响应事件时可以采取的一些不同的操作，包括如何访问[自定义详细信息](playbook-triggers-actions.md#work-with-custom-details)中的信息。

Playbook 文档还明确阐述了多租户 MSSP 方案。

### <a name="new-documentation-reorganization"></a>新的文档改编

本月，我们改编了 [Azure Sentinel 文档](index.yml)，将其重构为遵循常见客户旅程的直观类别。 使用筛选后的文档搜索和更新的登陆页面来浏览 Azure Sentinel 文档。

:::image type="content" source="media/whats-new/new-docs.png" alt-text="新的 Azure Sentinel 文档改编。" lightbox="media/whats-new/new-docs.png":::


## <a name="may-2021"></a>2021 年 5 月

- [Azure Sentinel PowerShell 模块](#azure-sentinel-powershell-module)
- [警报分组增强功能](#alert-grouping-enhancements)
- [Azure Sentinel 解决方案（公共预览版）](#azure-sentinel-solutions-public-preview)
- [针对 SAP 解决方案的持续威胁监视（公共预览版）](#continuous-threat-monitoring-for-sap-solution-public-preview)
- [威胁情报集成（公共预览版）](#threat-intelligence-integrations-public-preview)
- [定时警报融合（公共预览版）](#fusion-over-scheduled-alerts-public-preview)
- [SOC-ML 异常（公共预览版）](#soc-ml-anomalies-public-preview)
- [IP 实体页（公共预览版）](#ip-entity-page-public-preview)
- [活动自定义（公共预览版）](#activity-customization-public-preview)
- [搜寻仪表板（公共预览版）](#hunting-dashboard-public-preview)
- [事件团队 - 在 Microsoft Teams 中开展协作（公共预览版）](#azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview)
- [零信任 (TIC3.0) 工作簿](#zero-trust-tic30-workbook)


### <a name="azure-sentinel-powershell-module"></a>Azure Sentinel PowerShell 模块

适用于自动化日常操作任务的官方 Azure Sentinel PowerShell 模块已经发布正式版！

你可以在此处下载该模块：[PowerShell 库](https://www.powershellgallery.com/packages/Az.SecurityInsights/)。

有关详细信息，请参阅 PowerShell 文档：[Az.SecurityInsights](/powershell/module/az.securityinsights/)

### <a name="alert-grouping-enhancements"></a>警报分组增强功能

现在，你可以将分析规则配置为将警报按单个事件分组，而且该规则不仅适用于与特定实体类型匹配的警报，还适用于与所配置实体的特定警报名称、严重性或其他自定义详细信息匹配的警报。 

在分析规则向导的“事件设置”选项卡中，选择启用警报分组，然后选择“如果所选实体类型与详细信息匹配，则将警报按单个事件分组” **"** 选项。 

然后，选择要匹配的实体类型及相关详细信息：

:::image type="content" source="media/whats-new/alert-grouping-details.png" alt-text="按匹配实体的详细信息对警报进行分组。":::

有关详细信息，请参阅[警报分组](detect-threats-custom.md#alert-grouping)。

### <a name="azure-sentinel-solutions-public-preview"></a>Azure Sentinel 解决方案（公共预览版）

Azure Sentinel 现提供打包内容[解决方案](sentinel-solutions-catalog.md)，其中包含适用于 Azure Sentinel 的一个或多个数据连接器、工作簿、分析规则、playbook、搜寻查询、分析程序、监视列表以及其他组件。

该解决方案改进了产品内的可发现性、单步部署以及端到端产品方案。 有关详细信息，请参阅[发现和部署 Azure Sentinel 解决方案](sentinel-solutions-deploy.md)。

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>针对 SAP 解决方案的持续威胁监视（公共预览版）

Azure Sentinel 解决方案目前包含适用于 SAP 的持续威胁监视功能，可以让你监视 SAP 系统，从而发现业务和应用程序层中的复杂威胁。

SAP 数据连接器从整个 SAP 系统环境中流式传输大量（14 个）应用程序日志，通过 NetWeaver RFC 调用从高级业务应用程序编程 (ABAP) 收集日志，并通过 OSSAP 控制接口收集文件存储数据。 SAP 数据连接器增加了 Azure Sentinels 监视 SAP 基础结构的能力。

要将 SAP 日志引入 Azure Sentinel，必须在 SAP 环境中安装 Azure Sentinel SAP 数据连接器。 部署 SAP 数据连接器之后，部署丰富的 SAP 解决方案安全内容，以顺利深入了解组织的 SAP 环境并改进任何相关的安全操作功能。

有关详细信息，请参阅[教程：为 SAP 部署 Azure Sentinel 解决方案（公共预览版）](sap-deploy-solution.md)。

### <a name="threat-intelligence-integrations-public-preview"></a>威胁情报集成（公共预览版）

Azure Sentinel 可提供一些[使用威胁情报](./understand-threat-intelligence.md)源的不同方法，以增强安全分析师检测及确定已知威胁优先级的能力。

你现在可以从众多新发布的集成威胁情报平台 (TIP) 产品中选择使用其一连接到 TAXII 服务器，以利用可与 STIX 兼容的任何威胁情报源，以及使用可直接与 [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) 通信的任何自定义解决方案。

你还可以从 playbook 连接到威胁情报源，以便使用有助于直接调查和响应操作的 TI 信息来扩充事件。

有关详细信息，请参阅[在 Azure Sentinel 中集成威胁情报](threat-intelligence-integration.md)。

### <a name="fusion-over-scheduled-alerts-public-preview"></a>定时警报融合（公共预览版）

除了从其他数据源导入的警报外，Fusion 机器学习关联引擎现还可使用一组由其关联中的[定时分析规则](detect-threats-custom.md)生成的警报来检测多阶段攻击。

有关详细信息，请参阅 [Azure Sentinel 中的高级多阶段攻击检测](fusion.md)。

### <a name="soc-ml-anomalies-public-preview"></a>SOC-ML 异常（公共预览版）

Azure Sentinel 提供基于 SOC-ML 机器学习的异常检测功能，该功能可以识别可能会绕过检测的异常行为。

SOC-ML 使用可开箱即用的分析规则模板。 虽然异常本身并不一定表明存在恶意甚至可疑的行为，但可用于提高检测、调查和威胁搜寻的保真度。

有关详细信息，请参阅[在 Azure Sentinel 中使用 SOC-ML 异常检测威胁](soc-ml-anomalies.md)。

### <a name="ip-entity-page-public-preview"></a>IP 实体页（公共预览版）

Azure Sentinel 现支持 IP 地址实体，你可以在新的 IP 实体页中查看 IP 实体信息。

与用户和主机实体页一样，IP 页包含有关 IP 的一般信息、发现 IP 所属的活动列表等，可以为你提供更丰富的信息存储，从而加强你对安全事件的调查。

有关详细信息，请参阅[实体页](identify-threats-with-entity-behavior-analytics.md#entity-pages)。

### <a name="activity-customization-public-preview"></a>活动自定义（公共预览版）

你现在可以通过实体页为实体创建新的自定义活动（系统会在各个活动的实体页上跟踪及显示这些活动），以及你到目前为止在实体页上看到的开箱即用型活动。

有关详细信息，请参阅[在实体页时间线上自定义活动](customize-entity-activities.md)。

### <a name="hunting-dashboard-public-preview"></a>搜寻仪表板（公共预览版）

“搜寻”边栏选项卡已更新。 只需在新仪表板中单击一下，即可运行所有查询或所选子集。

通过查看结果计数、峰值或 24 小时内结果计数的变化，便可确定开始搜寻的位置。 你还可以按收藏夹、数据源、MITRE ATT&CK 策略和技巧、结果或结果增量执行排序和筛选。 查看尚未连接必要数据源的查询，并获取有关启用此类查询的建议方法。

有关详细信息，请参阅[通过 Azure Sentinel 搜寻威胁](hunting.md)。

### <a name="azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview"></a>Azure Sentinel 事件团队 - 在 Microsoft Teams 中开展协作（公共预览版）

Azure Sentinel 现支持与 Microsoft Teams 直接集成，以使你能够跨组织与外部利益干系人开展无缝协作。

直接从 Azure Sentinel 事件创建新的事件团队，以供中央通信和协调时使用。

对于高严重性、持续性的事件，将事件团队用作专用会议桥特别有用。 已使用 Microsoft Teams 进行沟通和协作的组织可以使用 Azure Sentinel 集成将安全数据直接引入其对话和日常工作中。

在 Microsoft Teams 中，新团队的事件页选项卡始终具有来自 Azure Sentinel 的最新数据，从而确保团队随手便可获得最相关的数据。

[ ![Microsoft Teams 中的事件页。](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

有关详细信息，请参阅[在 Microsoft Teams 中开展协作（公共预览版）](collaborate-in-microsoft-teams.md)。

### <a name="zero-trust-tic30-workbook"></a>零信任 (TIC3.0) 工作簿

新的 Azure Sentinel 零信任 (TIC3.0) 工作簿提供[零信任](/security/zero-trust/)原则自动可视化功能，可交叉到[受信任的 Internet 连接](https://www.cisa.gov/trusted-internet-connections) (TIC) 框架。

我们知道，合规性不仅仅是一项年度要求，各组织必须如锻炼肌肉一般持续监视配置。 Azure Sentinel 零信任工作簿使用 Microsoft 全套安全产品/服务，包括 Azure、Office 365、Teams、Intune、Windows 虚拟桌面等等。

[ ![零信任工作簿。](media/zero-trust-workbook.gif) ](media/zero-trust-workbook.gif#lightbox)

零信任工作簿：

- 可以让实现者、SecOps 分析师、评估师、安全合规决策者、MSSP 以及其他人员了解云工作负荷的安全状况。
- 配有超过 75 张控制卡，与 TIC 3.0 安全功能匹配，提供用于导航的可选 GUI 按钮。
- 经专门设计，可通过自动化、人工智能、机器学习、查询/警报生成、可视化效果、定制建议和相应的文档参考来增强人员配备。

有关详细信息，请参阅[可视化和监视数据](monitor-your-data.md)。

## <a name="april-2021"></a>2021 年 4 月

- [基于 Azure Policy 的数据连接器](#azure-policy-based-data-connectors)
- [事件时间线（公共预览版）](#incident-timeline-public-preview)

### <a name="azure-policy-based-data-connectors"></a>基于 Azure Policy 的数据连接器

借助 Azure Policy 可将一组通用的诊断日志设置应用于特定类型的所有（当前和未来）资源（你希望将这些资源的日志引入到 Azure Sentinel）。

继续进行操作，将 [Azure Policy](../governance/policy/overview.md) 的强大功能引入到数据收集配置任务，我们现在为 [Azure 存储帐户](connect-azure-storage-account.md) 资源提供另一个发布为公共预览版的 Azure Policy 增强型数据收集器。

此外，对于 [Azure Key Vault](connect-azure-key-vault.md) 和 [Azure Kubernetes 服务](connect-azure-kubernetes-service.md)，有两个预览版连接器现已正式发布 (GA)，加入了我们的 [Azure SQL 数据库](connect-azure-sql-logs.md)连接器。

### <a name="incident-timeline-public-preview"></a>事件时间线（公共预览版）

事件详细信息页上的第一个选项卡现在为“时间线”，其中显示事件中警报和书签的时间线。 事件的时间线可以帮助你更好地了解事件，并跨相关的警报和书签重建攻击者活动的时间线。

- 选择时间线中的项以查看其详细信息，而无需离开事件上下文
- 筛选时间线内容以仅显示警报或书签，或特定严重性或 MITRE 策略的项。
- 可选择“系统警报 ID”链接来查看整个记录，或者选择“事件”链接在“日志”区域中查看相关事件  。

例如：

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="事件时间线选项卡":::

有关详细信息，请参阅[教程：通过 Azure Sentinel 调查事件](investigate-cases.md)。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[加入 Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[了解警报](get-visibility.md)
