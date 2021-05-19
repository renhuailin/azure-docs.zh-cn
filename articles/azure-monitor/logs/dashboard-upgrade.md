---
title: 升级 Log Analytics 仪表板可视化效果
description: 了解如何通过可提供强大见解的查询升级 Log Analytics 仪表板可视化效果。
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 276ea1e2f083da7ab4a6ab44e60bc7e0832e8651
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030930"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>升级 Log Analytics 仪表板可视化效果

2020 年 2 月，我们引入了改进的可视化技术。 快速改进和增强直观显示查询结果并获得强大见解的功能。 

有关此升级的详细信息，可参阅[此 Azure 更新](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/)。 

这项新的可视化技术为围绕查询结果集的新体验和改进体验铺平了道路。 

## <a name="dashboards-in-azure"></a>Azure 中的仪表板

通过 Azure 仪表板可直观显示整个 Azure 外围应用的状态。 Azure 仪表板旨在为你的 Azure 资产状态提供单一管理平台，允许使用各种常见操作快捷方式。 

有关详细信息，请参阅 [Azure 仪表板](../../azure-portal/azure-portal-dashboards.md)


## <a name="upgrading-log-analytics-dashboard-parts"></a>升级 Log Analytics 仪表板部件

新的可视化技术解决了旧实现的一些常见问题，并为固定的 Log Analytics 部件引入了一些新功能： 

- 相同可用类型 - Log Analytics 中所有可用的可视化类型都可以作为仪表板上固定的部件使用。

- 一致的外观 - 固定部件的可视化外观现在几乎与 Log Analytics 中的外观相同。 差异是由于优化导致的，优化要求视觉对象的数据内容存在细微差异。 若要深入了解这些差异，请参阅本文档的注意事项部分。

- 工具提示和标签 - 新的固定 Log Analytics 可视化效果支持工具提示。 饼图和圆环图现在支持标签。

- 交互式图例 - 单击可视化效果图例可以在固定的视觉对象中添加/删除维度，就如同在 Log Analytics 中一样。

## <a name="stage-1---opt-in-upgrade-message"></a>阶段 1 - 选择启用升级消息

当 Log Analytics 固定部件能够升级时，一个新的“选择启用”通知将出现在仪表板的 Log Analytics 固定部件上，允许用户升级其可视化效果。 如果想要体验新的可视化效果，请升级其仪表板中选定的可视化效果。

 
![边栏](media/dashboard-upgrade/update-message-1.png)
 
![此屏幕截图显示如何更新磁贴可视化效果。](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> 发布仪表板后，升级是不可逆的。 但是，如果你离开仪表板而不重新发布，则更改会被放弃。  

单击后，可视化效果将更新为新技术。 在 Log Analytics 中，可视化效果可能会出现细微差异，以与其外观保持一致。

升级可视化效果后，需要重新发布仪表板以使更改生效。

![此屏幕截图显示升级后的可视化效果。](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>阶段 2 - 迁移所有仪表板

初始选择启用期结束后，Log Analytics 团队会升级系统中的所有仪表板。 通过调整所有 Azure 仪表板，团队可以在整个板中引入更多可视化效果和体验改进。

## <a name="considerations"></a>注意事项

固定到仪表板的 Log Analytics 可视化效果有一些特定行为，旨在实现最佳体验。 将可视化效果固定到仪表板时，请查看以下设计注意事项。

### <a name="query-time-scope---30-day-limit"></a>查询时间范围 - 30 天限制

由于仪表板可能包含来自多个查询的多个可视化效果，因此单个固定查询的时间范围限制为 30 天。 单个查询只能针对小于或等于 30 天的时间范围运行。 此限制是为了确保仪表板加载时间合理。

### <a name="query-data-values---25-values-and-other-grouping"></a>查询数据值 - 25 个值和其他分组

仪表板可以在视觉上密集而复杂。 为了减少查看仪表板时的认知负担，我们通过将显示限制为 25 种不同的数据类型来优化可视化效果。 如果有 25 种以上的数据类型，Log Analytics 会优化数据。 它分别显示了 25 种类型（其中大多数数据是单独的），然后将其余的值分组为一个“其他”值。 下面的图表显示了这种情况。  

![此屏幕截图显示了具有 25 种不同数据类型的仪表板。](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>加载时刷新仪表板

仪表板在加载时刷新。 将执行与仪表板中固定的 Log Analytics 可视化效果相关的所有查询，并在加载后刷新仪表板。 如果仪表板页保持打开状态，则仪表板中的数据每 60 分钟刷新一次。

## <a name="next-steps"></a>后续步骤

[在 Log Analytics 中创建和共享仪表板](../visualize/tutorial-logs-dashboards.md)