---
title: 将分析添加到必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 必应统计信息为必应图像搜索 API 提供分析。 分析包括调用量、最常用的查询字符串、地理分布等等。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.custom: seodec2018
ms.openlocfilehash: 878a6fd49a46b47c3243b168469a15909102a019
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587055"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>将分析添加到必应搜索 API

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

必应统计信息为必应搜索 API 提供分析。 这些分析包括调用量、最常用的查询字符串、地理分布等等。 可导航到 Azure 资源并单击“启用必应统计信息”，在 [Azure 门户](https://ms.portal.azure.com)中启用必应统计信息。

> [!IMPORTANT]
> * 必应统计信息不适用于免费 `F0` 定价层上的资源。
> * 无法使用通过必应统计信息仪表板提供的任何数据来创建要分发到第三方的应用程序。
> * 启用必应统计信息会略微增加订阅费用。 有关详细信息，请参阅[定价](https://aka.ms/bingstatisticspricing)。


下图显示了每个必应搜索 API 终结点的可用分析。

![按终结点显示的分布支持矩阵](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>访问分析

必应每 24 小时更新一次分析数据，并保留历史记录最长 13 个月，可从[分析仪表板](https://bingapistatistics.com)访问这些历史记录。 确保使用注册必应统计信息时所用的同一 Microsoft 帐户 (MSA) 登录。

> [!NOTE]  
> * 最长可能需要在 24 小时后，指标才会显示在仪表板上。 仪表板显示上次更新数据的日期和时间。  
> * 从启用必应统计信息加载项时开始提供指标。

## <a name="filter-the-data"></a>筛选数据

默认情况下，图表和图形显示你有权访问的所有指标和数据。 选择资源、市场、终结点和所需的报告时段，可以筛选图表和图形中显示的数据。 可以更改以下筛选：

- **资源 ID**：用于标识 Azure 订阅的唯一资源 ID。 如果你订阅了多个必应搜索 API 层，则该列表包含多个 ID。 默认已选中所有资源。  
  
- **市场**：结果的来源市场。 例如，en-us (美国英语) 默认已选中所有市场。 `en-WW` 市场是当调用未指定市场并且必应无法确定用户的市场时，必应使用的市场。  
  
- **终结点**：必应搜索 API 终结点。 该列表包含为其购买了付费订阅的所有终结点。 默认已选中所有终结点。  

- **时间范围**：报告时段。 可以指定：
  - **全部**：包含最多 13 个月的数据  
  - **过去 24 小时**：包含过去 24 小时的分析  
  - **过去一周**：包含过去 7 天的分析  
  - **过去一个月**：包含过去 30 天的分析  
  - **自定义日期范围**：包含指定日期范围的分析（如果可用）  

## <a name="charts-and-graphs"></a>图表和图形

仪表板显示适用于选定终结点的指标的图表和图形。 并非所有指标都适用于所有终结点。 每个终结点的图表和图形是静态的（无法选择要显示的图表和图形）。 仪表板仅显示具有数据的图表和图形。

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

下面是可能的指标和终结点限制。

- **调用量**：显示在报告时段发出的调用次数。 如果报告时段为一天，则图表显示每小时发出的调用次数。 否则，图表显示报告时段内每天发出的调用次数。  
  
  > [!NOTE]
  > 调用量可能不同于计费报告，后者通常只包括成功的调用。

- **最常见的查询**：显示在报告时段最常见的查询，以及每个查询的出现次数。 可以配置显示的查询数。 例如，可以显示最常见的 25、50 或 75 个查询。 “最常见的查询”不适用于以下终结点：  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > 可以抑制一些查询字词，以删除电子邮件、电话号码、SSN 等机密信息。

- **地理分布**：搜索结果的来源市场。 例如 `en-us`（英语，美国）。 必应使用 `mkt` 查询参数来确定市场（如果已指定）。 否则，必应使用调用方 IP 地址等信号来确定市场。

- **响应代码分布**：在报告时段发出的所有调用的 HTTP 状态代码。

- **调用来源分布**：用户使用的浏览器类型。 例如 Microsoft Edge、Chrome、Safari 和 FireFox。 从浏览器外部（例如机器人、Postman）发出的调用，或在控制台应用中使用 curl 发出的调用，将分组到“库”下面。 来源是使用请求的 User-Agent 标头值确定的。 如果请求不包含 User-Agent 标头，则必应会尝试从其他信号派生来源。  

- **安全搜索分布**：安全搜索值的分布。 例如 off、moderate 或 strict。 `safeSearch` 查询参数包含该值（如果已指定）。 否则，必应使用默认值 moderate。  

- **请求的回答分布**：在 `responseFilter` 查询参数中请求的 Web 搜索 API 回答。  

- **返回的回答分布**：Web 搜索 API 在响应中返回的回答。

- **响应服务器分布**：为 API 请求提供服务的应用程序服务器。 可能的值为 Bing.com（从台式机和便携式设备为流量提供服务）和 Bing.com-mobile（从移动设备为流量提供服务）。 服务器是使用请求的 User-Agent 标头值确定的。 如果请求不包含 User-Agent 标头，则必应会尝试从其他信号派生服务器。

## <a name="next-steps"></a>后续步骤

* [什么是必应搜索 API？](bing-api-comparison.md)
* [必应搜索 API 使用和显示要求](use-display-requirements.md)