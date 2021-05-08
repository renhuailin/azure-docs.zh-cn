---
title: 监视 Azure Static Web Apps 预览
description: 在 Azure Static Web Apps 预览中监视请求、失败和跟踪信息
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: cshoe
ms.openlocfilehash: 474b612d835ab415f9607f737ef219acc2e99152
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108077644"
---
# <a name="monitor-azure-static-web-apps"></a>创建 Azure Static Web Apps

启用 [Application Insights](../azure-monitor/app/app-insights-overview.md)，以监视 API 请求、失败和跟踪信息。

> [!NOTE]
> 将 Application Insights 与 Azure Static Web Apps 配合使用时，需要使用包含 [API](./add-api.md) 的应用程序。

## <a name="add-monitoring"></a>添加监视

使用以下步骤将 Application Insights 监视添加到静态 Web 应用。

1. 在 Azure 门户中打开 Static Web Apps 实例。

1. 从菜单中选择“Application Insights”。

1. 选择“启用 Application Insights”旁边的“是”。

1. 选择“保存”。

:::image type="content" source="media/monitoring/azure-static-web-apps-application-insights-add.png" alt-text="将 Application Insights 添加到 Azure Static Web Apps":::

> [!NOTE]
> 创建 Application Insights 实例后，系统会在用于集中链接服务的 Azure Static Web Apps 实例中创建关联的应用程序设置。

## <a name="access-data"></a>访问数据

1. 从静态 Web 应用的“概览”窗口中，选择“资源组”旁边的链接。

1. 从列表中选择以与静态 Web 应用相同的名称作为前缀的 Application Insights 实例。

以下内容突出显示了门户中的几个位置，这些位置将用于检查应用程序的 API 终结点的各个方面。

> [!NOTE]
> 有关 Application Insights 使用情况的详细信息，请参阅[我要在何处查看遥测数据？](../azure-monitor/app/app-insights-overview.md#where-do-i-see-my-telemetry)。

| 类型 | 菜单位置 | 说明 |
|--- | --- | --- |
| [失败](../azure-monitor/app/asp-net-exceptions.md) | _调查 > 故障_ | 查看失败请求。 |
| [服务器请求数](../azure-monitor/app/tutorial-performance.md) | 调查 > 性能 | 查看单个 API 请求。  |
| [日志](../azure-monitor/app/diagnostic-search.md) | 监视 > 日志 | 与编辑器交互以查询事务日志。 |
| [度量值](../azure-monitor/essentials/app-insights-metrics.md) | 监视 > 指标 | 与设计器交互以使用各种指标创建自定义图表。 |

### <a name="traces"></a>跟踪

使用以下步骤在应用程序中查看跟踪。

1. 选择“监视”下的“日志”。

1. 将鼠标悬停在“查询”窗口的任意卡片上。

1. 选择“加载编辑器”按钮。

1. 将生成的查询替换为单词 `traces`。

1. 选择“运行”按钮。

:::image type="content" source="media/monitoring/azure-static-web-apps-application-insights-traces.png" alt-text="查看 Application Insights 跟踪":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [应用程序身份验证和授权](authentication-authorization.md)
