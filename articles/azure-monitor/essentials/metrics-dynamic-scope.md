---
title: 在 Azure 指标资源管理器中查看多个资源
description: 了解如何使用 Azure 指标资源管理器可视化多个资源。
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.openlocfilehash: 85fa252f999407d5cd7f75e954554e3b14357071
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037313"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>在 Azure 指标资源管理器中查看多个资源

通过资源范围选取器，可以查看跨同一订阅和区域内的多个资源的指标。 本文介绍如何使用 Azure Monitor 的 Azure 指标资源管理器功能查看多个资源。 

## <a name="select-a-resource"></a>选择资源 

从 Azure Monitor 菜单或从资源的菜单的“监视”部分选择“指标”    。 然后选择“选择范围”以打开范围选取器。 

使用范围选取器选择要查看其指标的资源。 如果从资源的菜单中打开了指标资源管理器，则该范围应已填充完毕。 

![屏幕截图，显示如何打开资源范围选取器。](./media/metrics-dynamic-scope/019.png)

## <a name="select-multiple-resources"></a>选择多个资源 

某些资源类型可以查询多个资源的指标。 这些指标必须位于相同的订阅和位置中。 可以在“资源类型”菜单顶部找到这些资源类型。

![显示与多个资源兼容的资源的菜单的屏幕截图。](./media/metrics-dynamic-scope/020.png)

> [!WARNING] 
> 必须在订阅级别具有“监视读取者”权限，才能可视化跨多个资源、资源组或订阅的指标。 有关详细信息，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

若要可视化多个资源的指标，首先在资源范围选取器中选择多个资源。 

![显示如何选择多个资源的屏幕截图。](./media/metrics-dynamic-scope/021.png)

> [!NOTE]
> 选择的资源必须位于同一资源类型、位置和订阅中。 不符合这些条件的资源不可选择。 

完成后，选择“应用”以保存所做的选择。 

## <a name="select-a-resource-group-or-subscription"></a>选择资源组或订阅 

> [!WARNING]
> 必须在订阅级别具有“监视读取者”权限，才能可视化跨多个资源、资源组或订阅的指标。 

对于兼容多个资源的类型，你可以查询跨订阅或跨多个资源组的指标。 首先选择订阅或一个或多个资源组： 

![显示如何跨多个资源组进行查询的屏幕截图。](./media/metrics-dynamic-scope/022.png)

选择数据库类型和位置。 

![显示所选资源组的屏幕截图。](./media/metrics-dynamic-scope/023.png)

可以展开所选范围以验证你的选择所应用的资源。

![显示组中所选资源的屏幕截图。](./media/metrics-dynamic-scope/024.png)

选择范围后，选择“应用”。 

## <a name="split-and-filter-by-resource-group-or-resources"></a>按资源组或资源拆分和筛选

绘制资源后，可以使用拆分和筛选更深入地了解数据。 

使用拆分可以可视化指标不同部分之间的差异。 例如，当你为多个资源绘制指标时，可以选择“应用拆分”以按资源 ID 或资源组拆分。 使用拆分可以比较跨多个资源或资源组的单个指标。  

例如，下图显示了跨 9 个 VM 的 CPU 百分比。 按资源 ID 拆分时，可以看到 CPU 百分比与 VM 之间的差异。 

![显示如何使用拆分查看跨 VM 的 CPU 百分比的屏幕截图。](./media/metrics-dynamic-scope/026.png)

除了拆分，还可以使用筛选仅显示要查看的资源组。  例如，若要查看特定资源组的 VM 的 CPU 百分比，可以选择“添加筛选器”以按资源组筛选。 

在此示例中，我们按 TailspinToysDemo 进行筛选。 此处，筛选器将删除与 TailspinToys 中的资源关联的指标。 

![显示如何按资源组筛选的屏幕截图。](./media/metrics-dynamic-scope/027.png)

## <a name="pin-multiple-resource-charts"></a>固定多资源图表 

可视化跨资源组和订阅的指标的多资源图表要求用户在订阅级别具有“监视读取者”权限。 确保要将多资源图表固定到其中的仪表板的所有用户都具有足够的权限。 有关详细信息，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

若要将多资源图表固定到仪表板，请参阅[固定到仪表板](../essentials/metrics-charts.md#pinning-to-dashboards)。 

## <a name="next-steps"></a>后续步骤

* [对指标资源管理器进行故障排除](../essentials/metrics-troubleshoot.md)
* [查看 Azure 服务的可用指标列表](./metrics-supported.md)
* [查看已配置图表的示例](../essentials/metric-chart-samples.md)