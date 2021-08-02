---
title: Azure Sentinel 中的新增功能
description: 本文介绍了过去几个月内 Azure Sentinel 中的新增功能。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 6289a142e98b347f3295b8961ee1518ce8499eb4
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110539459"
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

有关详细信息，请参阅[警报分组](tutorial-detect-threats-custom.md#alert-grouping)。

### <a name="azure-sentinel-solutions-public-preview"></a>Azure Sentinel 解决方案（公共预览版）

Azure Sentinel 现提供打包内容[解决方案](sentinel-solutions-catalog.md)，其中包含适用于 Azure Sentinel 的一个或多个数据连接器、工作簿、分析规则、playbook、搜寻查询、分析程序、监视列表以及其他组件。

该解决方案改进了产品内的可发现性、单步部署以及端到端产品方案。 有关详细信息，请参阅[发现和部署 Azure Sentinel 解决方案](sentinel-solutions-deploy.md)。

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>针对 SAP 解决方案的持续威胁监视（公共预览版）

Azure Sentinel 解决方案目前包含适用于 SAP 的持续威胁监视功能，可以让你监视 SAP 系统，从而发现业务和应用程序层中的复杂威胁。

SAP 数据连接器从整个 SAP 系统环境中流式传输大量（14 个）应用程序日志，通过 NetWeaver RFC 调用从高级业务应用程序编程 (ABAP) 收集日志，并通过 OSSAP 控制接口收集文件存储数据。 SAP 数据连接器增加了 Azure Sentinels 监视 SAP 基础结构的能力。

要将 SAP 日志引入 Azure Sentinel，必须在 SAP 环境中安装 Azure Sentinel SAP 数据连接器。 部署 SAP 数据连接器之后，部署丰富的 SAP 解决方案安全内容，以顺利深入了解组织的 SAP 环境并改进任何相关的安全操作功能。

有关详细信息，请参阅[教程：为 SAP 部署 Azure Sentinel 解决方案（公共预览版）](sap-deploy-solution.md)。

### <a name="threat-intelligence-integrations-public-preview"></a>威胁情报集成（公共预览版）

Azure Sentinel 可提供一些[使用威胁情报](import-threat-intelligence.md)源的不同方法，以增强安全分析师检测及确定已知威胁优先级的能力。

你现在可以从众多新发布的集成威胁情报平台 (TIP) 产品中选择使用其一连接到 TAXII 服务器，以利用可与 STIX 兼容的任何威胁情报源，以及使用可直接与 [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) 通信的任何自定义解决方案。

你还可以从 playbook 连接到威胁情报源，以便使用有助于直接调查和响应操作的 TI 信息来扩充事件。

有关详细信息，请参阅[在 Azure Sentinel 中集成威胁情报](threat-intelligence-integration.md)。

### <a name="fusion-over-scheduled-alerts-public-preview"></a>定时警报融合（公共预览版）

除了从其他数据源导入的警报外，Fusion 机器学习关联引擎现还可使用一组由其关联中的[定时分析规则](tutorial-detect-threats-custom.md)生成的警报来检测多阶段攻击。

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

有关详细信息，请参阅[教程：可视化和监视数据](tutorial-monitor-your-data.md)。

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

有关详细信息，请参阅[教程：通过 Azure Sentinel 调查事件](tutorial-investigate-cases.md)。

## <a name="march-2021"></a>2021 年 3 月

- [在视图模式中将工作簿设置为自动刷新](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Azure 防火墙的新检测](#new-detections-for-azure-firewall)
- [自动化规则和事件触发的 playbook（公共预览版）](#automation-rules-and-incident-triggered-playbooks-public-preview)（包括所有新的 playbook 文档）
- [新警报扩充：增强的实体映射和自定义详细信息（公共预览版）](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview)
- [打印 Azure Sentinel 工作簿或另存为 PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [事件筛选器和排序首选项现已保存在会话中（公共预览版）](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365 Defender 事件集成（公共预览版）](#microsoft-365-defender-incident-integration-public-preview)
- [使用 Azure Policy 的新 Microsoft 服务连接器](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>在视图模式中将工作簿设置为自动刷新

Azure Sentinel 用户现在可以使用新的 [Azure Monitor 功能](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555)在视图会话期间自动刷新工作簿数据。

在每个工作簿或工作簿模板中，选择 :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false":::“自动刷新”以显示间隔选项。 选择要用于当前视图会话的选项，然后选“应用”。

- 支持的刷新间隔范围为“5 分钟”到“1 天” 。
- 默认情况下，自动刷新处于关闭状态。 为了优化性能，自动刷新会在每次关闭工作簿时被关闭，并且不会在后台运行。 当你下次打开工作簿时，请根据需要重新启用自动刷新。
- 在编辑工作簿时，自动刷新会暂停，且每次从编辑模式切换回视图模式时，自动刷新间隔将重新开始。

    如果选择 :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::“刷新”按钮手动刷新工作簿，则间隔也将重新开始。

有关详细信息，请参阅[教程：可视化和监视数据](tutorial-monitor-your-data.md)和 [Azure Monitor 文档](../azure-monitor/visualize/workbooks-overview.md)。

### <a name="new-detections-for-azure-firewall"></a>Azure 防火墙的新检测

已将 Azure 防火墙的几个现成检测添加到 Azure Sentinel 中的[分析](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats)区域。 如果内部网络中的计算机尝试查询或连接到与已知 IOC 关联的 Internet 域名或 IP 地址（如检测规则查询中所定义），则这些新的检测将允许安全团队获取警报。

新检测包括：

- [Solorigate 网络信标](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [已知 GALLIUM 域和哈希](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [已知 IRIDIUM IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [已知 Phosphorus 组域/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [包含在 DCU 撤销中的 THALLIUM 域](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [已知与 ZINC 相关的 maldoc 哈希](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [已知 STRONTIUM 组域](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM - 域和 IP IOC - 2021 年 3 月](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Azure 防火墙的检测会持续添加到内置模板库。 若要获取最新的 Azure 防火墙检测，请在“规则模板”下，按“Azure 防火墙”筛选“数据源”  ：

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="分析效率工作簿中的新检测":::

有关详细信息，请参阅 [Azure Sentinel 中的 Azure 防火墙的新检测](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958)。

### <a name="automation-rules-and-incident-triggered-playbooks-public-preview"></a>自动化规则和事件触发的 playbook（公共预览版）

自动化规则是 Azure Sentinel 中的一种新概念，使你能够集中管理事件处理的自动化。 除了允许向事件分配 playbook（之前只有警报），自动化规则还允许一次性自动执行多个分析规则的响应，自动标记、分配或关闭事件而无需 playbook，并控制执行操作的顺序。 自动化规则可简化 Azure Sentinel 中的自动化使用，并能够简化事件业务流程进程的复杂工作流。

通过此[自动化规则完整说明](automate-incident-handling-with-automation-rules.md)了解更多详细信息。

如上所述，除了警报触发器外，现在还可以用事件触发器激活 playbook。 事件触发器为 playbook 提供了更大的一组输入来处理（因为事件还包括所有警报和实体数据），为响应工作流提供了更强大的功能和灵活性。 事件触发的 playbook 通过从自动化规则调用来激活。

详细了解 [playbook 的增强功能](automate-responses-with-playbooks.md)，以及如何使用 playbook 和自动化规则[创建响应工作流](tutorial-respond-threats-playbook.md)。

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details-public-preview"></a>新警报扩充：增强的实体映射和自定义详细信息（公共预览版）

通过两种新方式增强警报，使其更有用且信息更丰富。

首先，将实体映射提升到下一级别。 现在可以将几乎 20 种实体从用户、主机和 IP 地址映射到文件和进程、邮箱、Azure 资源和 IoT 设备。 还可以对每个实体使用多个标识符，以增强其唯一标识。 这为事件提供了一个更丰富的数据集，从而提供更广泛的关联和更强大的调查。 [了解在警报中映射实体的新方法](map-data-fields-to-entities.md)。

[阅读有关实体的详细信息](entities-in-azure-sentinel.md)，并查看[可用实体及其标识符的完整列表](entities-reference.md)。

通过自定义警报以使原始事件呈现详细信息，更大程度地提高调查和响应功能。 使事件内容可见，从而为响应和调查安全威胁提供更强大的功能和灵活性。 [了解如何在警报中呈现自定义详细信息](surface-custom-details-in-alerts.md)。



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>打印 Azure Sentinel 工作簿或另存为 PDF

现在，可以打印 Azure Sentinel 工作簿，还可以将其导出到 PDF 并保存在本地或共享。

在工作簿中，选择选项菜单 > :::image type="icon" source="media/whats-new/print-icon.png" border="false":::“打印内容”。 然后选择打印机，或根据需要选择“另存为 PDF”。

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="打印工作簿或另存为 PDF。":::

有关详细信息，请参阅[教程：可视化和监视数据](tutorial-monitor-your-data.md)。

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>事件筛选器和排序首选项现已保存在会话中（公共预览版）

现在，事件筛选器和排序在整个 Azure Sentinel 会话中得以保存，即使在导航到产品的其他区域时亦是如此。
只要仍处于相同的会话中，导航回 Azure Sentinel 中的[事件](tutorial-investigate-cases.md)区域即可显示筛选器和排序与离开时一样。

> [!NOTE]
> 离开 Azure Sentinel 或刷新浏览器后，不会保存事件筛选器和排序。

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365 Defender 事件集成（公共预览版）

通过 Azure Sentinel 的 [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) 事件集成，可将所有 M365D 事件都流式传输到 Azure Sentinel，并使它们在这两个门户之间保持同步。 M365D 之前称为 Microsoft 威胁防护 (MTP)，来自它的事件包括所有关联警报、实体和相关信息，其中提供了足够的上下文来在 Azure Sentinel 中执行会审和初步调查。 传输到 Sentinel 后，事件将与 M365D 保持双向同步，让你能够在事件调查中利用这两个门户的优势。

同时使用 Azure Sentinel 和 Microsoft 365 Defender 可让你这两者的最佳优势。 你可获得 SIEM 提供的跨组织整个信息资源范围的丰富见解，还可获得 XDR 为保护 Microsoft 365 资源而提供的量身定制的深度调查能力，这两者协调同步，实现无缝 SOC 操作。

有关详细信息，请参阅 [Microsoft 365 Defender 与 Azure Sentinel 集成](microsoft-365-defender-sentinel-integration.md)。

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>使用 Azure Policy 的新 Microsoft 服务连接器

[Azure Policy](../governance/policy/overview.md) 是一项 Azure 服务，它让你能够使用策略来强制实施和控制资源的属性。 使用策略可确保资源符合 IT 管理标准。

在策略可控制的资源属性中，涉及到诊断和审核日志的创建与处理。 Azure Sentinel 现在使用 Azure Policy，可将一组通用的诊断日志设置应用于特定类型的所有（当前和未来）资源，其中你希望将此类型的日志引入到 Azure Sentinel 中。 得益于 Azure Policy，你无需再逐个资源地设置诊断日志设置。

基于 Azure Policy 的连接器现可用于以下 Azure 服务：
- [Azure Key Vault](connect-azure-key-vault.md)（公共预览版）
- [Azure Kubernetes 服务](connect-azure-kubernetes-service.md)（公共预览版）
- [Azure SQL 数据库/服务器](connect-azure-sql-logs.md) (GA)

客户仍然可为特定实例手动发送日志，不过无需使用策略引擎。

## <a name="february-2021"></a>2021 年 2 月

- [网络安全成熟度模型认证 (CMMC) 工作簿](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [第三方数据连接器](#third-party-data-connectors)
- [实体页中的 UEBA 见解（公共预览版）](#ueba-insights-in-the-entity-page-public-preview)
- [改进的事件搜索（公共预览版）](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>网络安全成熟度模型认证 (CMMC) 工作簿

Azure Sentinel CMMC 工作簿提供了一种机制，用于查看与整个 Microsoft 产品组合（包括 Microsoft 安全产品/服务、Office 365、Teams、Intune、Windows 虚拟桌面等）中的 CMMC 控件一致的日志查询。

CMMC 工作簿使安全架构师、工程师、安全操作分析员、管理者和 IT 专业人员能够在态势感知的情况下了解到云工作负载安全状况。 此外，还提供了有关选择、设计、部署和配置 Microsoft 产品/服务的建议，以便与相应的 CMMC 要求和实践保持一致。

即使不需要遵守 CMMC，CMMC 工作簿也有助于构建安全运营中心、开发警报、直观呈现威胁和提供对工作负载的态势感知。

访问 Azure Sentinel 的“工作簿”区域中的 CMMC 工作簿。 选择“模板”，然后搜索“CMMC” 。

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="打开和关闭 CMMC 工作簿指南" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


有关详细信息，请参阅：

- [Azure Sentinel 网络安全成熟度模型认证 (CMMC) 工作簿](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [教程：可视化和监视数据](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>第三方数据连接器

我们的第三方集成集合不断扩大，最近 2 个月添加了 30 款连接器。 列表如下：

- [Agari Phishing Defense 和 Agari Brand Protection](connect-agari-phishing-defense.md)
- [Akamai 安全事件](connect-akamai-security-events.md)
- [Alsid for Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace（之前称为 G Suite）](connect-google-workspace.md)
- [Imperva WAF 网关](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS 日志](connect-nxlog-dns.md)
- [NXLog Linux 审核](connect-nxlog-linuxaudit.md)
- [Onapsis 平台](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys 漏洞管理知识库](connect-data-sources.md)
- [Salesforce 服务云](connect-salesforce-service-cloud.md)
- [SonicWall 防火墙](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>实体页中的 UEBA 见解（公共预览版）

Azure Sentinel 实体详细信息页提供了一个[见解窗格](identify-threats-with-entity-behavior-analytics.md#entity-insights)，其中显示了有关实体的行为见解，有助于快速识别异常和安全威胁。

如果[已启用 UEBA](ueba-enrichments.md)，并且时间范围设为至少 4 天，那么此见解窗格现在还将包括有关 UEBA 见解的以下新部分：

|部分  |说明  |
|---------|---------|
|**UEBA 见解**     | 汇总异常用户活动： <br>- 跨地理位置、设备和环境<br>- 跨时间和频率（与用户自己的历史记录相比） <br>- 与对等方的行为进行比较 <br>- 与组织的行为进行比较     |
|**基于安全组成员身份的用户对等方**     |   列出基于 Azure AD 安全组成员身份的用户对等方，为安全操作团队提供共享类似权限的其他用户的列表。  |
|**Azure 订阅的用户访问权限**     |     显示用户对 Azure 订阅的访问权限 - 可直接访问或通过 Azure AD 组/服务主体访问。   |
|**与用户相关的威胁指示器**     |  列出与用户活动中表示的 IP 地址相关的已知威胁的集合。 威胁按威胁类型和系列列出，并由 Microsoft 威胁情报服务进行扩充。       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>改进的事件搜索（公共预览版）

我们改进了 Azure Sentinel 事件搜索体验，让你能够在调查特定威胁时通过事件更快地导航。

现在，在 Azure Sentinel 中搜索事件时，可按以下事件详细信息进行搜索：

- ID
- 标题
- 产品
- 所有者
- 标记

## <a name="january-2021"></a>2021 年 1 月

- [分析规则向导：改进的查询编辑体验（公共预览版）](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Az.SecurityInsights PowerShell 模块（公共预览版）](#azsecurityinsights-powershell-module-public-preview)
- [SQL 数据库连接器](#sql-database-connector)
- [Dynamics 365 连接器（公共预览版）](#dynamics-365-connector-public-preview)
- [改进的事件注释](#improved-incident-comments)
- [专用 Log Analytics 群集](#dedicated-log-analytics-clusters)
- [逻辑应用托管标识](#logic-apps-managed-identities)
- [通过分析规则预览关系图改进的规则优化](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>分析规则向导：改进的查询编辑体验（公共预览版）

现在，Azure Sentinel 的计划分析规则向导对编写和编辑查询提供了以下增强功能：

-   可扩展的编辑窗口，提供更多屏幕空间来查看查询。
-   查询代码中突出显示关键字。
-   扩展了自动补全支持。
-   实时查询验证。 现在，查询中的错误在滚动条中显示为红色块，在“设置规则逻辑”选项卡名称中显示为一个红点。 此外，不能保存带有错误的查询。

有关详细信息，请参阅[教程：创建自定义分析规则以检测威胁](tutorial-detect-threats-custom.md)。
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Az.SecurityInsights PowerShell 模块（公共预览版）

Azure Sentinel 现支持新的 PowerShell 模块 [Az SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/)。

Az.SecurityInsights 模块支持常见的 Azure Sentinel 用例，例如与事件交互来更改状态、严重性和所有者等内容，为事件添加注释和标签，以及创建书签。

虽然建议为 CI/CD 管道使用 [Azure 资源管理器 (ARM)](../azure-resource-manager/templates/index.yml) 模板，但 Az.SecurityInsights 模块对于部署后的任务非常有用，而且面向 SOC 自动化。  例如，SOC 自动化可能包含配置数据连接器、创建分析规则或向分析规则添加自动化操作的步骤。

有关详细信息（包括可用 cmdlet 、参数说明和示例的完整列表和描述），请参阅 [Az.SecurityInsights PowerShell 文档](/powershell/module/az.securityinsights/)。

### <a name="sql-database-connector"></a>SQL 数据库连接器

Azure Sentinel 现提供 Azure SQL 数据库连接器，可将数据库的审核和诊断日志流式传输到 Azure Sentinel，并持续监视所有实例中的活动。

Azure SQL 是一个完全托管的平台即服务 (PaaS) 数据库引擎，可在没有用户参与的情况下处理大部分的数据库管理功能，例如升级、修补、备份和监视。

有关详细信息，请参阅[连接 Azure SQL 数据库诊断和审核日志](connect-azure-sql-logs.md)。

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365 连接器（公共预览版）

Azure Sentinel 现提供 Microsoft Dynamics 365 连接器，可将 Dynamics 365 应用程序的用户、管理员和支持活动日志收集到 Azure Sentinel 中。 你可使用此数据来帮助审核正在执行的所有数据处理操作，并分析其是否存在安全漏洞。

有关详细信息，请参阅[将 Dynamics 365 活动日志连接到 Azure Sentinel](connect-dynamics-365.md)。

### <a name="improved-incident-comments"></a>改进的事件注释

分析员使用事件注释来协作处理事件，从而手动记录过程和步骤，或将其作为 playbook 的一部分进行记录。 

我们改进了事件注释体验，让你能够设置注释的格式，还可编辑或删除现有注释。

有关详细信息，请参阅[从 Microsoft 安全警报自动创建事件](create-incidents-from-alerts.md)。
### <a name="dedicated-log-analytics-clusters"></a>专用 Log Analytics 群集

Azure Sentinel 现支持将专用 Log Analytics 群集作为部署选项。 建议在以下情况中考虑使用专用群集：

- 每天向 Azure Sentinel 工作区引入超过 1 TB 的数据
- Azure 注册中有多个 Azure Sentinel 工作区

当同一群集上有多个工作区时，可通过专用群集使用客户管理的密钥、密码箱、双重加密和更快跨工作区查询等功能。

有关详细信息，请参阅 [Azure Monitor 日志专用群集](../azure-monitor/logs/logs-dedicated-clusters.md)。

### <a name="logic-apps-managed-identities"></a>逻辑应用托管标识

Azure Sentinel 现支持对 Azure Sentinel 逻辑应用连接器使用托管标识，这样你就可直接向特定 playbook 授予权限以在 Azure Sentinel 上操作，而无需创建额外的标识。

- 如果没有托管标识，逻辑应用连接器需要具有 Azure Sentinel RBAC 角色的单独标识，才能在 Azure Sentinel 上运行。 单独的标识可以是 Azure AD 用户或服务主体，例如 Azure AD 注册的应用程序。

- 如果在逻辑应用中打开托管标识支持，会将逻辑应用注册到 Azure AD 并提供对象 ID。 请使用 Azure Sentinel 中的对象 ID 在 Azure Sentinel 工作区中为逻辑应用分配 Azure RBAC 角色。 

有关详细信息，请参阅：

- [在 Azure 逻辑应用中用托管标识进行身份验证](../logic-apps/create-managed-service-identity.md)
- [Azure Sentinel 逻辑应用连接器文档](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>通过分析规则预览关系图改进的规则优化（公共预览版）

Azure Sentinel 现可帮助你更好地优化分析规则，从而帮助提高其准确性并减少干扰。

在“设置规则逻辑”选项卡上编辑分析规则后，请在右侧找到“结果模拟”区域 。 

选择“使用当前数据进行测试”，让 Azure Sentinel 对分析规则的最后 50 次运行进行模拟。 这会生成一个图，显示该规则根据所评估的原始事件数据本会生成的警报的平均数量。 

有关详细信息，请参阅[定义规则查询逻辑和配置设置](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)。

## <a name="december-2020"></a>2020 年 12 月

- [80 个新的内置搜寻查询](#80-new-built-in-hunting-queries)
- [Log Analytics 代理改进](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 个新的内置搜寻查询
 
Azure Sentinel 的内置搜寻查询可让 SOC 分析员缩小当前检测覆盖范围的差距，并激发新的搜寻线索。

Azure Sentinel 的本次更新包括新的搜寻查询，它们可覆盖整个 MITRE ATT&CK 框架矩阵：

- **集合**
- **命令和控制**
- **凭据访问**
- **发现**
- **执行**
- **外泄**
- **影响**
- **初始访问**
- **持久性**
- **特权提升**

添加的搜寻查询旨在帮助查找环境中的可疑活动。 尽管它们可能既会返回合法活动，又会返回潜在的恶意活动，但它们在指导搜寻方面很有用。 

如果在运行这些查询后，你对结果很有信心，那么你可能会想将这些结果转换为分析规则，或者将搜寻结果添加到现有或新的事件中。

所有添加的查询均可通过 Azure Sentinel 搜寻页面获得。 有关详细信息，请参阅[通过 Azure Sentinel 搜寻威胁](hunting.md)。

### <a name="log-analytics-agent-improvements"></a>Log Analytics 代理改进

Azure Sentinel 用户会从以下 Log Analytics 代理改进中受益：

- 支持更多操作系统，包括 CentOS 8、RedHat 8 和 SUSE Linux 15。
- 除了 Python 2 外，还支持 Python 3

Azure Sentinel 使用 Log Analytics 代理向工作区发送事件，包括 Windows 安全事件、Syslog 事件和 CEF 日志等。

> [!NOTE]
> Log Analytics 代理有时被称为 OMS 代理或 Microsoft Monitoring Agent (MMA)。 
> 

有关详细信息，请参阅 [Log Analytics 文档](../azure-monitor/agents/log-analytics-agent.md)和 [Log Analytics 代理发行说明](https://github.com/microsoft/OMS-Agent-for-Linux/releases)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[加入 Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[了解警报](quickstart-get-visibility.md)
