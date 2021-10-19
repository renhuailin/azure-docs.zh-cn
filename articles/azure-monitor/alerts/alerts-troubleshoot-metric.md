---
title: 排查 Azure 指标警报问题
description: Azure Monitor 指标警报的常见问题和可能的解决方案。
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 09/30/2021
ms.openlocfilehash: 6b093eeda754d288030e6ff3f1739a5c68c659c1
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615593"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>排查 Azure Monitor 指标警报的问题 

本文介绍了 Azure Monitor [指标警报](alerts-metric-overview.md)的常见问题，以及如何排查这些问题。

在监视数据中发现重要情况时，Azure Monitor 警报会主动通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 有关警报的详细信息，请参阅 [Microsoft Azure 中的警报概述](./alerts-overview.md)。

## <a name="metric-alert-should-have-fired-but-didnt"></a>指标警报应当已触发但未触发 

如果你认为某个指标警报应当已触发但未触发且在 Azure 门户中找不到该警报，则请尝试执行以下步骤：

1. **配置** - 检查指标警报规则配置以确保它正确配置：
    - 检查是否按预期配置了“聚合类型”和“聚合粒度(周期)” 。 “聚合类型”可确定指标值的聚合方式（详情请参见[此处](../essentials/metrics-aggregation-explained.md#aggregation-types)），“聚合粒度(周期)”可控制每次运行警报规则时，评估聚合度量值的时间间隔 。
    -  检查“阈值”或“敏感度”是否按预期方式配置 。
    - 对于使用“动态阈值”的警报规则，请检查是否配置了高级设置，因为“冲突的数量”可能会筛选警报，而“忽略之前的数据”会影响阈值的计算方式 。

       > [!NOTE] 
       > 动态阈值在变为活动状态之前至少需要 3 天和 30 个指标示例。

2. **已触发但没有通知** - 复查 [触发的警报列表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)，看是否可以找到触发的警报。 如果可以在列表中看到该警报，但其部分操作或通知存在问题，请在[此处](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)了解更多信息。

3. **已处于活动状态** - 检查你预计会收到警报的指标时序是否已存在触发的警报。 指标警报是有状态的，即，一旦在特定的指标时序中触发某个警报，就不会触发该时序中的其他警报，直到相应的问题不再出现。 此设计选择减少了干扰。 当连续三次评估不满足警报条件时，警报会自动解决。

4. **使用的维度** - 如果选择了一些 [针对某个指标的维度值](./alerts-metric-overview.md#using-dimensions)，则警报规则会监视各个指标时序（通过将维度值组合在一起来定义）中是否存在超出阈值的情况。 如果还要监视聚合指标时序（不选择任何维度），请在该指标上配置附加警报规则而不选择维度。

5. **聚合和时间粒度** - 如果要使用 [指标图表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)来将指标可视化，请确保：
    * 指标图表中选择的“聚合”与警报规则中的“聚合类型”相同 
    * 所选的“时间粒度”与警报规则中的“聚合粒度(周期)”相同，且未设置为“自动” 

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>指标警报在不应当触发时触发

如果你认为指标警报不应当触发但却触发，则可通过以下步骤来解决问题。

1. 查看[触发的警报列表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2)，找到触发的警报，然后单击以查看其详细信息。 查看“为何会触发此警报?”下提供的信息，以了解触发警报时的指标图表、“指标值”和“阈值”  。

    > [!NOTE] 
    > 如果你使用动态阈值条件类型，并且认为使用的阈值不正确，请使用哭脸图标提供反馈。 此反馈会影响机器学习算法研究，有助于改进未来的检测。

2. 如果为某个指标选择了多个维度值，则当任何指标时序（通过将维度值组合在一起来定义）超出阈值时，都会触发警报。 有关在指标警报中使用维度的详细信息， [请参阅](./alerts-metric-overview.md#using-dimensions)。

3. 检查警报规则配置以确保它已正确配置：
    - 检查是否按预期配置了“聚合类型”、“聚合粒度(周期)”和“阈值”或“敏感度”   
    - 对于使用“动态阈值”的警报规则，请检查是否配置了高级设置，因为“冲突的数量”可能会筛选警报，而“忽略之前的数据”会影响阈值的计算方式 

   > [!NOTE]
   > 动态阈值在变为活动状态之前至少需要 3 天和 30 个指标示例。

4. 如果使用[指标图表](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)将指标可视化，请确保：
    - 指标图表中选择的“聚合”与警报规则中的“聚合类型”相同 
    - 所选的“时间粒度”与警报规则中的“聚合粒度(周期)”相同，且未设置为“自动” 

5. 如果警报被触发，而同时已有监视相同条件的已触发的警报（尚未解决），请检查警报规则是否已配置为不自动解决警报。 这样的配置会导致警报规则变为无状态，意味着警报规则不会自动解决触发的警报，并且不要求已触发的警报在同一时序上再次触发之前先行得到解决。
    可以通过以下方法之一来检查是否已将警报规则配置为不自动解决：
    - 在 Azure 门户中编辑警报规则，并查看是否未选中“自动解决警报”复选框（在“警报规则详细信息”部分）。
    - 查看用于部署警报规则的脚本，或检索警报规则定义并检查 autoMitigate 属性是否设置为 false。


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>找不到警报所针对的指标 - 虚拟机来宾指标

若要对虚拟机的来宾操作系统指标发出警报（例如：内存、磁盘空间），请确保已安装所需的代理将此数据收集到 Azure Monitor 指标：
- [对于 Windows VM](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [对于 Linux VM](../essentials/collect-custom-metrics-linux-telegraf.md)

有关从虚拟机的来宾操作系统收集数据的详细信息，请参阅[此处](../vm/monitor-vm-azure.md#guest-operating-system)。

> [!NOTE] 
> 如果将来宾指标配置为发送到 Log Analytics 工作区中，则这些指标将显示在 Log Analytics 工作区资源下，并且只会在创建用于监视数据的警报规则后才开始显示数据。 为此，按照步骤[配置日志的指标警报](./alerts-metric-logs.md#configuring-metric-alert-for-logs)。

> [!NOTE] 
> 指标警报当前不支持用单个警报规则监视多个虚拟机的来宾指标。 可以使用[日志警报规则](./alerts-unified-log.md)来实现此目的。 为此，请确保将来宾指标收集到 Log Analytics 工作区，并在该工作区上创建日志警报规则。

## <a name="cant-find-the-metric-to-alert-on"></a>找不到警报所针对的指标

如果要对特定指标发出警报，但创建警报规则时看不到该指标，请检查以下内容：
- 如果看不到资源的任何指标，请[检查指标警报是否支持该资源类型](./alerts-metric-near-real-time.md)。
- 如果可以看到资源的某些指标，但找不到特定指标，请[检查该指标是否可用](../essentials/metrics-supported.md)；如果可用，请查看指标说明，检查它是否仅适用于特定版本的资源。
- 如果该指标不适用于资源，则可能会在资源日志中提供，并且可使用日志警报来监视它。 请参阅此处，详细了解如何[从 Azure 资源收集资源日志并进行分析](../essentials/tutorial-resource-logs.md)。

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>找不到警报所针对的指标维度

如果希望对[指标的特定维度值](./alerts-metric-overview.md#using-dimensions)发出警报，但找不到这些值，请注意以下事项：

1. 维度值可能需要几分钟时间才能显示在“维度值”列表下
2. 显示的维度值基于在前一天收集到的指标数据
3. 如果维度值尚未发出或未显示，可以使用“添加自定义值”选项添加自定义维度值
4. 如果要对某个维度所有的可能值（包括将来的值）发出警报，请选中“选择所有当前和将来的值”选项
5. 默认情况下，Application Insights 资源的自定义指标维度处于关闭状态。 若要打开这些自定义指标的维度集合，请参阅[此处](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)。

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>在已删除资源上仍然会定义指标警报规则 

删除 Azure 资源时，不会自动删除关联的指标预警规则。 若要删除与已删除的资源关联的警报规则，请执行以下操作：

1. 打开在其中定义了删除的资源的资源组
1. 在显示资源的列表中，选中“显示隐藏的类型”复选框
1. 按类型 == microsoft.insights/metricalerts 筛选列表
1. 选择相关的警报规则，并选择“删除”

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>使指标警报在每次满足条件时都出现

默认情况下，指标警报是有状态的，因此，如果给定的时序已存在触发的警报，则不会触发其他警报。 如果需要使某个特定指标警报规则无状态，并在评估结果为满足警报条件的情况下收到警报，请按下述选项之一操作：
- 如果要以编程方式创建警报规则（例如，通过[资源管理器](./alerts-metric-create-templates.md)、[PowerShell](/powershell/module/az.monitor/)、[REST](/rest/api/monitor/metricalerts/createorupdate)、[CLI](/cli/azure/monitor/metrics/alert) 来这样做），请将 autoMitigate 属性设置为“False”。
- 如果要通过 Azure 门户创建警报规则，请取消选中“自动解决警报”选项（在“警报规则详细信息”部分）。

> [!NOTE] 
> 将指标警报规则设为无状态会妨碍已触发警报的解决，因此即使在不再满足条件后，触发的警报也会在 30 天的保留期内保持已触发状态。

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>在尚未发出的自定义指标上定义警报规则

创建指标警报规则时，将根据[指标定义 API](/rest/api/monitor/metricdefinitions/list) 验证指标名称，以确保其存在。 在某些情况下，你甚至希望在自定义指标发出之前就创建警报规则。 例如，在创建（使用资源管理器模板）将发出自定义指标的 Application Insights 资源及其用于监视该指标的警报规则时。

若要避免在尝试验证自定义指标的定义时部署失败，可以在警报规则的“条件”部分使用 skipMetricValidation 参数，这将导致跳过指标验证。 请参阅下面的示例，了解如何在资源管理器模板中使用此参数。 有关详细信息，请参阅[创建指标警报规则的完整资源管理器模板示例](./alerts-metric-create-templates.md)。

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```
> [!NOTE] 
> 对尚未在几天以上的时间内发出的现有自定义指标定义警报规则时，还可能需要使用 skipMetricValidation 参数。

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>通过 Azure 门户导出指标警报规则的 Azure 资源管理器模板

导出指标警报规则的资源管理器模板有助于了解其 JSON 语法和属性，并可用于自动执行后续部署。
1. 在 Azure 门户中，打开警报规则可查看其详细信息。
2. 单击 **“属性”**。
3. 在“自动化”下，选择“导出模板”。

## <a name="metric-alert-rules-quota-too-small"></a>指标警报规则配额太小

每个订阅允许的指标警报规则数目受制于[配额限制](../service-limits.md)。

如果已达到配额限制，请执行以下步骤以帮助解决此问题：
1. 尝试删除或禁用不再使用的指标警报规则。

2. 切换到使用监视多个资源的指标预警规则。 通过此功能，一个警报规则可以监视多个资源，且只会将一个警报规则计入配额。 要详细了解此功能和支持的资源类型，请参阅[此处](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)。

3. 如果需要提高配额限制，请创建支持请求，并提供以下信息：

    - 需要提高配额限制的订阅 ID
    - 增大配额的资源类型：“指标警报”或“指标警报(经典)” 
    - 请求的配额限制

## <a name="check-total-number-of-metric-alert-rules"></a>检查指标警报规则的总数

若要检查指标警报规则的当前使用情况，请执行以下步骤。

### <a name="from-the-azure-portal"></a>通过 Azure 门户

1. 打开“警报”屏幕，然后单击“管理预警规则” 
2. 使用“订阅”下拉列表控件筛选到相关订阅
3. 请勿筛选到特定的资源组、资源类型或资源
4. 在“信号类型”下拉列表控件中，选择“指标” 
5. 验证“状态”下拉列表控件是否设置为“已启用” 
6. 指标警报规则总数将显示在警报规则列表上方

### <a name="from-api"></a>通过 API

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST API - [按订阅列出](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>使用资源管理器模板、REST API、PowerShell 或 Azure CLI 管理警报规则

如果使用资源管理器模板、REST API、PowerShell 或 Azure 命令行界面 (CLI) 创建、更新、检索或删除指标警报时遇到问题，则以下步骤可能有助于解决问题。

### <a name="resource-manager-templates"></a>Resource Manager 模板

- 查看[常见 Azure 部署错误](../../azure-resource-manager/templates/common-deployment-errors.md)列表并相应地进行故障排除
- 请参阅[指标警报 Azure 资源管理器模板示例](./alerts-metric-create-templates.md)，以确保正确传递所有参数

### <a name="rest-api"></a>REST API

查看 [REST API 指南](/rest/api/monitor/metricalerts/)来验证是否正确传递了所有参数

### <a name="powershell"></a>PowerShell

确保对指标警报使用正确的 PowerShell cmdlet 命令：

- [Az.Monitor 模块](/powershell/module/az.monitor/) 中提供了用于指标警报的 PowerShell cmdlet
- 请确保对新（非经典）指标警报（例如 [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2)）使用以“V2”结尾的 cmdlet

### <a name="azure-cli"></a>Azure CLI

确保对指标警报使用正确的 CLI 命令：

- 适用于指标警报的 CLI 命令以 `az monitor metrics alert` 开头。 查看 [Azure CLI 参考](/cli/azure/monitor/metrics/alert)来了解语法。
- 你可以查看[展示了如何使用指标警报 CLI 的示例](./alerts-metric.md#with-azure-cli)
- 若要针对自定义指标发出警报，请确保使用相关指标命名空间为指标名称加上前缀：NAMESPACE.METRIC

### <a name="general"></a>常规

- 如果收到 `Metric not found` 错误：

   - 对于平台指标：请确保你使用的是来自[“Azure Monitor 支持的指标”页](../essentials/metrics-supported.md)的“指标名称”而不是“指标显示名称” 

   - 对于自定义指标：请确保已发出指标（不能对尚不存在的自定义指标创建警报规则），并且提供的是自定义指标的命名空间（请参阅[此处](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)的资源管理器模板示例）

- 如果要创建[关于日志的指标警报](./alerts-metric-logs.md)，请确保包括相应的依赖项。 参阅[示例模板](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs)。

- 如果要创建包含多个条件的警报规则，请注意以下限制：

   - 在每个条件内，只能为每个维度选择一个值
   - 不能使用“\*”作为维度值
   - 当以不同条件配置的指标支持相同维度时，则必须以相同方式为所有这些指标显式设置配置的维度值（请参阅[此处](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)的资源管理器模板示例）


## <a name="no-permissions-to-create-metric-alert-rules"></a>无权创建指标警报规则

若要创建指标警报规则，你需要有以下权限：

- 对警报规则的目标资源的读取权限
- 对在其中创建预警规则的资源组的写入权限（如果是从 Azure 门户中创建预警规则，则默认在目标资源所在的资源组中创建预警规则）
- 对关联到警报规则的任何操作组的读取权限（如果适用）


## <a name="naming-restrictions-for-metric-alert-rules"></a>指标警报规则的命名限制

请考虑对指标警报规则名称的以下限制：

- 指标警报规则名称在创建后无法更改（重命名）
- 指标警报规则名称在资源组中必须唯一
- 指标警报规则名称不能包含以下字符：* # & + : < > ? @ % { } \ / 
- 指标预警规则名称不能以空格或句点结尾
- 合并的资源组名称和警报规则名称不能超过 252 个字符

> [!NOTE] 
> 如果警报规则名称包含不是字母或数字的字符（例如：空格、标点符号或符号），则在某些客户端检索这些字符时，可能会对这些字符进行 URL 编码。

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>在具有多个条件的指标警报规则中使用维度时的限制

指标警报支持根据多维指标发出警报，并支持定义多个条件（每个警报规则最多可定义 5 个条件）。

在包含多个条件的警报规则中使用维度时，请考虑以下约束：
- 在每个条件中，只能为每个维度选择一个值。
- 不能使用选项“选择所有当前值和未来值”（选择 \*）。
- 如果在不同条件中配置的指标支持同一维度，则必须以相同方式为所有这些指标（在相关条件中）显式设置配置的维度值。
例如：
    - 请考虑在存储帐户上定义的一个指标警报规则，该警报规则监视两个条件：
        * **事务** 总数 > 5
        * 平均 **SuccessE2ELatency** > 250 毫秒
    - 我想更新第一个条件，并且仅监视 **ApiName** 维度等于“GetBlob”的事务
    - 由于“事务数”和 **SuccessE2ELatency** 指标都支持 **ApiName** 维度，所以我需要更新这两个条件，并将它们的 **ApiName** 维度都指定为“GetBlob”值。

## <a name="setting-the-alert-rules-period-and-frequency"></a>设置预警规则的周期和频率

建议选择大于评估频率的聚合粒度（周期），以降低在以下情况下错过对已添加的时序进行首次评估的可能性 ：
-   监视多个维度的指标警报规则 - 添加新维度值组合时
-   监视多个资源的指标警报规则 - 新资源添加到范围时
-   监视未连续发出的指标（稀疏指标）的指标警报规则 - 指标在超过 24 小时的时间段内未发出时

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>动态阈值边界似乎不适用于数据

如果指标的行为最近发生了更改，则这些更改不一定会立即反映在动态阈值边界（上限和下限）中，因为它们是基于过去 10 天的指标数据计算得出的。 查看给定指标的动态阈值边界时，请务必查看过去一周（而不仅仅是过去几个小时或几天）的指标趋势。

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>为什么动态阈值未检测到周周期性？

为了确定周周期性，动态阈值模型至少需要三周的历史数据。 有了足够的历史数据可用之后，就会识别指标数据中存在的任何周周期性，并相应调整该模型。 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>即使指标始终为正值，动态阈值也会显示该指标的负值下限

当指标出现较大波动时，动态阈值将围绕指标值生成更大的模型，这可能会导致下限值低于零。 具体来说，在以下情况下可能会发生这种情况：
1. 敏感度设置为低 
2. 中值接近零
3. 指标出现无规律的行为，且变化较大（数据中存在峰值或最小值）

当下限具有负值时，这意味着在指标出现无规律行为的情况下，指标可能会达到零值。 可以考虑选择更高的敏感度或更大的聚合粒度（时间范围），以降低模型的敏感度，或者使用“之前忽略数据”选项从用于构建模型的历史数据中排除最近的不规则行为 。

## <a name="the-dynamic-thresholds-alert-rule-is-too-noisy-fires-too-much"></a>动态阈值警报规则太嘈杂（触发太多）
若要降低动态阈值警报规则的敏感度，请使用以下选项之一：
1. 阈值敏感度 - 将敏感度设置为低，以便更能容忍偏差。
2. 违规次数（在高级设置下）- 将警报规则配置为仅在特定时间段内发生多次偏差时触发。 这将使规则不易受瞬态偏差的影响。


## <a name="the-dynamic-thresholds-alert-rule-is-too-insensitive-doesnt-fire"></a>动态阈值警报规则太不敏感（不触发）
有时，即使配置了高敏感度，警报规则也不会触发。 这通常发生在指标的分布非常不规则的情况下。
考虑下列选项之一：
* 转向监视适合你的场景的互补指标（如果适用）。 例如，检查成功率的变化，而不是失败率。
* 尝试选择不同的聚合粒度（周期）。 
* 检查过去 10 天内指标行为是否发生了重大变化（中断）。 突然的变化会影响为指标计算的上下限阈值，并使其更宽。 等待几天，直到中断不再计入阈值计算，或使用“忽略之前的数据”选项（在高级设置下） 。
* 如果数据具有每周周期性，但没有足够的历史记录可用于指标，则计算出的阈值可能会具有较宽的上限和下限。 例如，计算可以以相同的方式处理工作日和周末，并构建并不总是适合数据的宽边界。 一旦有了足够的指标历史记录，这个问题应会自行解决，此时将检测到正确的周期性，并且计算出的阈值将相应地更新。


## <a name="next-steps"></a>后续步骤

- 有关警报和通知的常规故障排除信息，请参阅[排查 Azure Monitor 警报中的问题](alerts-troubleshoot.md)。
