---
title: 在指标资源管理器中查看多个资源
description: 了解如何可视化 Azure Monitor 上的多个资源指标资源管理器
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 724809dbce3ca1b5a36f4da0ba5c03d0f78897f5
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577616"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>在指标资源管理器中查看多个资源

资源作用域选取器允许你查看同一订阅和区域内多个资源之间的指标。 下面是有关如何在 Azure Monitor 指标资源管理器中查看多个资源的说明。 

## <a name="selecting-a-resource"></a>选择资源 

从 Azure Monitor 菜单或从资源的菜单的“监视”部分选择“指标”    。 单击 "选择作用域" 按钮打开资源作用域选取器，这将允许您选择要查看其指标的资源 () 。 如果从资源菜单中打开指标资源管理器，应已填充此值。 

![以红色突出显示的资源范围选取器的屏幕截图](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>选择多个资源 

某些资源类型允许通过多个资源查询度量值，前提是它们在相同的订阅和位置中。 可以在 "资源类型" 下拉列表的顶部找到这些资源类型。 

![显示多资源的资源下拉菜单的屏幕截图 ](./media/metrics-charts/020.png)

> [!WARNING] 
> 你必须在订阅级别具有监视读者权限，才能可视化多个资源、资源组或订阅的指标。 为此，请按照 [本文档](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)中的说明进行操作。

若要可视化多个资源的指标，请首先在资源范围选取器中选择多个资源。 

![显示如何选择多个资源的屏幕截图](./media/metrics-charts/021.png)

> [!NOTE]
> 只能在同一资源类型、位置和订阅中选择多个资源。 不能选择此条件以外的资源。 

选择完成后，请单击 "应用" 按钮保存所做的选择。 

## <a name="selecting-a-resource-group-or-subscription"></a>选择资源组或订阅 

> [!WARNING]
> 你必须在订阅级别具有监视读者权限，才能可视化多个资源、资源组或订阅的指标。 

对于多资源兼容类型，您还可以在一个或多个资源组中查询度量值。 首先选择订阅或一个或多个资源组： 

![显示如何跨多个资源组进行查询的屏幕截图 ](./media/metrics-charts/022.png)

然后，你需要选择一个资源类型和位置，然后才能继续应用新的作用域。 

![显示选定资源组的屏幕截图 ](./media/metrics-charts/023.png)

你还可以展开所选作用域，验证将应用此域的资源。

![显示组中选定资源的屏幕截图 ](./media/metrics-charts/024.png)

选择完作用域后，请单击 "应用" 以保存所做的选择。 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>按资源组或资源拆分和筛选

绘制资源后，可以使用拆分和筛选工具来更深入地了解数据。 

利用拆分，可以可视化不同的度量段之间的比较情况。 例如，当你为多个资源绘制指标时，可以使用 "应用拆分" 工具按资源 id 或资源组进行拆分。 这使你可以轻松地跨多个资源或资源组比较单个指标。  

例如，下面是跨9VMs 的 CPU 百分比的图表。 通过按资源 id 进行拆分，可以轻松查看每个 VM 的 CPU 百分比差异。 

![屏幕截图，显示如何使用拆分来查看每个 VM 的 CPU 百分比](./media/metrics-charts/026.png)

除了进行拆分，您还可以使用筛选功能，只显示您要查看的资源组。  例如，如果想要查看特定资源组的 Vm 的 CPU 百分比，可以使用 "添加筛选器" 工具按资源组进行筛选。 在此示例中，我们按 Azure-tailspintoys 进行筛选，这会删除与 TailspinToysDemo 中的资源关联的度量值。 

![显示如何按资源组进行筛选的屏幕截图](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>固定多个资源图表 

> [!WARNING] 
> 你必须在订阅级别具有监视读者权限，才能可视化多个资源、资源组或订阅的指标。 为此，请按照 [本文档](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)中的说明进行操作。 

若要固定多资源图表，请按照 [此处](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#create-alert-rules)的说明进行操作。 

## <a name="next-steps"></a>后续步骤

* [对指标资源管理器进行故障排除](metrics-troubleshoot.md)
* [查看 Azure 服务的可用指标列表](metrics-supported.md)
* [查看已配置图表的示例](metric-chart-samples.md)

