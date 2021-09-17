---
title: 教程 - 使用 Azure Monitor VM 见解来监视混合计算机
description: 了解如何在 Azure Monitor 的混合计算机中收集和分析数据。
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: 8ab801885e86ed90d5f28c2ce90a994828b358a0
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772616"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-vm-insights"></a>教程：使用 VM 见解监视混合计算机

[Azure Monitor](../../../azure-monitor/overview.md) 可以直接从混合计算机将数据收集到 Log Analytics 工作区，以便进行详细分析和关联。 通常，这需要遵循配置管理标准，使用脚本、手动或自动方法在计算机上安装 [Log Analytics 代理](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent)。 已启用 Arc 的服务器最近引入了支持安装用于 Windows 和 Linux 的 Log Analytics 和依赖项代理 [VM 扩展](../manage-vm-extensions.md)，从而使 [VM 见解](../../../azure-monitor/vm/vminsights-overview.md)能够从非 Azure VM 收集数据。

本教程介绍如何通过简化的一组步骤启用 VM 见解来配置和收集 Linux 或 Windows 计算机中的数据，这可以简化体验并缩短时间。  

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* VM 扩展功能仅在[支持区域](../overview.md#supported-regions)的列表中提供。

* 查看[支持的操作系统](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems)，以确保 VM 见解支持正在启用的服务器操作系统。

* 查看 [Log Analytics 代理概述](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements)中提供的 Log Analytics 代理的防火墙要求。 VM 见解映射依赖关系代理本身不传输任何数据，它不需要对防火墙或端口做出任何更改。

## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com)。

## <a name="enable-vm-insights"></a>启用 VM 见解

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“计算机 - Azure Arc”，以启动 Azure Arc 服务 。

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="在“所有服务”中搜索启用了 Azure Arc 的服务器" border="false":::

1. 在“计算机 - Azure Arc”页上，选择在[快速入门](quick-enable-hybrid-vm.md)文章中创建的连接的计算机。

1. 从左窗格的“监视”部分下，选择“见解”，然后选择“启用”  。

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="从左侧菜单中选择“见解”选项" border="false":::

1. 在 Azure Monitor“见解载入”页上，系统会提示创建工作区。 对于本教程，在已有工作区的情况下，不建议选择现有 Log Analytics 工作区。 请选择默认值，即，已注册连接的计算机所在同一区域中具有唯一名称的工作区。 此工作区是为你创建和配置的。

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="“启用 VM 见解’页" border="false":::

1. 在执行配置时，会收到状态消息。 此过程需要几分钟时间，因为已连接的计算机上安装了扩展。

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="启用 VM insights 进度状态消息" border="false":::

    完成后，你会收到一条消息，表明计算机已成功安装且见解已成功部署。

## <a name="view-data-collected"></a>查看收集的数据

部署和配置完成后，选择“见解”，然后选择“性能”选项卡 。在“性能”选项卡上会显示从计算机的来宾操作系统收集的一组选定的性能计数器。 向下滚动查看更多计数器，并将鼠标移动到关系图上以查看从在计算机上安装 Log Analytics VM 扩展起采用的平均时间和百分位数。

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="所选计算机的 VM 见解性能图表" border="false":::

选择“映射”打开映射功能，其中显示了计算机上运行的进程及其依赖项。 选择“属性”打开属性窗格（如果尚未打开）。

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="所选计算机的 VM 见解映射" border="false":::

展开计算机的进程。 选择其中一个进程以查看其详细信息并突出显示其依赖项。

再次选择你的计算机，然后选择“日志事件”。 将会看到计算机的 Log Analytics 工作区中存储的表的列表。 此列表根据使用的是 Windows 还是 Linux 计算机而有所不同。 选择“事件”表。 “事件”表包含来自 Windows 事件日志的所有事件。 Log Analytics 将会打开，其中包含一个用于检索所收集事件日志条目的简单查询。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure Monitor 的详细信息，请参阅以下文章：

> [!div class="nextstepaction"]
> [Azure Monitor 概述](../../../azure-monitor/overview.md)