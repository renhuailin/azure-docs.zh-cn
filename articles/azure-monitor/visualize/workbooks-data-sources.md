---
title: Azure Monitor 工作簿数据源 | Microsoft Docs
description: 使用从多个数据源预生成的以及自定义的参数化 Azure Monitor 工作簿简化复杂的报表
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: ee4436d2b8445f2a1d368fb6c76e04d1ec790d98
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608855"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor 工作簿数据源

工作簿与大量的数据源兼容。 本文详细介绍当前可用于 Azure Monitor 工作簿的数据源。

## <a name="logs"></a>日志

工作簿允许查询来自以下源的日志：

* Azure Monitor 日志（Application Insights 资源和 Log Analytics 工作区。）
* 以资源为中心的数据（活动日志）

工作簿作者可以使用用于转换基础资源数据的 KQL 查询，以选择可作为文本、图表或网格可视化的结果集。

![工作簿日志报表界面的屏幕截图](./media/workbooks-data-sources/logs.png)

工作簿作者可以轻松查询多个资源，以创建真正统一的丰富报表体验。

## <a name="metrics"></a>指标

Azure 资源发出可以通过工作簿访问的[指标](../platform/data-platform-metrics.md)。 可以在工作簿中通过一个专用控件访问指标，该控件可让你指定目标资源、所需指标和聚合。 然后，可以在图表或网格中绘制这些数据。

![CPU 利用率工作簿指标图表的屏幕截图](./media/workbooks-data-sources/metrics-graph.png)

![工作簿指标界面的屏幕截图](./media/workbooks-data-sources/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

工作簿支持使用 Azure Resource Graph (ARG) 查询资源及其元数据。 此功能主要用于生成报表的自定义查询范围。 资源范围通过 ARG 支持的 KQL 子集来表示，这通常足以满足常见的用例。

若要让查询控件使用此数据源，请使用“查询类型”下拉列表选择“Azure Resource Graph”，然后选择目标订阅。 使用“查询”控件添加选择有意义资源子集的 ARG KQL 子集。

![Azure Resource Graph KQL 查询的屏幕截图](./media/workbooks-data-sources/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

工作簿支持 Azure 资源管理器 REST 操作。 这样，便可以查询 management.azure.com 终结点，而无需提供自己的授权标头令牌。

若要让查询控件使用此数据源，请使用“数据源”下拉列表选择“Azure 资源管理器”。 提供适当的参数，例如 Http 方法、URL 路径、标头、URL 参数和/或正文。

> [!NOTE]
> 目前仅支持 `GET`、`POST` 和 `HEAD` 操作。

## <a name="azure-data-explorer"></a>Azure 数据资源管理器

工作簿现在支持使用强大的 [Kusto](/azure/kusto/query/index) 查询语言从 [Azure 数据资源管理器](/azure/data-explorer/)群集进行查询。

![Kusto 查询窗口的屏幕截图](./media/workbooks-data-sources/data-explorer.png)

## <a name="workload-health"></a>工作负载运行状况

Azure Monitor 提供主动监视 Windows 或 Linux 来宾操作系统可用性和性能的功能。 Azure Monitor 为关键组件及其关系、有关如何度量这些组件的运行状况的条件，以及在检测到不正常情况时由哪些组件会发出警报的设置建模。 工作簿允许用户使用这些信息创建丰富的交互式报表。

若要让查询控件使用此数据源，请使用“查询类型”下拉列表选择“工作负荷运行状况”，然后选择目标订阅、资源组或 VM 资源。 使用运行状况筛选器下拉列表，根据分析需求选择有意义的运行状况事件子集。

![警报查询的屏幕截图](./media/workbooks-data-sources/workload-health.png)

## <a name="azure-resource-health"></a>Azure 资源运行状况

工作簿支持获取 Azure 资源运行状况，并将其与其他数据源合并，以创建丰富的交互式运行状况报告

若要让查询控件使用此数据源，请使用“查询类型”下拉列表选择“Azure 运行状况”，然后选择目标资源。 使用运行状况筛选器下拉列表，根据分析需求选择有意义的资源问题子集。

![显示运行状况筛选器列表的警报查询的屏幕截图。](./media/workbooks-data-sources/resource-health.png)

## <a name="change-analysis-preview"></a>更改分析 (预览) 

若要使用 [应用程序更改分析](../app/change-analysis.md) 作为数据源，请使用 " *数据源* " 下拉箭头并选择 " *更改分析 (预览)* 并选择单个资源。 最多可以显示最后14天的更改。 *级别* 下拉菜单可用于在 "重要"、"正常" 和 "干扰" 更改之间进行筛选，此下拉项支持类型为 [下拉](workbooks-dropdowns.md)的工作簿参数。

> [!div class="mx-imgBorder"]
> ![包含更改分析的工作簿的屏幕截图](./media/workbooks-data-sources/change-analysis-data-source.png)

## <a name="merge-data-from-different-sources"></a>合并来自不同源的数据

通常需要将不同源中的数据集中在一起，以增强见解体验。 例如，使用相关指标数据来补充活动的警报信息。 这样，用户不仅可以查看活动警报)  (的效果，还可以查看潜在的原因 (例如，CPU 使用率) 高。 监视域具有许多此类相关数据源，这些数据源对会审和诊断工作流通常很重要。

工作簿不仅允许查询不同的数据源，而且还提供了简单的控件，使你能够合并或联接数据以提供丰富的见解。 `merge`控件是实现此目的的方式。

以下示例结合了警报数据和 log analytics VM 性能数据，以获取丰富的见解网格。

> [!div class="mx-imgBorder"]
> ![工作簿的屏幕截图，其中包含合并警报和 log analytics 数据的合并控件](./media/workbooks-data-sources/merge-control.png)

工作簿支持多种合并：

* 内部唯一联接
* 完全内部联接
* 完全外联
* 左外部联接
* 右外部联接
* 左半部联接
* 右半部联接
* 左反联接
* 右反联接
* Union
* 复制表

## <a name="json"></a>JSON

使用 JSON 提供程序可从静态 JSON 内容创建查询结果。 它最常用于在参数中创建静态值的下拉列表参数。 简单的 JSON 数组或对象将自动转换为网格行和列。  有关更具体的行为，可使用“结果”选项卡和 JSONPath 设置来配置列。

此访问接口支持 [JSONPath](workbooks-jsonpath.md)。

## <a name="alerts-preview"></a>警报（预览版）

> [!NOTE]
> 建议使用 [Azure Resource Graph](#azure-resource-graph) 数据源通过查询 `AlertsManagementResources` 表来查询 Azure 警报信息。
>
> 有关示例，请参阅 [Azure Resource Graph 表格引用](../../governance/resource-graph/reference/supported-tables-resources.md)或[警报模板](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook)。
>
> 作者转换为使用 ARG 时，警报数据源将在一段时间内保持可用。 不建议在模板中使用此数据源。 

工作簿允许用户可视化与其资源相关的活动警报。 限制：警报数据源需要对订阅的读取访问权限才能查询资源，并且可能不会显示较新的警报类型。 

若要使查询控件使用此数据源，请使用 " _数据源_ " 下拉箭头选择 _警报 (预览)_ 并选择要面向的订阅、资源组或资源。 使用警报筛选器下拉列表根据分析需求选择一个所关注的警报子集。

## <a name="custom-endpoint"></a>自定义终结点

工作簿支持从任何外部源获取数据。 如果数据位于 Azure 外部，可以使用此数据源类型将数据引入工作簿。

若要让查询控件使用此数据源，请使用“数据源”下拉列表选择“自定义终结点” 。 提供适当的参数，如 `Http method`、`url`、`headers`、`url parameters` 和/或 `body`。 请确保数据源支持 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)，否则请求将会失败。

若要避免在使用模板时自动调用不受信任的主机，用户需要将使用的主机标记为受信任。 为此，可以单击“添加为受信任”按钮，或在工作簿设置中将其添加为受信任的主机。 这些设置将保存在 [支持与 web 工作进程 IndexDb 的浏览器](https://caniuse.com/#feat=indexeddb)中。

> [!NOTE]
> 请勿在任何字段（`headers`、`parameters`、`body`、`url`）中写入任何机密，因为这些字段对所有工作簿用户均可见。

此访问接口支持 [JSONPath](workbooks-jsonpath.md)。

## <a name="next-steps"></a>后续步骤

* [开始](../platform/workbooks-overview.md#visualizations)详细了解工作簿丰富的可视化效果选项。
* [控制](../platform/workbooks-access-control.md)并共享对工作簿资源的访问权限。
* [Log Analytics 查询优化提示](../log-query/query-optimization.md)