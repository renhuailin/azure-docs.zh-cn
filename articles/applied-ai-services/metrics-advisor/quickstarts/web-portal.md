---
title: 快速入门：指标顾问 Web 门户
titleSuffix: Azure Cognitive Services
description: 了解如何开始使用指标顾问 Web 门户。
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.custom:
- mode-portal
ms.openlocfilehash: 1ad7d21907e8be6de4c28881719cc31f31b02c7a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745957"
---
# <a name="quickstart-monitor-your-first-metric-by-using-the-web-portal"></a>快速入门：使用 Web 门户监视你的首个指标

预配 Azure 指标顾问的实例时，可以使用 API 和基于 Web 的工作区来处理服务。 可以使用基于 Web 的工作区充当快速开始使用该服务的简单方法。 它还提供了一种用于配置设置、自定义模型和执行根本原因分析的可视方式。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 [免费创建一个](https://azure.microsoft.com/free/cognitive-services)。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="创建指标顾问资源"  target="_blank">创建指标顾问资源</a>以部署指标顾问的实例。  

    
> [!TIP]
> * 部署指标顾问资源可能需要 10 到 30 分钟。 部署成功后，选择“转到资源”。
> * 如果想要使用 REST API 与服务进行交互，需要从创建的资源获取密钥和终结点。 可以在创建的资源的“密钥和终结点”选项卡中找到它们。


本文档使用 SQL 数据库作为创建首个监视器的示例。

## <a name="sign-in-to-your-workspace"></a>登录到工作区

创建资源后，使用 Active Directory 帐户登录到[指标顾问门户](https://go.microsoft.com/fwlink/?linkid=2143774)。 在登录页上，选择刚创建的“目录”、“订阅”和“工作区”，然后选择“开始使用”   。 若要使用时序数据，请从左侧菜单中选择“添加数据馈送”。

 
目前可以在每个可用区域创建一个指标顾问资源。 你可以随时在指标顾问门户中切换工作区。


## <a name="time-series-data"></a>时序数据

指标顾问为不同的数据源（例如 Azure SQL 数据库、Azure 数据资源管理器和 Azure 表存储）提供了连接器。 对于不同的连接器，连接数据的步骤是相似的，尽管某些配置参数可能会有所不同。 有关详细信息，请参阅[连接不同的数据源](../data-feeds-from-different-sources.md)。

本快速入门使用 SQL 数据库作为示例。 你还可以按相同的步骤引入自己的数据。


### <a name="data-schema-requirements-and-configuration"></a>数据架构要求和配置

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings-and-query"></a>配置连接设置和查询

通过连接到时序数据源来[添加数据馈送](../how-tos/onboard-your-data.md)。 首先选择以下参数：

* **源类型**：用于存储时序数据的数据源的类型。
* **粒度**：时序数据中连续数据点之间的间隔（例如每年、每月或每天）。 支持的最短时间间隔为 60 秒。
* **引入数据的时间 (UTC)** ：要引入的第一个时间戳的开始时间。 


:::image type="content" source="../media/connection-settings.png" alt-text="屏幕截图：显示了连接设置。" lightbox="../media/connection-settings.png":::


### <a name="load-data"></a>加载数据

输入连接和查询字符串后，选择“加载数据”。 指标顾问将检查加载数据的连接和权限，检查需要在查询中使用的必要参数，并检查来自数据源的列名。 

如果此步骤中出现错误：
1. 检查连接字符串是否有效。 
1. 确认是否有足够的权限，以及是否授予了引入辅助角色 IP 地址访问权限。
1. 检查查询中是否使用了所需的参数（`@IntervalStart` 和 `@IntervalEnd`）。 

### <a name="schema-configuration"></a>架构配置

通过运行查询加载数据后，选择适当的字段。


|选择  |说明  |说明  |
|---------|---------|---------|
|**Timestamp**     | 数据点的时间戳。 如果省略了时间戳，则指标顾问在引入数据点时使用时间戳。 对于每个数据馈送，最多可将一列指定为时间戳。        | 可选。 最多只能指定一列。       |
|度量      |  数据馈送中的数值。 对于每个数据馈送，可以指定多个度量值，但至少应选择一列作为度量值。        | 应至少指定一列。        |
|**维度**     | 分类值。 不同值的组合标识特定的一维时序。 示例包括国家/地区、语言和租户。 可以不选择列或选择任意数量的列作为维度。 如果要选择非字符串列作为维度，请小心以避免维度爆炸。 | 可选。        |
|**忽略**     | 忽略所选列。        | 可选。 对于支持使用查询获取数据的数据源，没有“忽略”选项。       |


:::image type="content" source="../media/schema-configuration.png" alt-text="屏幕截图：显示了架构配置。" lightbox="../media/schema-configuration.png":::

配置架构后，选择“验证架构”。 指标顾问执行以下检查：
- 查询数据的时间戳是否处于单个间隔。 
- 在一个指标间隔内，是否为同一维度组合返回了重复值。  

### <a name="automatic-roll-up-settings"></a>自动汇总设置

> [!IMPORTANT]
> 如果想要启用根本原因分析和其他诊断功能，请配置自动汇总设置。 启用分析后，不能更改自动汇总设置。

指标顾问可以在引入过程中自动对每个维度执行聚合。 然后，服务将生成可在根本原因分析和其他诊断功能中使用的层次结构。 有关详细信息，请参阅[自动汇总设置](../how-tos/onboard-your-data.md#automatic-roll-up-settings)。

为数据馈送提供自定义名称，该名称将显示在工作区中。 选择“提交”。  

## <a name="tune-detection-configuration"></a>优化检测配置

添加数据馈送后，指标顾问会尝试从指定的开始日期引入指标数据。 完全引入数据需要一段时间，可以通过选择数据馈送页顶部的“引入进度”来查看引入状态。 如果数据已引入，指标顾问将应用检测，并继续监视源以获取新数据。

应用检测时，选择数据馈送中列出的其中一项指标，找到“指标详细信息”页。 你可以在此执行以下操作： 
- 查看此指标下所有时序切片的可视化效果。
- 更新检测配置以满足预期的结果。
- 为检测到的异常设置通知。

:::image type="content" source="../media/metric-details.png" alt-text="屏幕截图：显示了指标详细信息。" lightbox="../media/metric-details.png":::

## <a name="view-diagnostic-insights"></a>查看诊断见解

优化检测配置后，你应该会发现，检测到的异常反映了数据中的实际异常。 指标顾问对多维度指标执行分析，以将根本原因定位到特定维度。 该服务还通过使用指标图功能来执行跨指标分析。 

若要查看诊断见解，请选择时序可视化效果上的红点。 这些红点代表检测到的异常。 随即将显示一个窗口，其中包含指向事件分析页的链接。 

:::image type="content" source="../media/incident-link.png" alt-text="屏幕截图：显示了事件链接。" lightbox="../media/incident-link.png":::

在事件分析页上，可以看到一组相关的异常和诊断见解。 以下部分将介绍诊断事件的主要步骤。

### <a name="check-the-summary-of-the-current-incident"></a>检查当前事件的摘要

可以在事件分析页的顶部找到摘要。 此摘要包括基本信息、操作和跟踪，以及分析的根本原因。 基本信息包括关系图“受影响最严重的系列”、“影响开始和结束时间”、“严重程度”和“包括的异常总数”。

分析的根本原因是自动分析所得的结果。 指标顾问将分析一个指标内按时序捕获的所有异常，这些异常在同一时间戳具有不同的维度值。 然后，服务执行关联，对与组相关的异常一起进行聚类分析，并生成根本原因相关的建议。

:::image type="content" source="../media/diagnostics/incident-summary.png" alt-text="屏幕截图：显示了事件诊断摘要。" lightbox="../media/diagnostics/incident-summary.png":::

基于这些信息，你已经可以对当前异常状态、事件的影响以及最有可能的根本原因有一个直观的看法。 然后，你可以立即采取行动来解决事件。 

### <a name="view-cross-dimension-diagnostic-insights"></a>查看跨维度诊断见解

还可使用“诊断树”功能，以整体方式获取有关同一指标内其他维度的异常状态的更多详细信息。

对于具有多个维度的指标，指标顾问将时序归类为一个层次结构（称为“诊断树”）。 例如，“收入”指标按两个维度监视：“区域”和“类别”。 你需要有一个聚合维度值，例如 `SUM`。 然后，`region = SUM` 和 `category = SUM` 的时序被归类为树中的根节点。 每当在 `SUM` 维度捕获到异常时，均可对其进行分析，找出对父级节点异常贡献最大的特定维度值。 选择每个节点，展开它以了解详细信息。

:::image type="content" source="../media/diagnostics/cross-dimension-diagnostic.png" alt-text="屏幕截图：显示了事件诊断跨维度视图。" lightbox="../media/diagnostics/cross-dimension-diagnostic.png":::

### <a name="view-cross-metrics-diagnostic-insights"></a>查看跨指标诊断见解

有时，很难通过检查单个指标的异常状态来分析问题，需要将多个指标关联在一起。 为此，配置一个指示指标之间的关系的“指标图”。 

通过使用上一部分中所述的跨维度诊断结果，可以确定根本原因限于特定的维度值。 然后使用指标图按分析的根本原因维度进行筛选，以检查其他指标的异常状态。

:::image type="content" source="../media/diagnostics/cross-metrics-analysis.png" alt-text="屏幕截图：显示了事件诊断跨指标分析。" lightbox="../media/diagnostics/cross-metrics-analysis.png":::

还可使用附加功能跨更多诊断见解进行透视。 这些功能可帮助你深度探索异常的维度，查看类似的异常，以及跨指标进行比较。 有关详细信息，请参阅[诊断事件](../how-tos/diagnose-an-incident.md)。 

## <a name="get-notified-when-new-anomalies-are-found"></a>发现新的异常时获取通知

如果想要在数据中检测到异常时收到警报，可以为一个或多个指标创建订阅。 指标顾问使用挂钩发送警报。 支持三种类型的挂钩：电子邮件挂钩、Webhook 和 Azure DevOps。 我们以 Webhook 为例。 

### <a name="create-a-web-hook"></a>创建 Webhook

在指标顾问中，可以使用 Webhook 以编程方式显示异常。 触发警报时，服务会调用用户提供的 API。 有关详细信息，请参阅[创建挂钩](../how-tos/alerts.md#create-a-hook)。 

### <a name="configure-alert-settings"></a>配置警报设置

创建挂钩后，警报设置将确定应发送的警报通知和发送方式。 可以为每个指标设置多个警报设置。 有两个重要的设置：警报对象，用于指定要包含的异常；筛选异常选项，用于定义要包含在警报中的异常 。 有关详细信息，请参阅[添加或编辑警报设置](../how-tos/alerts.md#add-or-edit-alert-settings)。


## <a name="next-steps"></a>后续步骤

- [将指标数据添加到指标顾问](../how-tos/onboard-your-data.md)
    - [管理数据馈送](../how-tos/manage-data-feeds.md)
    - [连接不同的数据源](../data-feeds-from-different-sources.md)
- [使用客户端库或 REST API 自定义解决方案](./rest-api-and-client-library.md)
- [配置指标并微调检测配置](../how-tos/configure-metrics.md)
