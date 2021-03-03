---
title: 应用程序更改分析的可视化效果-Azure Monitor
description: 了解如何在 Azure Monitor 中的应用程序更改分析中使用可视化效果。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 838a48aa11a1cb36c3a7d822ce88f58936aa976d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734615"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>应用程序更改分析 (预览的可视化) 

## <a name="standalone-ui"></a>独立 UI

在 Azure Monitor 中，有一个独立的窗格可供更改分析查看对应用程序依赖项和资源的见解的所有更改。

在 Azure 门户上的搜索栏中搜索 "更改分析" 以启动体验。

![在 Azure 门户中搜索“更改分析”的屏幕截图](./media/change-analysis/search-change-analysis.png)

所选订阅下的所有资源都显示在过去24小时内的更改中。 为了优化页面加载性能，服务一次显示10个资源。 选择下一页查看更多资源。 我们正在努力消除此限制。

![Azure 门户中“更改分析”边栏选项卡的屏幕截图](./media/change-analysis/change-analysis-standalone-blade.png)

单击资源即可查看其所有更改。 如果需要，请向下钻取以查看 json 格式的更改详细信息和见解。

![更改详细信息的屏幕截图](./media/change-analysis/change-details.png)

对于任何反馈，请使用 "发送反馈" 按钮或电子邮件 changeanalysisteam@microsoft.com 。

!["更改分析" 选项卡中 "反馈" 按钮的屏幕截图](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>支持多个订阅

UI 支持选择多个订阅以查看资源更改。 使用订阅筛选器：

![支持选择多个订阅的订阅筛选器的屏幕截图](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Web 应用诊断和解决问题

在 Azure Monitor 中，更改分析也已内置到自助式“诊断并解决问题”体验中。 可以从应用服务应用程序的“概述”页访问此体验。

![“概述”按钮和“诊断并解决问题”按钮的屏幕截图](./media/change-analysis/change-analysis.png)

## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>诊断和解决问题工具中的应用程序更改分析

应用程序更改分析是 Web 应用程序中的独立检测器，诊断并解决问题工具。 它还在 **应用程序崩溃** 和 **Web 应用关闭检测程序** 中进行聚合。 输入 "诊断并解决问题" 工具时，将自动注册 **ChangeAnalysis** 资源提供程序。 按照以下说明启用 web 应用来宾内更改跟踪。

1. 选择 " **可用性和性能**"。

    ![“可用性和性能”故障排除选项的屏幕截图](./media/change-analysis/availability-and-performance.png)

2. 选择“应用程序更改”。 此功能在 **应用程序崩溃** 中也可用。

   ![“应用程序崩溃”按钮的屏幕截图](./media/change-analysis/application-changes.png)

3. 链接导致应用程序更改分析 UI 的作用域为 web 应用。 如果未启用 "来宾内的 web 应用" 更改跟踪，请单击标题以获取文件和应用设置更改。

   ![“应用程序崩溃”选项的屏幕截图](./media/change-analysis/enable-changeanalysis.png)

4. 启用“更改分析”并选择“保存”。 该工具显示应用服务计划下的所有 web 应用。 可以使用计划级别开关，为某个计划下的所有 Web 应用启用更改分析。

    ![“启用更改分析”用户界面的屏幕截图](./media/change-analysis/change-analysis-on.png)

5. 更改数据还可在选择 **Web 应用关闭** 和 **应用程序崩溃** 检测程序中使用。 此时会显示一个图形，其中汇总了在不同时间发生的更改类型，以及有关这些更改的详细信息。 默认情况下会显示过去 24 小时的更改，方便你解决即时问题。

     ![更改差异视图的屏幕截图](./media/change-analysis/change-view.png)

## <a name="virtual-machine-diagnose-and-solve-problems"></a>虚拟机诊断和解决问题

请参阅诊断并解决虚拟机的问题工具。  转到 **故障排除工具**，浏览页面，并选择 " **分析最近的更改** " 以查看虚拟机上的更改。

![VM 的屏幕截图诊断和解决问题](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![故障排除工具中的更改分析器](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>活动日志更改历史记录

活动日志中的 " [查看更改历史记录](../essentials/activity-log.md#view-change-history) " 功能将调用应用程序更改分析服务后端，以获取与操作关联的更改。 用于直接调用 [Azure 资源关系图](../../governance/resource-graph/overview.md)的 **更改历史记录**，但已通过交换后端来调用应用程序更改分析，因此，返回的更改将包括来自 [azure 资源关系图](../../governance/resource-graph/overview.md)的资源级别更改、 [azure 资源管理器](../../azure-resource-manager/management/overview.md)的资源属性以及 PaaS 服务（如应用服务 web 应用）的来宾内更改。 为了使应用程序更改分析服务能够扫描用户订阅中的更改，需要注册资源提供程序。 第一次输入 " **更改历史记录** " 选项卡时，该工具将自动开始注册 **ChangeAnalysis** 资源提供程序。 注册后，将立即提供 **Azure 资源关系图** 的更改，并涵盖过去14天的时间。 在订阅完成后大约4小时后，其他源的更改将可用。

![活动日志更改历史记录集成](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>VM Insights 集成

已启用 [VM Insights](../vm/vminsights-overview.md) 的用户可以查看其虚拟机中的更改，这些更改可能会导致度量值图表（例如 CPU 或内存）出现任何高峰。 更改数据集成在 VM Insights 导航栏中。 用户可以查看 VM 中是否发生了任何更改，并选择 **调查更改** 以在应用程序更改分析独立 UI 中查看更改详细信息。

[![VM insights 集成](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 了解如何 [解决更改分析中的问题](change-analysis-troubleshoot.md)