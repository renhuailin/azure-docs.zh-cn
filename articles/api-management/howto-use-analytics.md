---
title: 在 Azure API 管理中使用 API 分析 |Microsoft Docs
description: 在 Azure API 管理中使用分析，以帮助你了解和分类 Api 和 API 性能的使用情况。
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841382"
---
# <a name="get-api-analytics-in-azure-api-management"></a>在 Azure API 管理中获取 API 分析

Azure API 管理为 Api 提供内置的分析。 跨多个维度分析 API 管理实例中的 Api 的使用情况和性能，包括：

* 时间
* 地理位置
* API
* API 操作
* 产品
* 订阅
* 用户
* 请求

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="门户中的时间线分析":::

使用分析实现对 Api 进行高级监视和故障排除。 有关其他监视功能，包括接近实时的指标和诊断和审核的资源日志，请参阅 [教程：监视已发布的 api](api-management-howto-use-azure-monitor.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>分析-门户

使用 Azure 门户来一目了然地查看 API 管理实例的分析数据。

1. 在 [Azure 门户](https://portal.azure.com)，导航到 API 管理实例。 
1. 在左侧菜单中的 " **监视**" 下，选择 " **分析**"。

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="在门户中为 API 管理实例选择分析":::  
1. 选择数据的时间范围，或输入自定义时间范围。
1. 选择分析数据的报表类别，如 **时间线**、 **地理位置** 等。
1. 根据需要，还可以按一个或多个其他类别筛选报表。

## <a name="analytics---rest-api"></a>分析-REST API

使用 API 管理 REST API 中的 [报表](/rest/api/apimanagement/2019-12-01/reports) 操作来检索和筛选 api 管理实例的分析数据。

可用操作按 API、地理位置、API 操作、产品、请求、订阅、时间或用户返回报表记录。

## <a name="next-steps"></a>后续步骤

* 有关 API 管理中 Azure Monitor 功能的简介，请参阅 [教程：监视已发布的 api](api-management-howto-use-azure-monitor.md)
* 有关 HTTP 日志记录和监视的详细信息，请参阅 [通过 AZURE API 管理、事件中心和 Moesif 监视 api](api-management-log-to-eventhub-sample.md)。
* 了解如何将 [AZURE API 管理与 Azure 应用程序 Insights](api-management-howto-app-insights.md)集成。