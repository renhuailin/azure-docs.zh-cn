---
title: 在 Azure 指标资源管理器中查看多个资源
description: 了解如何使用 Azure 指标资源管理器可视化多个资源。
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 23507a01f3ed0015690d9655fde92a875f89c2da
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095471"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>在 Azure 指标资源管理器中查看多个资源

资源作用域选取器允许你查看同一订阅和区域内多个资源之间的指标。 本文介绍如何使用 Azure Monitor 的 Azure 指标资源管理器功能查看多个资源。 

## <a name="select-a-resource"></a>选择资源 

从 Azure Monitor 菜单或从资源的菜单的“监视”部分选择“指标”    。 然后选择“选择范围”以打开范围选取器。 

使用范围选取器选择要查看其指标的资源。 如果从资源菜单中打开 "指标资源管理器"，则应填充范围。 

![屏幕截图，显示如何打开资源范围选取器。](./media/metrics-dynamic-scope/019.png)

## <a name="select-multiple-resources"></a>选择多个资源 

某些资源类型可以查询多个资源的指标。 指标必须位于相同的订阅和位置。 可以在“资源类型”菜单顶部找到这些资源类型。

![显示与多个资源兼容的资源菜单的屏幕截图。](./media/metrics-dynamic-scope/020.png)

> [!WARNING] 
> 你必须在订阅级别具有 "监视读者" 权限，才能可视化多个资源、资源组或订阅的指标。 有关详细信息，请参阅 [使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

若要可视化多个资源的指标，请首先在资源范围选取器中选择多个资源。 

![显示如何选择多个资源的屏幕截图。](./media/metrics-dynamic-scope/021.png)

> [!NOTE]
> 选择的资源必须在相同的资源类型、位置和订阅中。 不符合这些条件的资源不能选择。 

完成后，选择 " **应用** " 以保存你的选择。 

## <a name="select-a-resource-group-or-subscription"></a>选择资源组或订阅 

> [!WARNING]
> 你必须在订阅级别具有 "监视读者" 权限，才能可视化多个资源、资源组或订阅的指标。 

对于兼容多个资源的类型，你可以查询跨订阅或跨多个资源组的指标。 首先选择订阅或一个或多个资源组： 

![显示如何跨多个资源组进行查询的屏幕截图。](./media/metrics-dynamic-scope/022.png)

选择 "资源类型" 和 "位置"。 

![显示选定资源组的屏幕截图。](./media/metrics-dynamic-scope/023.png)

你可以展开所选作用域，验证所选应用到的资源。

![显示组中选定资源的屏幕截图。](./media/metrics-dynamic-scope/024.png)

选择完作用域后，请选择 " **应用**"。 

## <a name="split-and-filter-by-resource-group-or-resources"></a>按资源组或资源进行拆分和筛选

绘制资源后，可以使用拆分和筛选来更深入地了解数据。 

利用拆分，可以可视化不同的度量段之间的比较情况。 例如，当你为多个资源绘制指标时，可以选择 " **应用拆分** " 以按资源 ID 或资源组拆分。 使用拆分可以比较多个资源或资源组的单个度量值。  

例如，下图显示了跨9个 Vm 的 CPU 百分比。 按资源 ID 拆分时，可以看到 CPU 与 VM 有多少差异。 

![屏幕截图，显示如何使用拆分来查看跨 Vm 的 CPU 百分比。](./media/metrics-dynamic-scope/026.png)

除了拆分，你还可以使用筛选仅显示你要查看的资源组。  例如，若要查看特定资源组的 Vm 的 CPU 百分比，可以选择 " **添加筛选** 器" 以按资源组筛选。 

在此示例中，我们按 TailspinToysDemo 进行筛选。 此处，筛选器将删除与 Azure-tailspintoys 中的资源相关联的度量值。 

![显示如何按资源组进行筛选的屏幕截图。](./media/metrics-dynamic-scope/027.png)

## <a name="pin-multiple-resource-charts"></a>固定多个资源图表 

跨资源组和订阅可视化度量值的多个资源图表要求用户在订阅级别具有 " *监视读取* 者" 权限。 确保将多个资源图表固定到的仪表板的所有用户都具有足够的权限。 有关详细信息，请参阅 [使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

若要将多个资源图表固定到仪表板，请参阅 [固定到仪表板](../essentials/metrics-charts.md#pinning-to-dashboards)。 

## <a name="next-steps"></a>后续步骤

* [指标资源管理器疑难解答](../essentials/metrics-troubleshoot.md)
* [查看 Azure 服务的可用指标列表](../platform/metrics-supported.md)
* [查看已配置图表的示例](../essentials/metric-chart-samples.md)