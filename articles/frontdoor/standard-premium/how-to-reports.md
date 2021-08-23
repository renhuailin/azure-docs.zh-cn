---
title: Azure Front Door 标准版/高级版（预览版）报告
description: 本文介绍 Azure Front Door 中报告功能的工作原理。
services: frontdoor
author: jessie-jyy
ms.service: frontdoor
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: yuajia
ms.openlocfilehash: 8e43bf01be44c8d82bb8b5f9d466552fc9d7ad17
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113594996"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Azure Front Door 标准版/高级版（预览版）报告

> [!IMPORTANT]
> Azure Front Door 标准版/高级版（预览版）目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Front Door 标准版/高级版分析报告提供一个内置的全方位视图，帮助你了解 Azure Front Door 的行为方式以及相关的 Web 应用程序防火墙指标。 你还可以利用访问日志进行进一步的故障排除和调试。 Azure Front Door 分析报告包括流量报告和安全报告。

| 报表 | 详细信息 |
|---------|---------|
| 关键指标概述 | 显示从 Azure Front Door 边缘发送到客户端的总体数据<br/>- 峰值带宽<br/>- 请求数 <br/>- 缓存命中率<br/> - 总延迟时间<br/>- 5XX 错误率 |
| 流量(按域) | - 提供配置文件下所有域的概况<br/>- 从 AFD 边缘传输到客户端的数据量的细分<br/>- 请求总数<br/>- 按域显示的 3XX/4XX/5XX 响应代码 |
| 流量(按位置) | - 按热门国家/地区显示请求和使用情况的地图视图<br/>- 热门国家/地区的趋势视图 |
| 使用情况 | - 显示从 Azure Front Door 边缘传输到客户端的数据量<br/>- 从原点传输到 AFD 边缘的数据量<br/>- 从 AFD 边缘到客户端的带宽<br/>- 从原点到 AFD 边缘的带宽<br/>- 请求数<br/>- 总延迟时间<br/>- 按 HTTP 状态代码显示的请求计数趋势 |
| Caching | - 按请求计数显示缓存命中率<br/>- 命中和失误请求数的趋势视图 |
| 热门 URL | - 显示请求计数 <br/>- 已传输数据量 <br/>- 缓存命中率 <br/>- 最常请求的 50 项资产的响应状态代码分布。 |
| 热门引荐者 | - 显示请求计数 <br/>- 已传输数据量 <br/>- 缓存命中率 <br/>- 生成流量的前 50 个引荐者的响应状态代码分布。 |
| 热门用户代理 | - 显示请求计数 <br/>- 已传输数据量 <br/>- 缓存命中率 <br/>- 用于请求内容的前 50 个用户代理的响应状态代码分布。 |

| 安全报表 | 详细信息 |
|---------|---------|
| 关键指标概述 | - 显示匹配的 WAF 规则<br/>- 匹配的 OWASP 规则<br/>- 匹配的 BOT 规则<br/>- 匹配的自定义规则 |
| 指标(按维度) | - 按操作细分的匹配 WAF 规则趋势<br/>- 按规则集类型显示的事件和按规则组显示的事件的圆环图<br/>- 按规则 ID、国家/地区、IP 地址、URL 和用户代理显示的热门事件的细分列表  |

> [!NOTE]
> 安全报告仅适用于 Azure Front Door 高级版 SKU。

大多数报告都基于访问日志，并通过 Azure Front Door 免费提供给客户。 客户无需启用访问日志或进行任何配置即可查看这些报告。 可通过门户和 API 访问报告。 还支持 CSV 下载。 

报告支持过去 90 天内的任何选定日期范围。 根据选定的日期范围，每 5 分钟、每小时或每天一个数据点。 通常情况下，你可以查看延迟不到一小时的数据，偶尔也会有延迟长达几小时的数据。 

## <a name="access-reports-using-the-azure-portal"></a>使用 Azure 门户访问报告

1. 登录到 [Azure 门户](https://portal.azure.com)，选择你的 Azure Front Door 标准版/高级版配置文件。

1. 在导航窗格中，选择“分析”下的“报告”或“安全性”。

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="“报告”登陆页面的屏幕截图":::

1. 有七个不同维度的选项卡，请选择感兴趣的维度。

   * 流量(按域)
   * 使用情况 
   * 流量(按位置)
   * 缓存
   * 热门 URL
   * 热门引荐者
   * 热门用户代理

1. 选择维度后，可以选择其他筛选器。
  
    1. **显示以下时段的数据** - 选择要按域查看流量的日期范围。 可用范围包括：
        
        * 过去 24 小时
        * 过去 7 天
        * 最近 30 天
        * 过去 90 天
        * 本月
        * 上个月
        * 自定义日期

         默认情况下，系统显示过去 7 天的数据。 对于包含折线图的选项卡，数据粒度与选为默认行为的日期范围一致。 
    
        * 5 分钟 - 在少于或等于 24 小时的日期范围内，每 5 分钟一个数据点。
        * 按小时 - 在 24 小时至 30 天的日期范围内，每小时一个数据点
        * 按天 - 在超过 30 天的日期范围内，每小时一个数据点。

        始终可以使用“聚合”来更改默认聚合粒度。 注意：“5 分钟”不适用于超过 14 天的数据范围。 

    1. 位置 - 按国家/地区选择单个或多个客户端位置。 国家/地区分为六个区域：北美、亚洲、欧洲、非洲、大洋洲和南美洲。 请参阅[国家/地区映射](https://en.wikipedia.org/wiki/Subregion)。 默认情况下，所有国家/地区均处于选中状态。
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="位置维度报告的屏幕截图。":::
   
    1. **协议** - 选择 HTTP 或 HTTPS 以查看流量数据。
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="协议维度报告的屏幕截图。":::
    
    1. **域** - 选择单个或多个终结点或自定义域。 默认情况下，所有终结点和自定义域均处于选中状态。 
    
        * 如果在一个配置文件中删除终结点或自定义域，然后在另一个配置文件中重新创建相同的终结点或域。 该终结点将被视为第二个终结点。  
        * 如果正在按自定义域查看报告 - 删除一个自定义域并将其绑定到其他终结点时。 它们将被视为一个自定义域。 如果按终结点查看 - 它们将被视为单独的项。 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="域维度报告的屏幕截图。":::

1. 如果要将数据导出到 CSV 文件，请在所选选项卡上选择“下载 CSV”链接。

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="为报告下载 csv 文件的屏幕截图。":::

### <a name="key-metrics-for-all-reports"></a>所有报告的关键指标

| 指标 | 说明 |
|---------|---------|
| 已传输数据 | 针对所选的时间范围、客户端位置、域和协议，显示从 AFD 边缘 POP 传输到客户端的数据量。 |
| 峰值带宽 | 针对所选的时间范围、客户端位置、域和协议，从 Azure Front Door 边缘 POP 到客户端的峰值带宽使用率（以每秒位数为单位）。 | 
| 请求总数 | 针对所选的时间范围、客户端位置、域和协议，AFD 边缘 POP 对客户端响应的请求数。 |
| 缓存命中率 | 针对所选的时间范围、客户端位置、域和协议，AFD 从边缘缓存中为其提供内容的所有可缓存请求所占的百分比。 |
| 5XX 错误率 | 针对所选的时间范围、客户端位置、域和协议，返回给客户端的 HTTP 状态代码为 5XX 的请求所占的百分比。 |
| 总延迟 | 针对所选的时间范围、客户端位置、域和协议，所有请求的平均延迟时间。 每个请求的延迟时间的测量方式为：从 Azure Front Door 收到客户端请求到 Azure Front Door 发给客户端最后一个响应字节所经历的总时长。 |

## <a name="traffic-by-domain"></a>流量(按域)

“流量(按域)”提供此 Azure Front Door 配置文件下所有域的网格视图。 在此报告中，你可以查看： 
* 请求
* 从 Azure Front Door 传输到客户端的数据量
* 每个域中状态代码为 3XX、4XX 和 5XX 的请求数

如“访问报告”部分中所述，域包括终结点和自定义域。  

如果发现指标低于预期值，可以转到其他选项卡做进一步调查，或者查看访问日志以获取详细信息。 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="报告登陆页面的屏幕截图":::


## <a name="usage"></a>使用情况

此报告按不同维度显示流量和响应状态代码的趋势，其中包括：

* 折线图中从边缘传输到客户端以及从原点传输到边缘的数据量。 

* 折线图中按协议显示的从边缘传输到客户端的数据量。 

* 折线图中从边缘到客户端的请求数。  

* 折线图中按协议 HTTP 和 HTTPS 显示的从边缘到客户端的请求数。 

* 折线图中从边缘到客户端的带宽。 

* 总延迟时间，用于衡量从 Front Door 收到客户端请求，到 Front Door 发给客户端最后一个响应字节所经历的总时长。

* 折线图中按 HTTP 状态代码显示的从边缘到客户端的请求数。 每个请求都会生成一个 HTTP 状态代码。 HTTP 状态代码显示在原始日志的 HTTPStatusCode 中。 状态代码描述 CDN 边缘如何处理请求。 例如，2xx 状态代码表示请求已成功提供给客户端。 4xx 状态代码则表示发生了错误。 有关 HTTP 状态代码的详细信息，请参阅 HTTP 状态代码列表。 

* 按 HTTP 状态代码显示的从边缘到客户端的请求数。 按 HTTP 状态代码显示的请求在网格中的所有请求中所占的百分比。 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="使用情况报告的屏幕截图" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>流量(按位置)

此报告按国家/地区显示最常访问你资产的访问者所在的前 50 个位置。 此报告还提供按国家/地区显示的指标明细，并提供产生最多流量的国家/地区的总体视图。 最后，你可以看到哪个国家/地区具有更高的缓存命中率或 4XX/5XX 错误代码。

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="位置报告的屏幕截图" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

报告中包含以下内容：

* 按所选的已传出数据量或请求数列出的前 50 个国家/地区的世界地图视图。
* 按所选的已传出数据量或请求数列出的前五个国家/地区的两个折线图趋势视图。 
* 一个网格，其中包含热门国家/地区以及从 AFD 传出到客户端的相应数据量、已传出数据量在所有国家/地区中的百分比、请求数、请求数在所有国家/地区中的百分比、缓存命中率、4XX 响应代码和 5XX 响应代码。

## <a name="caching"></a>Caching

缓存报告提供基于请求的缓存命中/失误数以及缓存命中率的图表视图。 这些关键指标说明了 CDN 如何缓存内容，因为最快的性能源自缓存命中。 可以通过最大程度地减少缓存失误来优化数据传递速度。 此报告包括：

* 折线图形式的缓存命中和失误计数趋势。

* 折线图形式的缓存命中率。

缓存命中/失误数描述针对客户端请求，缓存命中和未命中的请求数。

* 命中：直接从 Azure CDN 边缘服务器响应的客户端请求。 指原始日志中 CacheStatus 的值为 HIT、PARTIAL_HIT 或 REMOTE HIT 的请求。 

* 失误：由从原点提取内容的 Azure CDN 边缘服务器响应的客户端请求。 指原始日志中 CacheStatus 字段的值为 MISS 的请求。 

缓存命中率描述了直接从边缘响应的缓存请求所占的百分比。 缓存命中率的公式为：`(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%`。 

此报告需要考虑缓存场景，并计算满足以下要求的请求。 

* 所请求的内容缓存在最靠近请求者或源盾牌的 POP 上。 

* 对象区块的部分缓存内容。

它不包括以下所有情况： 

* 由于规则集而被拒绝的请求。

* 包含已设置为禁用缓存的匹配规则集的请求。 

* WAF 阻止的请求。 

* 原点响应头指示不应缓存它们。 例如，Cache-Control: private、Cache-Control: no-cache 或 Pragma: no-cache 标头会阻止资产进行缓存。 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="缓存报告的屏幕截图。":::

## <a name="top-urls"></a>热门 URL

热门 URL 允许你查看特定终结点或自定义域上的流量。 你将看到在过去 90 天内的任何时期，最常请求的 50 项资产的数据。 热门 URL 将显示以下值。 用户可以按请求计数、请求百分比、已传输数据量和已传输数据量所占的百分比对 URL 进行排序。 所有指标均按小时聚合，并且可能会随所选时间范围而变化。 URL 是指访问日志中 RequestUri 的值。 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="热门 URL 报告的屏幕截图。":::

* URL，指所请求资产的完整路径，格式为 `http(s)://contoso.com/index.html/images/example.jpg`。 
* 请求计数。 
* 请求数占 Azure Front Door 响应的请求总数的百分比。 
* 已传输数据量。 
* 已传输数据量所占的百分比。 
* 缓存命中率百分比
* 响应代码为 4XX 的请求数
* 响应代码为 5XX 的请求数

> [!NOTE]
> 热门 URL 可能会随时间而变化；为了获取前 50 个 URL 的准确列表，Azure Front Door 会按小时对所有 URL 请求进行计数，并保留一天的汇总值。 500 个 URL 底部的 URL 可能会在一天内进入或掉出列表，因此这些 URL 的总数为近似值。  
>
> 前 50 个 URL 可能会在列表中上升和下降，但很少从列表中消失，因此热门 URL 的数量通常是可靠的。 如果某个 URL 在一天内先是掉出列表，然后又进入列表，那么将根据该 URL 出现在列表中的这段时间的请求数，来估计它从列表中消失的这段时间的请求数。  
>
> 同样的逻辑也适用于热门用户代理。 

## <a name="top-referrers"></a>热门引荐者

热门引荐者允许客户查看对特定终结点或自定义域上的内容发出最多请求的前 50 个引荐者。 你可以查看过去 90 天内任何时期的数据。 引荐者表示生成请求的 URL。 引荐者可能来自搜索引擎或其他网站。 如果用户直接在浏览器的地址栏中键入 URL（例如，http(s)://contoso.com/index.html），则所请求内容的引荐者为“空”。 热门引荐者报告包含以下值。 你可以按请求计数、请求百分比、已传输数据量和已传输数据量所占的百分比进行排序。 所有指标均按小时聚合，并且可能会随所选时间范围而变化。 

* 引荐者，即原始日志中 Referrer 的值 
* 请求计数 
* 在所选时间段内，请求数占 Azure CDN 响应的请求总数的百分比。 
* 传输的数据 
* 已传输数据量所占的百分比 
* 缓存命中率百分比
* 响应代码为 4XX 的请求数
* 响应代码为 5XX 的请求数

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="热门引荐者报告的屏幕截图。":::

## <a name="top-user-agent"></a>热门用户代理

此报告允许你查看用于请求内容的前 50 个用户代理的图形和统计视图。 例如，
* Mozilla/5.0（Windows NT 10.0；WOW64） 
* AppleWebKit/537.36（KHTML，例如 Gecko） 
* Chrome/86.0.4240.75 
* Safari/537.36。  

一个网格显示请求计数、请求百分比、已传输数据量和已传输数据量所占的百分比、缓存命中率百分比、响应代码为 4XX 的请求数和响应代码为 5XX 的请求数。 用户代理是指访问日志中 UserAgent 的值。

## <a name="security-report"></a>安全报告

此报告允许你按不同维度查看 WAF 模式的图形和统计视图。

| 维度 | 说明 |
|---------|---------|
| 概述指标 - 匹配的 WAF 规则 | 与自定义 WAF 规则、托管 WAF 规则和 Bot Manager 匹配的请求数。 |
| 概述指标 - 阻止的请求数 | 被 WAF 规则阻止的请求数在与 WAF 规则匹配的所有请求中所占的百分比。 |
| 概述指标 - 匹配的托管规则 | 请求的四种折线图趋势，分别为阻止、记录、允许和重定向。 |
| 概述指标 - 匹配的自定义规则 | 与自定义 WAF 规则匹配的请求数。 |
| 概述指标 - 匹配的 Bot 规则 | 与 Bot Manager 匹配的请求数。 |
| WAF 请求趋势(按操作) | 请求的四种折线图趋势，分别为阻止、记录、允许和重定向。 |
| 事件数(按规则类型) | 按规则类型（例如 Bot、自定义规则和托管规则）分布的 WAF 请求数的圆环图。 |
| 事件数(按规则组) | 按规则组分布的 WAF 请求数的圆环图。 |
| 请求数(按操作) | 按操作显示的请求数表，按降序排列。 |
| 请求数(按热门规则 ID) | 按前 50 个规则 ID 显示的请求数表，按降序排列。 |
| 请求数(按热门国家/地区) |  按前 50 个国家/地区列出的请求数表，按降序排列。 |
| 请求数(按热门客户端 IP) |  按前 50 个 IP 显示的请求数表，按降序排列。 |
| 请求数(按热门请求 URL) |  按前 50 个 URL 显示的请求数表，按降序排列。 |
| 请求数(按热门主机名) | 按前 50 个主机名显示的请求数表，按降序排列。 |
| 请求数(按热门用户代理) | 按前 50 个用户代理显示的请求数表，按降序排列。 |

## <a name="cvs-format"></a>CVS 格式

你可以为报告中的不同标签下载 CSV 文件。 本部分介绍每个 CSV 文件中的值。

### <a name="general-information-about-the-cvs-report"></a>有关 CVS 报告的常规信息

每个 CSV 报告都包含一些常规信息，这些信息在所有 CSV 文件中均可用。 变量基于所下载的报告。 


| 值 | 描述 |
|---------|---------|
| 报表 | 报表的名称。 | 
| 域 | 报告的终结点或自定义域列表。 |
| 开始时间 UTC | 生成报告的日期范围的开始时间，采用协调世界时 (UTC) |
| 结束时间 UTC | 生成报告的日期范围的结束时间，采用协调世界时 (UTC) |
| 生成时间 UTC | 生成报告的日期和时间，采用协调世界时 (UTC) |
| 位置 | 发起客户端请求的国家/地区列表。 默认情况下，该值为 ALL。 不适用于安全报告。 |
| 协议 | HTTP 或 HTTPs 请求协议。 不适用于报告和安全报告中的“热门 URL”和“流量(按用户代理)”。 |
| 聚合 | 每一行中数据聚合的粒度：每 5 分钟、每小时和每天。 不适用于报告和安全报告中的“流量(按域)”、“热门 URL”和“流量(按用户代理)”。 |

### <a name="data-in-traffic-by-domain"></a>“流量(按域)”中的数据

* Domain 
* 请求总数 
* 缓存命中率 
* 3XX 请求数 
* 4XX 请求数 
* 5XX 请求数 
* 从边缘传输到客户端的字节数 

### <a name="data-in-traffic-by-location"></a>“流量(按位置)”中的数据

* 位置
* TotalRequests
* 请求百分比
* 从边缘传输到客户端的字节数

### <a name="data-in-usage"></a>“使用情况”中的数据

此 CSV 文件中有三个报告。 分别是 HTTP 协议报告、HTTPS 协议报告和 HTTP 状态代码报告。 

HTTP 和 HTTPs 的报告共享相同的数据集。 

* 时间 
* 协议 
* 已传输数据量(字节) 
* 请求总数 
* 从边缘到客户端的 bps 
* 2XX 请求数 
* 3XX 请求数 
* 4XX 请求数 
* 5XX 请求数 

HTTP 状态代码报告。 

* 时间 
* 已传输数据量(字节) 
* 请求总数 
* 从边缘到客户端的 bps 
* 2XX 请求数 
* 3XX 请求数 
* 4XX 请求数 
* 5XX 请求数

### <a name="data-in-caching"></a>“缓存”中的数据 

* 时间
* 缓存命中率 
* 命中请求数 
* 失误请求数 

### <a name="data-in-top-url"></a>“热门 URL”中的数据 

* URL 
* TotalRequests 
* 请求百分比 
* 已传输数据量(字节) 
* 已传输数据量所占的百分比 

### <a name="data-in-user-agent"></a>“用户代理”中的数据 

* UserAgent 
* TotalRequests 
* 请求百分比 
* 已传输数据量(字节) 
* 已传输数据量所占的百分比 

### <a name="security-report"></a>安全报告 

此报告有七个表，都具有下面的相同字段。  

* 阻止的请求数 
* 允许的请求数 
* 记录的请求数 
* 重定向的请求数 
* OWASP 规则请求数 
* 自定义规则请求数 
* Bot 请求数 

这七个表分别为时间表、规则 ID 表、国家/地区表、IP 地址表、URL 表、主机名表、用户代理表。 

## <a name="next-steps"></a>后续步骤

了解 [Azure Front Door 标准版/高级版实时监视指标](how-to-monitor-metrics.md)。
