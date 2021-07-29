---
title: Azure Sentinel 成本和账单 | Microsoft Docs
description: 了解 Azure Sentinel 定价模型、估算和管理 Azure Sentinel 成本，并且了解成本和帐单。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 06/03/2021
ms.openlocfilehash: 0d9c016741fcdb36a2803eaf9705e9dc2ea1782b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950295"
---
# <a name="azure-sentinel-costs-and-billing"></a>Azure Sentinel 成本和账单

Azure Sentinel 使用广泛的查询语言在数秒钟内分析大量操作数据，与之交互并从中获取见解。 Azure Sentinel 将待分析的数据存储在 Azure Monitor Log Analytics 工作区中。

在 Log Analytics 工作区上启用时，Azure Sentinel 将自动分析工作区获取的所有数据，并针对工作区获取和存储的数据量进行计费。 本文描述了如何监视、理解 Azure Sentinel 和相关的 Log Analytics 工作区的用量和成本以及如何节约使用。

## <a name="azure-sentinel-pricing-model"></a>Azure Sentinel 定价模型

Azure Sentinel 提供灵活、可预测的定价模型。 有关详细信息，请参阅 [Azure Sentinel 定价页面](https://azure.microsoft.com/pricing/details/azure-sentinel/)。 要了解相关的 Log Analytics 费用，请参阅 [Azure Monitor Log Analytics 定价](https://azure.microsoft.com/pricing/details/log-analytics/)。

### <a name="pay-as-you-go-and-commitment-tiers"></a>即用即付和承诺层级

有两种方法可以支付 Azure Sentinel 服务：

即用即付是默认模型，基于存储的实际数据量，并且可以选择超过 90 天的数据保留。 数据卷以 GB 进行测量（10^9 字节）。

Log Analytics 和 Azure Sentinel 也有承诺层定价，以前称为容量预留，与即用即付定价相比，可预测性更高，节省高达 65%。 使用承诺层定价，可以从最低 100 GB/天开始购买承诺使用量。 超过承诺级别的任何使用量都按所选的承诺层级费率进行计费。 例如，如果承诺层级为 100GB/天，会按照 100GB/天的承诺数据量向你计费，对于每天超出的数据量 (GB)，则按照该层级的折扣价格进行计费。

可以随时提高承诺层级，也可以每隔 31 天降低一次承诺层级，以根据数据量的增加或减少来优化成本。 要查看当前的 Azure Sentinel 定价层级，请在 Azure Sentinel 左侧导航中选择“设置”，然后选择“定价”选项卡。您的当前定价层标记为“当前层”。  

要设置和更改承诺层级，请参阅[设置或更改定价层级](#set-or-change-pricing-tier)。

### <a name="costs-for-other-services"></a>其他服务的成本

Azure Sentinel 与许多其他 Azure 服务集成，以提供增强的功能。 这些服务包括 Azure 逻辑应用、Azure Notebooks 和自带机器学习 (BYOML) 模型。 其中部分服务可能会有额外的费用。 一 Azure Sentinel 的数据连接器和解决方案使用 Azure Functions 进行数据引入，这也会产生单独的关联成本。

有关这些服务的定价详细信息，请参阅：

- [自动化逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)
- [Notebooks 定价](https://azure.microsoft.com/pricing/details/machine-learning/)
- [BYOML 定价](https://azure.microsoft.com/pricing/details/machine-learning-studio/)
- [Azure Functions 定价](https://azure.microsoft.com/en-us/pricing/details/functions/)

你使用的其他任何服务都有关联的成本。

### <a name="data-retention-costs"></a>数据保留成本

在 Log Analytics 工作区上启用 Azure Sentinel 后，可以在最初的 90 天内免费保留工作区中引入的所有数据。 保留天数超过 90 天后，将按照 [Log Analytics 保留价格](https://azure.microsoft.com/pricing/details/monitor/)标准进行计费。

你可以为各个数据类型指定不同的保留设置。 有关详细信息，请参阅[按数据类型设置保留期](../azure-monitor/logs/manage-cost-storage.md#retention-by-data-type)。

### <a name="free-trial"></a>免费试用

可以在新的或现有的 Log Analytics 工作区上启用 Azure Sentinel，在最初的 31 天内没有额外费用。 在免费试用期间，仍然会收取与 Log Analytics、自动化和 BYOML 相关的费用。 在经过 31 天后，使用量按 [Azure Sentinel 定价](https://azure.microsoft.com/pricing/details/azure-sentinel)来计费。 

### <a name="free-data-sources"></a>免费数据源

以下数据源对于 Azure Sentinel 是免费的：

- Azure 活动日志。
- Office 365 审核日志，包括所有 SharePoint 活动、Exchange 管理员活动和 Teams。
- Microsoft Defender 警报，包括来自 Azure Defender、Microsoft 365 Defender、Microsoft Defender for Office 365、Microsoft Defender for Identity 和 Microsoft Defender for Endpoint 的警报。
- Azure 安全中心和 Microsoft Cloud App Security (MCAS) 警报。 但是，某些 Microsoft 365 Defender、MCAS、Azure Active Directory (Azure AD) 和 Azure 信息保护 (AIP) 数据类型的原始日志将被计费。

下表列出了可在 Azure Sentinel 中启用的免费数据源。 某些数据连接器（例如 Microsoft 365 Defender 和 MCAS）包括免费和付费数据类型。

| Azure Sentinel 数据连接器   | 数据类型 | 免费或付费 |
|-------------------------------------|--------------------------------|------------------|
| **Azure 活动日志**         | AzureActivity                  | 免费             |
| **Office 365**                     | OfficeActivity (SharePoint)    | 免费|
|| OfficeActivity (Exchange)|免费|
|| OfficeActivity (Teams)          | 免费|
| **Azure Defender**                  | SecurityAlert (ASC)             | 免费             |
| **适用于 IoT 的 Azure Defender**          | SecurityAlert (ASC for IoT)     | 免费             |
| **Microsoft 365 Defender**          | SecurityIncident | 免费|
||SecurityAlert| 免费|
||DeviceEve                    | 已付|
||DeviceFileEvents                | 已付|
||DeviceImageLoadEvents           | 已付|
||DeviceInfo                      | 已付|
||DeviceLogonEvents               | 已付|
||DeviceNetworkEvents             | 已付|
||DeviceNetworkInfo               | 已付|
||DeviceProcessEvents             | 已付|
||DeviceRegistryEvents            | 已付|
||DeviceFileCertificateInfo       | 已付|
| **用于终结点的 Microsoft Defender** | SecurityAlert (MDATP)          | 免费             |
| **Microsoft Defender for Identity** | SecurityAlert (AATP)           | 免费             |
| **Microsoft Cloud App Security**   | SecurityAlert (MCAS)           | 免费             |
||MCASShadowITReporting           | 已付|

对于同时包含免费和付费数据类型的数据连接器，你可以选择要启用的数据类型。

![显示 MCAS 的数据连接器页面的屏幕截图，其中选择了免费安全警报，但未选择付费的 MCASShadowITReporting。](media/billing/data-types.png)

## <a name="estimate-azure-sentinel-costs"></a>估算 Azure Sentinel 成本

如果尚未使用 Azure Sentinel，则可以使用 [Azure Sentinel 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel)来估计使用 Azure Sentinel 可能产生的成本。 在搜索框中输入“Azure Sentinel”，并选择生成的 Azure Sentinel 磁贴。 定价计算器可帮助你根据预期的数据引入和保留期来估算可能的成本。

例如，可以输入预计每天要在 Azure Sentinel 中引入的数据量，以及工作区的区域。 计算器提供跨这些组件的每月总成本：

- Log Analytics 数据引入 
- Azure Sentinel 数据分析 
- Log Analytics 数据保留 

## <a name="manage-azure-sentinel-costs"></a>管理 Azure Sentinel 成本

可以通过多种方法了解和管理 Azure Sentinel 使用情况和成本。

管理数据引入和保留：

- [使用承诺层级定价优化成本](#set-or-change-pricing-tier)（基于你的数据引入量）。
- [定义 Log Analytics 数据量上限](#define-a-data-volume-cap-in-log-analytics)以管理数据引入，即使安全数据被排除在外。
- [使用专用群集优化 Log Analytics 成本](#optimize-log-analytics-costs-with-dedicated-clusters)。
- [将非安全数据分隔到不同的工作区中](#separate-non-security-data-in-a-different-workspace)。
- [使用 Azure 数据资源管理器 (ADX) 降低长期数据保留成本](#reduce-long-term-data-retention-costs-with-adx).

了解、监视和提醒数据引入和成本变化：

- [运行查询来了解数据引入](#run-queries-to-understand-your-data-ingestion)。
- [部署工作簿以直观显示数据引入](#deploy-a-workbook-to-visualize-data-ingestion)。
- [使用成本管理工作簿](#use-a-playbook-for-cost-management-alerts)，当引入量超过了预定义的阈值时，该工作簿会发送警报。
- [了解通用事件格式 (CEF) 数据引入](#understand-cef-ingestion-volume)。

### <a name="manage-data-ingestion-and-retention"></a>管理数据引入和保留

使用以下方法来管理 Azure Sentinel 工作区的数据引入和保留。

#### <a name="set-or-change-pricing-tier"></a>设置或更改定价层级

要进行优化以实现最大程度的节省，需要监视你的引入量，以确保你的承诺层级最贴近你的引入量模式。 可以增加或减少您的承诺层，以贴近不断变化的数据量。

可以随时增大的承诺层级，这将重新开始 31 天的承诺期。 但是，要切换回到即用即付或更低的承诺层级，必须等到 31 天承诺期结束。 承诺层级的计费是按天计费。

要查看当前的 Azure Sentinel 定价层级，请在 Azure Sentinel 左侧导航中选择“设置”，然后选择“定价”选项卡。您的当前定价层标记为“当前层”。  

若要更改定价层级承诺，请在定价页上选择一个其他层，然后选择“应用”。 必须在 Azure Sentinel 中具有“参与者”或“所有者”角色才能更改定价层级。

![显示 Azure Sentinel 设置中“定价”页面的屏幕截图，其中的“即用即付”指示为当前定价层级。](media/billing/pricing.png)

> [!NOTE]
> 在某些门户使用量图表中，Azure Sentinel 数据引入量显示在“安全见解”下面。

Azure Sentinel 定价层级不包括 Log Analytics 费用。 要更改 Log Analytics 的定价层级承诺，请参阅[更改定价层级](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier)。

#### <a name="define-a-data-volume-cap-in-log-analytics"></a>在 Log Analytics 中定义数据量上限

在 Log Analytics 中，可以启用每日数据量上限，这将限制你的工作区的每日引入量。 每日上限可帮助你管理数据量的意外增加，保持在限制范围内，并限制意外费用。

要定义每日引入量上限，请在 Log Analytics 工作区的左侧导航中选择“使用量和估计成本”，然后选择“每日上限”。  选择“开启”，输入每日数据量上限，然后选择“确定”。 

![显示“使用量和估计成本”屏幕和“每日上限”窗口的屏幕截图。](media/billing/daily-cap.png)

“使用量和估计成本”屏幕还显示过去 31 天的引入数据量趋势和总共保留的数据量。

> [!IMPORTANT]
> 每日上限不会限制所有数据类型的集合。 有关在 Log Analytics 中管理每日上限的详细信息，请参阅[管理每日最大数据量](../azure-monitor/logs/manage-cost-storage.md#manage-your-maximum-daily-data-volume)。

#### <a name="optimize-log-analytics-costs-with-dedicated-clusters"></a>使用专用群集优化 Log Analytics 成本

如果在同一区域中的 Azure Sentinel 工作区或工作区中每天至少引入 1TB 的数据量，请考虑切换到 Log Analytics 专用群集以降低成本。 Log Analytics 专用群集承诺层级将聚合整个工作区的数据量，这些工作区每天总共引入了 1TB 数据量或更多。

Log Analytics 专用群集不适用于 Azure Sentinel 承诺层级。 对于专用群集中的每个工作区，Azure Sentinel 成本仍然适用。

可以将多个 Azure Sentinel 工作区添加到 Log Analytics 专用群集中。 对 Azure Sentinel 使用 Log Analytics 专用群集有多种优势，包括：

- 如果查询中涉及的所有工作区都在专用群集中，则跨工作区查询的运行速度更快。 在您的环境中，最好尽可能少地使用工作区，专用群集仍然[限制最多 100 个工作区](../azure-monitor/logs/cross-workspace-query.md)可以包含在单个跨工作区查询中。

- 专用群集中的所有工作区都可以共享群集上设置的 Log Analytics 承诺层级。 不必为每个工作区单独承诺 Log Analytics 承诺层级，这样可以节省成本，同时提高效率。 如果启用专用群集，你要承诺的最低 Log Analytics 承诺层级是每天 1TB 引入。

下面是迁移到专用群集以实现成本优化的一些其他注意事项：

- 每个区域和每个订阅的群集的最大数目为 2。
- 链接到群集的所有工作区必须位于同一区域中。
- 链接到一个群集的最大工作区数量为 1000。
- 可以取消某个已链接工作区与群集的链接。 在 30 天内，与特定工作区的链接操作数限制为 2。
- 不能将现有工作区移动到客户托管的密钥 (CMK) 群集。 需要在群集中创建工作区。
- 当前不支持将群集移到另一个资源组或订阅。
- 如果工作区已链接到另一个群集，则指向群集的工作区链接将失败。

有关专用群集的详细信息，请参阅 [Log Analytics 专用群集](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters)。

#### <a name="separate-non-security-data-in-a-different-workspace"></a>将非安全数据分隔到不同的工作区中

Azure Sentinel 会将所有数据引入分析到启用了 Azure Sentinel 的 Log Analytics 工作区。 最好为非安全操作数据使用单独的工作区，以确保不会产生 Azure Sentinel 成本。

在 Azure Sentinel 中搜寻或调查威胁时，可能需要访问存储在这些独立 Azure Log Analytics 工作区中的操作数据。 可以通过在日志探索体验和工作簿中使用跨工作区查询来访问这些数据。 但是，除非对所有工作区启用了 Azure Sentinel，否则不能使用跨工作区分析规则和搜寻查询。

#### <a name="reduce-long-term-data-retention-costs-with-adx"></a>通过 ADX 降低长期数据保留成本

Azure Sentinel 数据保留期前 90 天免费。 要调整 Log Analytics 中的数据保留时间段，可在左侧导航中选择“使用量和估计成本”，然后选择“数据保留”，然后调整滑块。 

在几个月后，Azure Sentinel 安全数据可能会丢失其中的某些值。 安全运营中心 (SOC) 用户可能无需像更新的数据一样频繁访问较旧的数据，但在偶尔调查或审核时可能仍需要访问数据。 为了降低 Azure Sentinel 数据保留成本，你可以使用 Azure 数据资源管理器以较低的成本实现长期数据保留。 对于不再需要 Azure Sentinel 安全情报的陈旧数据，ADX 提供了成本与使用量之间的最佳平衡。

借助 ADX，你可以以更低的价格存储数据，但仍可使用与 Azure Sentinel 中相同的 Kusto 查询语言 (KQL) 查询来浏览数据。 还可以使用 ADX 代理功能来执行跨平台查询。 这些查询将跨 ADX、Application Insights、Azure Sentinel 和 Log Analytics 聚合和关联数据。

有关详细信息，请参阅[集成 Azure 数据资源管理器以实现长期日志保留](store-logs-in-azure-data-explorer.md)。

### <a name="understand-monitor-and-alert-for-changes-in-data-ingestion-and-costs"></a>了解、监视和提醒数据引入和成本方面的变化

使用以下方法了解、监视和提醒 Azure Sentinel 工作区中的变化。

#### <a name="run-queries-to-understand-your-data-ingestion"></a>运行查询来了解数据引入

可以使用以下查询来了解数据引入量。

运行以下查询，按解决方案显示数据引入量： 

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| render columnchart
```

运行以下查询以按数据类型显示数据引入量： 

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType
| render columnchart
```
运行以下查询以同时按解决方案和数据类型显示数据引入量： 

```kusto
Usage
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| sort by Solution asc, DataType asc
```

#### <a name="deploy-a-workbook-to-visualize-data-ingestion"></a>部署工作簿以直观显示数据引入

“工作区使用量报告”工作簿提供工作区的数据消耗、成本和使用量统计信息。 此工作簿提供了工作区的数据引入状态以及免费和付费数据的数量。 可以使用该工作簿的逻辑来监视数据引入和成本，并生成自定义视图和基于规则的警报。

此工作簿还提供了不同颗粒度的引入详情。 工作簿用数据表将工作区中的数据分解，并提供表和条目提供使用量，以帮助你更好地理解你的引入模式。

启用“工作区使用量报告”工作簿：

1. 在 Azure Sentinel 的左侧导航中，选择“威胁管理” > “工作簿”。
1. 在搜索栏中输入“工作区量”，然后选择“工作区使用量报告”。
1. 选择“查看模板”以按原样使用工作簿，或选择“保存”以创建工作簿的可编辑副本。 如果保存了副本，请选择“查看保存的工作簿”。
1. 在工作簿中，选择要查看的“订阅”和“工作区”，然后将“时间范围”设置为要查看的时间范围。   可以将“显示帮助”开关设置为“是”以在工作簿中就进显示说明。 

#### <a name="use-a-playbook-for-cost-management-alerts"></a>使用 playbook 发出成本管理警报

为了帮助你控制 Azure Sentinel 预算，你可以创建成本管理 playbook。 如果 Azure Sentinel 工作区在给定的时间范围内超出了你定义的预算，playbook 将向你发送警报。

GitHub Azure Sentinel GitHub 社区在 GitHub 中提供了 [Send-IngestionCostAlert](https://github.com/iwafula025/Azure-Sentinel/tree/master/Playbooks/Send-IngestionCostAlert) 成本管理 playbook。 此 playbook 由定期触发器激活，可为你提供高程度的灵活性。 你可以根据要求控制执行频率、引入量以及要触发的消息。

#### <a name="understand-cef-ingestion-volume"></a>了解 CEF 引入量

Azure Sentinel 中支持以 CEF 作为 Syslog 事件格式。 可以使用 CEF 将各种源中有价值的安全信息引入 Azure Sentinel 工作区中。 CEF 日志在 Azure Sentinel 中的 CommonSecurityLog 表，其中包含所有标准最新 CEF 字段。

许多设备和数据源允许记录标准 CEF 架构以外的字段。 这些附加字段将记录在 AdditionalExtensions 表中。 这些字段的引入量可能高于标准 CEF 字段，因为这些字段中的事件内容可能是变化的。

## <a name="understand-your-azure-sentinel-costs-and-bill"></a>了解 Azure Sentinel 成本和帐单

了解和跟踪成本 Azure Sentinel 很重要。 [Azure 成本管理 + 计费](../cost-management-billing/costs/quick-acm-cost-analysis.md)中心提供了有用的功能。 在 Azure 门户中打开“Azure 成本管理 + 计费”之后，在左侧导航中选择“成本管理”，然后选择要调查的资源[范围](..//cost-management-billing/costs/understand-work-scopes.md)或资源集，例如 Azure 订阅或资源组。 

要查看你的 Azure 账单，请在“成本管理 + 计费”的左侧导航中选择“成本分析”。  在“成本分析”屏幕上，选择“视图”字段中的下拉插入符号，然后选择“发票详细信息”。  

> [!NOTE]
> 此图所示的费用仅用作示例。 并非是为了反映实际成本。

![显示示例 Azure 票据的 Azure Sentinel 部分的屏幕截图。](media/billing/sample-bill.png)

Azure Sentinel 和 Log Analytics 费用将根据你选择的定价计划作为单独的行项出现在你的 Azure 账单中。 如果在某个给定月份中超过了工作区的承诺层级使用量，Azure 帐单会显示一个行项，对应承诺层级及其关联的固定成本，还有一个单独的行项，对应超出承诺层级的引入量（按相同的承诺层级费率计费）。

下表显示了 Azure Sentinel 和 Log Analytics 成本在 Azure 发票的“服务名称”和“计量”列中的显示方式： 

|开销|Service name|计量|
|----|------------|----------------|
|Azure Sentinel 承诺层级|**sentinel**|**`n` gb 承诺层级**|
|Log Analytics 承诺层级|azure monitor|**`n` gb 承诺层级**|
|超过承诺层级的 Azure Sentinel 的超额，或者即用即付|**sentinel**|分析|
|超过承诺层级的 Log Analytics 的超额，或者即用即付|log analytics|data ingestion|

有关查看和下载 Azure 帐单详细信息，请参阅 [Azure 成本和计费信息](../cost-management-billing/understand/download-azure-daily-usage.md)。

“成本分析”屏幕还显示 Azure 使用量和成本的详细视图，以及应用各种控件和筛选器的选项。

例如，查看特定时间范围的每日成本图表：
1. 在“视图”字段中选择下拉插入符号，然后选择“累计成本”或“每日成本”。  
1. 在"日期"字段中选择下拉型点，然后选择一个日期范围。
1. 选择“粒度”旁边的下拉插入符号并选择“每日”。 

> [!NOTE]
> 此图所示的费用仅用作示例。 并非是为了反映实际成本。

![显示“成本管理 + 计费成本分析”屏幕的屏幕截图。](media/billing/cost-management.png)

还可以应用其他控件。 例如，若要仅查看与 Azure Sentinel 相关联的成本，请选择“添加筛选器”，选择“服务名称”，然后选择服务名称“sentinel”、“log analytics”和“azure monitor”。    

## <a name="next-steps"></a>后续步骤
有关减少 Log Analytics 数据量的更多提示，请参阅[有关减少数据量的提示](../azure-monitor/logs/manage-cost-storage.md#tips-for-reducing-data-volume)。