---
title: 应用程序更改分析的可视化效果 - Azure Monitor
description: 了解如何使用 Azure Monitor 中的应用程序更改分析的可视化效果。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 8319885de26bf79f5e402c4d06b29e9dd94894de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655832"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>应用程序更改分析的可视化效果（预览版）

## <a name="standalone-ui"></a>独立 UI

在 Azure Monitor 中，有一个更改分析独立窗格，用以查看对应用程序依赖项和资源的见解的所有更改。

在 Azure 门户的搜索栏中搜索“更改分析”以启动该体验。

![在 Azure 门户中搜索“更改分析”的屏幕截图](./media/change-analysis/search-change-analysis.png)

所选订阅下的所有资源都显示了过去24小时内的更改。 所有更改都显示为旧值和新值，以一目了然地提供见解。

![Azure 门户中“更改分析”边栏选项卡的屏幕截图](./media/change-analysis/change-analysis-standalone-blade.png)

单击一个更改，以查看完整资源管理器片段和其他属性。

![更改详细信息的屏幕截图](./media/change-analysis/change-details.png)

对于任何反馈，请使用发送反馈按钮或电子邮件 changeanalysisteam@microsoft.com。

![更改分析选项卡中反馈按钮的屏幕截图](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>多个订阅支持

UI 支持选择多个订阅以查看资源更改。 使用订阅筛选器：

![支持选择多个订阅的订阅筛选器的屏幕截图](./media/change-analysis/multiple-subscriptions-support.png)


## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>诊断并解决问题工具中的应用程序更改分析

应用程序更改分析是 Web 应用诊断并解决问题工具中的独立检测器。 它还聚合在“应用程序崩溃”和“Web 应用关闭检测器”中。  进入诊断并解决问题工具时，“Microsoft.ChangeAnalysis”资源提供程序将自动注册。 按照以下说明启用 web 应用来宾内更改跟踪。

1. 选择“可用性和性能”。

    ![“可用性和性能”故障排除选项的屏幕截图](./media/change-analysis/availability-and-performance.png)

2. 选择“应用程序更改”。 此功能也在“应用程序崩溃”中提供。

   ![“应用程序崩溃”按钮的屏幕截图](./media/change-analysis/application-changes.png)

3. 链接转到 web 应用范围的应用程序更改分析 UI。 如果未启用 web 应用来宾内更改跟踪，请遵循横幅以获取文件和应用设置更改。

   ![“应用程序崩溃”选项的屏幕截图](./media/change-analysis/enable-changeanalysis.png)

4. 启用“更改分析”并选择“保存”。 此工具显示应用服务计划下的所有 Web 应用。 可以使用计划级别开关，为某个计划下的所有 Web 应用启用更改分析。

    ![“启用更改分析”用户界面的屏幕截图](./media/change-analysis/change-analysis-on.png)

5. 还可以选择 **Web 应用关闭** 和 **应用程序崩溃** 检测器来更改数据。 此时会显示一个图形，其中汇总了在不同时间发生的更改类型，以及有关这些更改的详细信息。 默认情况下会显示过去 24 小时的更改，方便你解决即时问题。

     ![更改差异视图的屏幕截图](./media/change-analysis/change-view.png)

## <a name="diagnose-and-solve-problems-tool"></a>诊断并解决问题工具
更改分析可作为诊断并解决问题工具中的见解卡提供。 如果资源遇到问题，并在过去72小时内发现了更改，则见解卡将显示更改的数量。 单击查看更改详细信息链接将转到更改分析独立 UI 中的筛选视图。

![在诊断并解决问题工具中查看更改见解的屏幕截图。](./media/change-analysis/change-insight-diagnose-and-solve.png)



## <a name="virtual-machine-diagnose-and-solve-problems"></a>虚拟机诊断并解决问题

转到诊断并解决问题工具，查看虚拟机。  转到“故障排除工具”，向下浏览页面并选择“分析最近的更改”以查看虚拟机上的更改。 

![VM 诊断并解决问题的屏幕截图](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![故障排除工具中的更改分析器](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>活动日志更改历史记录

活动日志中的[查看更改历史记录](../essentials/activity-log.md#view-change-history)功能将调用应用程序更改分析服务后端，以获取与操作关联的更改。 “更改历史记录”用于直接调用 [Azure Resource Graph](../../governance/resource-graph/overview.md)，但交换后端来调用应用程序更改分析，因此返回的更改将包括来自[Azure Resource Graph](../../governance/resource-graph/overview.md)的资源级别更改、[Azure 资源管理器](../../azure-resource-manager/management/overview.md)的资源属性以及 PaaS 服务（如应用服务 web 应用）的来宾内更改。 为了使应用程序更改分析服务能够扫描用户订阅中的更改，需要注册资源提供程序。 第一次输入“更改历史记录”选项卡时，该工具将自动开始注册“Microsoft.ChangeAnalysis”资源提供程序。  注册后，将立即提供“Azure Resource Graph”的更改，并涵盖过去14天的时间。 其他源的更改将在订阅完成后大约4小时后提供。

![活动日志更改历史记录集成](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>VM Insights 集成

已启用 [VM Insights](../vm/vminsights-overview.md) 的用户可以查看其虚拟机中的什么更改可能导致指标图表（如 CPU 或内存）出现任何高峰。 更改数据集成在 VM Insights 侧边导航栏中。 用户可以查看 VM 中是否发生了任何更改，并选择“调查更改”以在应用程序更改分析独立 UI 中查看更改详细信息。

[![VM Insights 集成](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 了解如何[排查更改分析中的问题](change-analysis-troubleshoot.md)
