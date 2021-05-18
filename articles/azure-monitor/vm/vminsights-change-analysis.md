---
title: VM 见解中的更改分析
description: 通过 VM 见解与应用程序更改分析的集成，你可以查看对虚拟机所做的任何可能影响其性能的更改。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 375812813d704eb9b48d0ed8fbbc65dd5e47da49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046765"
---
# <a name="change-analysis-in-vm-insights"></a>VM 见解中的更改分析
通过 VM 见解与[应用程序更改分析](../app/change-analysis.md)的集成，你可以查看对虚拟机所做的任何可能影响其性能的更改。

## <a name="overview"></a>概述
假设你有一个运行缓慢的 VM，你希望调查最近对其配置所做的更改是否影响到其性能。 你使用 VM 见解来查看 VM 的性能，并发现过去一小时的内存使用量增加。 更改分析有助于确定是否因为这段时间所做的任何配置更改导致了这种增加。

应用程序更改分析服务聚合了 [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md) 中的更改以及嵌套的属性更改，例如来自 Azure 资源管理器的网络安全规则。 

## <a name="enabling-change-analysis"></a>启用更改分析
若要将更改分析加入到 VM 见解中，必须注册 Microsoft.ChangeAnalysis 资源提供程序。 首次在 Azure 门户中启动 VM 见解或应用程序更改分析时，系统将自动注册此资源提供程序。 应用程序更改分析是一项免费的服务，在资源上不会产生任何性能开销。

## <a name="view-change-analysis"></a>查看更改分析
通过选择“更改”选项，可从 VM 见解的“性能”或“映射”选项卡中获取更改分析  。 

[![调查更改](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


单击“调查更改”按钮，可启动为 VM 筛选的“应用程序更改分析”页。 你可以检查列出的更改，查看是否有任何更改可能导致此问题。 如果对某个特定的更改不太确定，可引用“更改者”列来确定执行更改的人员。

[![更改详细信息](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>后续步骤
- 详细了解[应用程序更改分析](../app/change-analysis.md)。
- 详细了解 [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md)。 

