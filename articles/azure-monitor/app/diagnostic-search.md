---
title: 在 Azure Application Insights 中使用搜索 | Microsoft Docs
description: 搜索和筛选由 Web 应用发送的原始遥测数据。
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 020e602ec52c40c66439df09275f7d4936a57229
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112293070"
---
# <a name="using-search-in-application-insights"></a>使用 Application Insights 中的搜索

搜索是 [Application Insights](./app-insights-overview.md) 中的一项功能，可用于查找和浏览单个遥测项，例如页面视图、异常或 Web 请求。 此外，可以查看编写的日志跟踪和事件。

（若要对数据进行更复杂的查询，请使用 [Analytics](../logs/log-analytics-tutorial.md)。）

## <a name="where-do-you-see-search"></a>哪里可以看到“搜索”？

### <a name="in-the-azure-portal"></a>在 Azure 门户中

可以从应用程序的“Application Insights 概览”选项卡（位于顶栏中）或左侧的“调查”下打开诊断搜索。

![“搜索”选项卡](./media/diagnostic-search/view-custom-events.png)

转到事件类型的下拉菜单，查看遥测项列表 - 服务器请求、页面视图、编码的自定义事件，等等。 结果列表的上面是摘要图表，显示一段时间内的事件计数。

单击下拉菜单或“刷新”，获取新的事件。

### <a name="in-visual-studio"></a>在 Visual Studio 中

在 Visual Studio 中，也有一个 Application Insights 搜索窗口。 该窗口最适合显示由正在进行调试的应用程序生成的遥测事件， 但也可显示从 Azure 门户的已发布应用收集的事件。

在 Visual Studio 中打开“搜索”窗口：

![Visual Studio 打开 Application Insights 搜索](./media/diagnostic-search/32.png)

“搜索”窗口的功能与 Web 门户类似：

![Visual Studio Application Insights 搜索窗口](./media/diagnostic-search/34.png)

打开请求或页面视图即可使用“跟踪操作”选项卡。 “操作”是一系列与单个请求或页面视图关联的事件。 例如，依赖项调用、异常、跟踪日志和自定义事件可能是单个操作的一部分。 “跟踪操作”选项卡以图形方式显示这些事件相对于请求或页面视图的计时和持续时间。

## <a name="inspect-individual-items"></a>检查单个项

选择任一遥测项可查看关键字段和相关的项。

![单个依赖项请求的屏幕截图](./media/diagnostic-search/telemetry-item.png)

这会启动端到端的事务详细信息视图。

## <a name="filter-event-types"></a>筛选事件类型

打开事件类型的下拉菜单，选择要查看的事件类型。 （如果以后要还原筛选器，请单击“重置”。）

事件类型包括：

* **跟踪** - [诊断日志](./asp-net-trace-logs.md)，包括 TrackTrace、log4Net、NLog 和 System.Diagnostic.Trace 调用。
* **请求** - 服务器应用程序收到的 HTTP 请求，包括页面、脚本、图像、样式文件和数据。 这些事件用于创建请求和响应概述图表。
* **页面视图** - [由 Web 客户端发送的遥测数据](./javascript.md)，用于创建页面视图报告。
* **自定义事件** - 如果插入了对 TrackEvent() 的调用以便 [监视使用情况](./api-custom-events-metrics.md)，可以在此处搜索这些调用。
* **异常** - 未捕获到的 [服务器中的异常](./asp-net-exceptions.md)，以及使用 TrackException() 记录的异常。
* **依赖项** - [发自服务器应用程序的调用](./asp-net-dependencies.md)，调用对象为 REST API 或数据库等其他服务器，以及 [客户端代码](./javascript.md)中的 AJAX 调用。
* **可用性** - [可用性测试](./monitor-web-app-availability.md)的结果。

## <a name="filter-on-property-values"></a>按属性值筛选

可以按事件的属性值筛选事件。 可用的属性取决于所选的事件类型。 单击筛选器图标 ![筛选器图标](./media/diagnostic-search/filter-icon.png) 即可开始。

不选择特定属性的任何值与选择所有值的效果相同； 这会关闭根据该属性进行筛选的功能。

请注意，筛选器值右侧的计数显示当前筛选的集内有多少个出现的项。

## <a name="find-events-with-the-same-property"></a>查找具有相同属性的事件

若要查找具有相同属性值的所有项，请将其键入搜索栏中，或者在查看筛选器选项卡中的属性时单击复选框。

![单击筛选器选项卡中属性的复选框](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>搜索数据

> [!NOTE]
> 若要编写更复杂的查询，请在“搜索”边栏选项卡的顶部打开 [**日志（分析）**](../logs/log-analytics-tutorial.md)。
>

可以搜索任何属性值中的关键词。 如果已编写包含属性值的[自定义事件](./api-custom-events-metrics.md)，可使用此功能。

可以设置时间范围，因为搜索一小段时间内的值可以更快地返回结果。

![打开诊断搜索](./media/diagnostic-search/search-property.png)

搜索完整单词，而不搜索子字符串。 使用引号将特殊字符引起来。

| String |  未找到 | 已找到 |
| --- | --- | --- |
| HomeController.About |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|United States|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

下面是可以使用的搜索表达式：

| 示例查询 | 效果 |
| --- | --- |
| `apple` |在时间范围中查找字段包含“apple”一词的所有事件 |
| `apple AND banana` <br/>`apple banana` |查找同时包含这两个词的事件。 应使用大写“AND”，而不是“and”。 <br/>简格式。 |
| `apple OR banana` |查找包含任一词的事件。 使用“OR”而非“or”。 |
| `apple NOT banana` |查找包含一个词但不包含另一个词的事件。 |

## <a name="sampling"></a>采样

如果应用生成大量遥测（且使用的是 ASP.NET SDK 版本 2.0.0-beta3 或更高版本），自适应采样模块将通过仅发送具有代表性的事件部分来自动减少发送到门户的量。 但是，以组为单位选择或取消选择与同一请求相关的事件，以便可以在相关事件之间浏览。

[了解采样](./sampling.md)。

## <a name="create-work-item"></a>创建工作项

可以使用任何遥测项中的详细信息，在 GitHub 或 Azure DevOps 中创建 Bug。

通过单击任何遥测项转到端到端事务详细信息视图，然后选择“创建工作项”。 

![单击“新建工作项”、编辑字段，并单击“确定”。](./media/diagnostic-search/work-item.png)

首次执行此操作时，系统将要求配置指向 Azure DevOps 组织和项目的链接。

（还可以在“工作项”选项卡上配置链接。）

## <a name="send-more-telemetry-to-application-insights"></a>将更多遥测数据发送到 Application Insights

除了 Application Insights SDK 原本发送的遥测数据以外，可以：

* 从 [.NET](./asp-net-trace-logs.md) 或 [Java](java-2x-trace-logs.md) 中偏好的日志记录框架捕获日志跟踪。 也就是说，可以搜索日志跟踪并将其与页面视图、异常和其他事件相关联。
* [编写代码](./api-custom-events-metrics.md)用于发送自定义事件、页面视图和异常。

[了解如何向 Application Insights 发送日志和自定义的遥测数据](./asp-net-trace-logs.md)。

## <a name="q--a"></a><a name="questions"></a>问题解答

### <a name="how-much-data-is-retained"></a><a name="limits"></a>保留多少数据？

请参阅[限制摘要](./pricing.md#limits-summary)。

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>如何查看服务器请求中的 POST 数据？

我们不自动记录 POST 数据，但可以使用 [TrackTrace 或日志调用](./asp-net-trace-logs.md)。 POST 数据放在消息参数中。 无法像筛选属性一样筛选消息，但消息的大小限制更大。

## <a name="next-steps"></a><a name="add"></a>后续步骤

* [在 Analytics 中编写复杂查询](../logs/log-analytics-tutorial.md)
* [向 Application Insights 发送日志和自定义的遥测数据](./asp-net-trace-logs.md)
* [设置可用性和响应能力测试](./monitor-web-app-availability.md)
* [故障排除](../faq.yml)