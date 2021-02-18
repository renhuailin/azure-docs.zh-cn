---
title: Azure 前门标准/高级 (预览版) 报表
description: 本文介绍如何在 Azure 前门中进行报表工作。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098804"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Azure 前门标准/高级 (预览版) 报表

> [!IMPORTANT]
> Azure 前门标准/高级 (预览版) 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 前门标准/高级分析报表提供有关 Azure 前门如何与关联的 Web 应用程序防火墙指标一起工作的内置和双向视图。 还可以利用访问日志进行进一步的疑难解答和调试。 Azure 前门分析报表包含流量报表和安全报告。

| 报表 | 详细信息 |
|---------|---------|
| 关键指标概述 | 显示从 Azure 前门边缘发送到客户端的总体数据<br/>-峰值带宽<br/>-个请求 <br/>-缓存命中率<br/> -总延迟<br/>-5XX 错误率 |
| 按域的流量 | -提供配置文件下所有域的概述<br/>-从 AFD edge 传输到客户端的数据的细分<br/>-请求总数<br/>-域的 3XX/4XX/5XX 响应代码 |
| 按位置排列的流量 | -显示上个国家/地区的请求和使用情况的地图视图<br/>-热门国家/地区的趋势视图 |
| 使用情况 | -显示从 Azure 前门边缘到客户端的数据传出<br/>-从原点到 AFD 边缘的数据传出<br/>-从 AFD 边缘到客户端的带宽<br/>-从原点到 AFD 边缘的带宽<br/>-个请求<br/>-总延迟<br/>-按 HTTP 状态代码的请求计数趋势 |
| Caching | -按请求计数显示缓存命中率<br/>-命中和未命中请求的趋势视图 |
| 顶部 URL | -显示请求计数 <br/>-已传输数据 <br/>-缓存命中率 <br/>-请求的50资产的响应状态代码分配。 |
| 热门引用 | -显示请求计数 <br/>-已传输数据 <br/>-缓存命中率 <br/>-用于生成流量的顶部50引用的响应状态代码分配。 |
| 排名靠前的用户代理 | -显示请求计数 <br/>-已传输数据 <br/>-缓存命中率 <br/>-用于请求内容的前50用户代理的响应状态代码分配。 |

| 安全报表 | 详细信息 |
|---------|---------|
| 关键指标概述 | -显示匹配的 WAF 规则<br/>-匹配的 OWASP 规则<br/>-匹配的机器人规则<br/>-匹配的自定义规则 |
| 度量值（按维度） | -按操作划分匹配的 WAF 规则<br/>-按规则集类型对事件进行圆环图，按规则组事件<br/>-按规则 ID、国家/地区、IP 地址、URL 和用户代理列出的排名靠前的事件分解  |

> [!NOTE]
> 安全报告仅适用于 Azure 前门高级 SKU。

大多数报表都基于访问日志，并在 Azure 前门上免费提供给客户。 客户无需启用访问日志，也无需进行任何配置即可查看这些报表。 可以通过门户和 API 来访问报表。 还支持 CSV 下载。 

报表支持之前90天内所选的任何日期范围。 每隔5分钟、每小时或每天基于所选日期范围的数据点。 通常情况下，你可以在一小时内查看数据，偶尔延迟时间最长为几小时。 

## <a name="access-reports-using-the-azure-portal"></a>使用 Azure 门户访问报表

1. 登录到 [Azure 门户](https://portal.azure.com) 并选择 Azure 前门标准/高级配置文件。

1. 在导航窗格中，选择 "*分析*" 下的 "**报告" 或 "安全性**"。

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="报表登录页的屏幕截图":::

1. 有七个不同维度的选项卡，请选择相关维度。

   * 按域的流量
   * 使用情况 
   * 按位置排列的流量
   * 缓存
   * 顶部 url
   * 热门引用
   * 排名靠前的用户代理

1. 选择维度后，可以选择不同的筛选器。
  
    1. **显示数据** -选择要按域查看流量的日期范围。 可用范围包括：
        
        * 过去 24 小时
        * 过去 7 天
        * 最近 30 天
        * 过去90天
        * 本月
        * 上个月
        * 自定义日期

         默认情况下，数据显示在过去7天内。 对于带有折线图的选项卡，数据粒度会将作为默认行为选择的日期范围。 
    
        * 5分钟-一个数据点，每5分钟一次，日期范围小于或等于24小时。
        * 按小时–一小时内日期范围为24小时到30天的数据
        * 按天–每日数据范围的日期范围大于30天。

        始终可以使用聚合来更改默认聚合粒度。 注意：5分钟不适用于超过14天的数据范围。 

    1. **位置** -按国家/地区选择单个或多个客户端位置。 国家/地区分为六个区域：北美、亚洲、欧洲、非洲、大洋洲和南美洲。 请参阅 [区域/国家/地区映射](https://en.wikipedia.org/wiki/Subregion)。 默认情况下，将选择所有国家/地区。
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="位置维度报表的屏幕截图。":::
   
    1. **协议** -选择 HTTP 或 HTTPS 以查看流量数据。
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="协议维度报表的屏幕截图。":::
    
    1. **域** -选择 "单个或多个终结点" 或 "自定义域"。 默认情况下，将选择所有终结点和自定义域。 
    
        * 如果删除一个配置文件中的终结点或自定义域，然后在另一个配置文件中重新创建相同的终结点或域，则为。 终结点将被视为第二个终结点。  
        * 如果你正在按自定义域查看报表-删除一个自定义域并将其绑定到不同的终结点。 它们将被视为一个自定义域。 如果按终结点查看，它们将被视为单独的项。 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="域维度报表的屏幕截图。":::

1. 如果要将数据导出到 CSV 文件，请选择 "选择" 选项卡上的 " *下载 CSV* " 链接。

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="下载用于报表的 csv 文件的屏幕截图。":::

### <a name="key-metrics-for-all-reports"></a>所有报表的关键指标

| 指标 | 描述 |
|---------|---------|
| 已传输数据 | 显示在所选时间范围、客户端位置、域和协议中，从 AFD 边缘 Pop 传输到客户端的数据。 |
| 最大带宽 | 对于选定的时间范围、客户端位置、域和协议，每秒从 Azure 前门边缘 Pop 到客户端的最大带宽使用量。 | 
| 请求总数 | AFD edge Pop 对所选时间范围、客户端位置、域和协议的客户端做出响应的请求数。 |
| 缓存命中率 | AFD 为所选时间范围、客户端位置、域和协议的边缘缓存提供内容的所有可缓存请求的百分比。 |
| 5XX 错误率 | 请求的 HTTP 状态代码对所选时间范围、客户端位置、域和协议的5XX 的请求百分比。 |
| 总延迟 | 所选时间范围内所有请求的平均延迟时间（客户端位置、域和协议）。 每个请求的延迟时间都是由 Azure 前门接收客户端请求到从 Azure 前门发送到客户端的上一个响应字节时的总时间。 |

## <a name="traffic-by-domain"></a>按域的流量

按域显示的流量提供此 Azure 前门配置文件下所有域的网格视图。 在此报表中，可以查看： 
* 请求
* 从 Azure 前门向客户端传输的数据
* 每个域的状态代码 (3XX、4Xx 和 5XX) 的请求

域包括终结点和自定义域，如访问报表会话中所述。  

如果在预期的下面找到指标，你可以访问其他选项卡进行进一步调查，或查看访问日志以获取详细信息。 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="报表登录页的屏幕截图":::


## <a name="usage"></a>使用情况

此报表按不同维度显示流量和响应状态代码的趋势，其中包括：

* 数据从边缘传输到客户端，而从原点传输到折线图中的边缘。 

* 数据在折线图中按协议从边缘传输到客户端。 

* 折线图中从边缘到客户端的请求数。  

* 在折线图中，按协议、HTTP 和 HTTPS 从边缘向客户端发出的请求数。 

* 折线图中从边缘到客户端的带宽。 

* 总延迟时间，用于测量从前门接收的客户端请求到客户端的上一个响应字节之间的总时间。

* 在折线图中，通过 HTTP 状态代码从边缘向客户端发出的请求数。 每个请求都会生成一个 HTTP 状态代码。 HTTP 状态代码显示在原始日志中的 HTTPStatusCode 中。 状态代码介绍 CDN 边缘如何处理请求。 例如，2xx 状态代码指示请求已成功提供给客户端。 而4xx 状态代码则指示发生了错误。 有关 HTTP 状态代码的详细信息，请参阅 HTTP 状态代码列表。 

* 通过 HTTP 状态代码从边缘到客户端的请求数。 网格中所有请求中的 HTTP 状态代码发出的请求的百分比。 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="按使用情况报表的屏幕截图" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>按位置排列的流量

此报表显示最多访问你的资产的访问者所在国家/地区的前50个位置。 该报表还按国家/地区提供度量值的细分，并为你提供在其中生成最多流量的国家/地区的总体视图。 最后，可以看到哪个国家/地区具有更高的缓存命中率或 4XX/5XX 错误代码。

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="按位置的报表的屏幕截图" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

报表中包括以下内容：

* 由上50国家/地区的全球地图视图，通过数据传出或请求选择。
* 两个折线图：按传输的数据和所选请求的前五个国家/地区趋势视图。 
* 将相应数据从 AFD 传输到客户端的前几个国家/地区的数据，在所有国家/地区的所有国家/地区，请求，个请求，个请求%，个在所有国家/地区，个

## <a name="caching"></a>Caching

缓存报表提供基于请求的缓存命中/未命中数和缓存命中率的图表视图。 这些关键指标说明了 CDN 如何缓存内容，因为缓存命中最快的性能结果。 可以通过最大限度地减少缓存未命中数来优化数据传递速度。 此报告包括：

* 折线图中的缓存命中数和未命中数趋势。

* 折线图中的缓存命中率。

缓存命中/未命中描述客户端请求的请求数缓存命中数和缓存未命中数。

* 命中：从 Azure CDN 边缘服务器直接提供的客户端请求。 指的是那些请求在原始日志中的 CacheStatus 值命中、PARTIAL_HIT 或远程命中。 

* 未命中：由 Azure CDN 边缘服务器提供的客户端请求从源获取内容。 指的是，其 CacheStatus 原始日志中的字段的值会被忽略。 

**缓存命中率** ：说明直接从边缘提供的缓存请求的百分比。 缓存命中率的公式为： `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` 。 

此报表需要考虑缓存方案，并在计算中考虑到满足以下要求的请求。 

* 请求的内容缓存在最靠近请求者或源盾牌的 POP 上。 

* 对象分块的部分缓存内容。

它不包括以下所有情况： 

* 由于规则集而被拒绝的请求。

* 包含已设置为禁用缓存的匹配规则集的请求。 

* WAF 阻止的请求。 

* 源响应标头指示不应缓存它们。 例如，Cache-Control: private、Cache-Control: no-cache 或 Pragma: no-cache 标头会阻止资产进行缓存。 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="用于缓存的报表的屏幕截图。":::

## <a name="top-urls"></a>顶部 Url

使用顶部 Url 可以查看通过特定终结点或自定义域产生的流量。 过去90天内，你将看到请求最多的50资产的数据。 常用 Url 将显示以下值。 用户可以按请求计数、请求%、传输的数据和传输的数据对 Url 进行排序。 所有指标都按小时聚合，并且根据所选的时间范围可能会有所不同。 URL 引用访问日志中 RequestUri 的值。 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="顶层 URL 报表的屏幕截图。":::

* URL，是指请求的资产的完整路径（格式为） `http(s)://contoso.com/index.html/images/example.jpg` 。 
* 请求计数。 
* 请求% 由 Azure 前门提供服务的请求总数。 
* 传输的数据。 
* 传输的数据%。 
* 缓存命中率百分比
* 响应代码为4XX 的请求
* 响应代码为5XX 的请求

> [!NOTE]
> 排名靠前的 Url 可能会随时间而变化，若要获取最高 50 Url 的准确列表，Azure 前门会按小时对所有 URL 请求进行计数，并在一天的整个过程中保持运行总计。 位于 500 Url 底部的 Url 可能会在一天中出现在列表中，因此这些 Url 的总数大致为近似值。  
>
> 前50个 Url 可能会增加和落在列表中，但它们很少会从列表中消失，因此，适用于顶级 Url 的数字通常是可靠的。 当某个 URL 下拉列表并再次增加时，从列表中找不到该 URL 时，将根据该时间段内显示的 URL 的请求编号来估计请求数。  
>
> 相同的逻辑适用于排名靠前的用户代理。 

## <a name="top-referrers"></a>顶级引用

顶级引用允许客户查看在特定终结点或自定义域上发出最多内容请求的前50个引用站点。 可以查看过去90天内的任何时间段的数据。 引用站点指示从中生成请求的 URL。 引用方可能来自搜索引擎或其他网站。 如果用户键入 URL (例如，http (s) ：//contoso.com/index.html) 直接加入到浏览器的地址行，则请求的引用者为 "空"。 顶部引用报表包含以下值。 可以按请求计数、请求%、传输的数据和传输的数据进行排序。 所有指标都按小时聚合，并且根据所选的时间范围可能会有所不同。 

* 引用源，原始日志中引用的值 
* 请求计数 
* 请求在所选时间段内由 Azure CDN 提供服务的请求的百分比。 
* 传输的数据 
* 传输的数据百分比 
* 缓存命中率百分比
* 响应代码为4XX 的请求
* 响应代码为5XX 的请求

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="热门引用报表的屏幕截图。":::

## <a name="top-user-agent"></a>排名靠前的用户代理

此报表允许您拥有用于请求内容的前50个用户代理的图形和统计信息视图。 例如，
* Mozilla/5.0 (Windows NT 10.0;WOW64)  
* AppleWebKit/537.36 (KHTML，如 Gecko)  
* Chrome/86.0.4240.75 
* Safari/537.36。  

网格显示请求计数、请求%、传输的数据和已传输的数据、缓存命中率百分比、响应代码为4XX 的请求和响应代码为5XX 的请求数。 用户代理引用 "访问日志" 中 "UserAgent" 的值。

## <a name="security-report"></a>安全报表

此报表允许您按不同的维度使用 WAF 模式的图形和统计信息视图。

| 维度 | 说明 |
|---------|---------|
| 概述指标-匹配的 WAF 规则 | 与自定义 WAF 规则、托管 WAF 规则和机器人管理器匹配的请求。 |
| 概述指标-阻止的请求 | 与 WAF 规则匹配的所有请求中 WAF 规则阻止的请求的百分比。 |
| 概述指标-匹配的托管规则 | 用于阻止、记录、允许和重定向的请求的四行图表趋势。 |
| 概述指标-匹配的自定义规则 | 与自定义 WAF 规则匹配的请求。 |
| 概述指标-匹配的机器人规则 | 与机器人管理器匹配的请求。 |
| 按操作的 WAF 请求趋势 | 用于阻止、记录、允许和重定向的请求的四行图表趋势。 |
| 按规则类型的事件 | WAF 请求按规则类型分配的圆环图，如 Bot、自定义规则和托管规则。 |
| 按规则组的事件 | WAF 按规则组请求分发的圆环图。 |
| 按操作请求 | 按操作的请求表，按降序排序。 |
| 按 top 规则 Id 列出的请求 | 按 top 50 规则 Id 排序的请求表，按降序排列。 |
| 按顶层国家/地区的请求 |  排名靠前的50国家/地区的请求的表。 |
| 排名靠前的客户端 Ip 请求 |  按降序列出排名靠前50的 Ip 请求的表。 |
| 按 top 请求 URL 列出的请求 |  按降序排列的请求表，其中的前50个 Url。 |
| 按 top 主机名请求 | 由 top 50 主机名的请求表，按降序排列。 |
| 排名靠前的用户代理请求 | 由 top 50 用户代理请求的表，按降序排列。 |

## <a name="cvs-format"></a>CVS 格式

您可以为报表中的不同选项卡下载 CSV 文件。 本部分介绍每个 CSV 文件中的值。

### <a name="general-information-about-the-cvs-report"></a>CVS 报表的常规信息

每个 CSV 报表都包含一些常规信息，所有 CSV 文件中都提供了这些信息。 包含基于所下载报表的变量。 


| 值 | 描述 |
|---------|---------|
| 报表 | 报表的名称。 | 
| 域 | 报表的终结点或自定义域的列表。 |
| StartDateUTC | 生成报表的日期范围的开始时间，采用协调世界时 (UTC)  |
| EndDateUTC | 生成报表的日期范围的结束时间，采用协调世界时 (UTC)  |
| GeneratedTimeUTC | 生成报表的日期和时间，采用协调世界时 (UTC)  |
| 位置 | 发出客户端请求的国家/地区的列表。 默认情况下，该值为 ALL。 不适用于安全报告。 |
| 协议 | 请求、HTTP 或 HTTPs 的协议。 在报表和安全报表中，不适用于用户代理的顶级 URL 和流量。 |
| 聚合 | 每一行中数据聚合的粒度，每隔5分钟、每小时一次。 在报表和安全报表中，不适用于按域、顶级 URL 和流量按用户代理的流量。 |

### <a name="data-in-traffic-by-domain"></a>按域的流量中的数据

* 域 
* 请求总数 
* 缓存命中率 
* 3XX 请求 
* 4XX 请求 
* 5XX 请求 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>按位置排列的流量中的数据

* 位置
* TotalRequests
* 需要
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>使用中的数据

此 CSV 文件中有三个报表。 一个用于 HTTP 协议，一个用于 HTTPS 协议，另一个用于 HTTP 状态代码。 

HTTP 和 HTTPs 报表共享相同的数据集。 

* 时间 
* 协议 
* DataTransferred (字节)  
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

报告 HTTP 状态代码。 

* 时间 
* DataTransferred (字节)  
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>缓存中的数据 

* 时间
* CacheHitRatio 
* HitRequests 
* MissRequests 

### <a name="data-in-top-url"></a>顶部 URL 中的数据 

* URL 
* TotalRequests 
* 需要 
* DataTransferred (字节)  
* DataTransferred% 

### <a name="data-in-user-agent"></a>用户代理中的数据 

* UserAgent 
* TotalRequests 
* 需要 
* DataTransferred (字节)  
* DataTransferred% 

### <a name="security-report"></a>安全报表 

下面有七个表，它们都具有相同的字段。  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* RedirectedRequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

这七个表适用于时间、规则 ID、国家/地区、IP 地址、URL、主机名、用户代理。 

## <a name="next-steps"></a>后续步骤

了解 [Azure 前门标准/高级实时监视指标](how-to-monitor-metrics.md)。
