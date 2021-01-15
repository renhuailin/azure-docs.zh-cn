---
title: Azure Monitor 指标指标聚合和显示说明
description: 有关如何在 Azure Monitor 中聚合度量值的详细信息
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 01/12/2020
ms.subservice: metrics
ms.openlocfilehash: 79728e53c1d53a8a4463fc0bd1ddee5db89fc013
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234691"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Azure Monitor 指标指标聚合和显示说明

本文介绍了 Azure Monitor [平台指标](data-platform.md) 和 [自定义指标](metrics-custom-overview.md)的 Azure Monitor 时序数据库中的指标聚合。 本文还适用于标准 [Application Insights 指标](../app/app-insights-overview.md)。 

这是一个复杂的主题，无需理解本文中的所有信息即可有效地使用 Azure Monitor 指标。

## <a name="overview-and-terms"></a>概述和条款

将指标添加到图表时，指标资源管理器会自动预先选择其默认聚合。 默认情况下，这在基本方案中是有意义的，但你可以使用不同的聚合获取有关指标的更多见解。 查看图表上的不同聚合时需要了解指标资源管理器处理它们的方式。

首先明确定义几个术语：

- **指标值** –为特定资源收集的单个度量值。
- **时间段** -一般时间段。
- **时间间隔** –收集两个指标值之间的时间段。 
- **时间范围** –图表中显示的时间段。 典型默认值为24小时。 只提供特定范围。 
- **时间粒度** 或 **时间粒度** –用于聚合值以允许在图表上显示的时间段。 只提供特定范围。 当前最小值为1分钟。 时间粒度值应小于所选时间范围才能有用，否则，只会为整个图表显示一个值。 
- **聚合类型** –从多个指标值计算得出的一种统计信息。  
- **聚合** –采用多个输入值，然后使用这些值通过聚合类型定义的规则生成单个输出值的过程。 例如，使用多个值的平均值。  

度量值是一系列按固定时间间隔捕获的指标值。 绘制图表时，所选指标的值将在时间粒度上单独聚合 (也称为时间粒度) 。 使用 [指标资源管理器时间选择器面板](metrics-getting-started.md#select-a-time-range)选择时间粒度的大小。 如果未做出显式选择，将根据当前所选时间范围自动选择时间粒度。 选择后，在每个时间间隔内捕获的指标值将聚合并置于图表上，每个间隔一个点。

## <a name="aggregation-types"></a>聚合类型 

指标资源管理器中提供五种基本的聚合类型。 指标资源管理器将隐藏不相关且不能用于给定指标的聚合。 

- **Sum** –聚合间隔内捕获的所有值的总和。 有时称为合计聚合。
- **Count** –通过聚合间隔捕获的度量值的数量。 计数不会查看度量值，而只查看记录数。 
- **Average** –聚合间隔内捕获的指标值的平均值。 对于大多数指标，此值是 Sum/Count。 
- **最小** 值–聚合间隔内捕获的最小值。
- **Max** –聚合间隔内捕获的最大值。

例如，假设某个图表在过去24小时的时间范围内使用 **SUM** 聚合显示了 VM 的 **网络总体** 指标。 如以下屏幕截图所示，可以从图表的右上方更改时间范围和粒度。

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="显示时间范围和时间粒度选取器的屏幕截图" border="true":::

对于时间粒度 = 30 分钟，时间范围 = 24 小时：

- 该图表是从48数据点中提取的。 这是每小时24小时 x 2 数据点 (60 分钟/30 分钟) 聚合1分钟数据点。 
- 折线图在图表绘图区中连接48点。 
- 每个点都表示在每个相关的30分钟时间段内发出的所有网络输出字节数之和。 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="显示折线图上的数据的屏幕截图，其时间范围设置为24小时，时间粒度为30分钟" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*单击此部分中的图像可查看更大的版本。*

如果将时间粒度切换到15分钟，则会从96个聚合数据点绘制图表。 也就是说，60分钟/15 分钟 = 4 数据点 per 小时 x 24 小时。

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="显示折线图上的数据的屏幕截图，其时间范围设置为24小时，时间间隔为15分钟" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

对于5分钟的时间粒度，你将获得 24 x (60/5) = 288 点。 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="显示折线图上的数据的屏幕截图，其时间范围设置为24小时，时间粒度为5分钟" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

如果时间粒度为1分钟 (图表上可能的最小值) ，则获取 24 x 60/1 = 1440 点。 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="显示折线图上的数据的屏幕截图，其时间范围设置为24小时，时间粒度为1分钟" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

如上图所示，这些就的图表外观有所不同。  请注意，此 VM 相对于时间窗口的其余部分，在较短的时间段内有很多输出。  

使用 "时间间隔"，您可以调整图表上的 "信噪" 比例。 聚合越大，消除了噪音并平滑了峰值。  请注意，底部1分钟的图表上的变体以及它们在你开始更高粒度值时的平滑方式。 

将此数据发送到其他系统（例如警报）时，此平滑行为非常重要。 通常，您通常不希望在超过90% 的 CPU 时间内收到极短的高峰。 但如果 CPU 在5分钟内保持为90%，这可能很重要。 如果在 CPU (或任何指标) 上设置警报规则，则时间粒度越大，就会减少收到的虚假警报数量。 

务必确定工作负荷的 "正常"，以了解哪种时间间隔最佳。 这是 [动态警报](alerts-dynamic-thresholds.md)的优点之一，这是一个不属于此的其他主题。  

## <a name="how-the-system-collects-metrics"></a>系统如何收集指标

数据收集因指标而异。 有两种类型的收集期。

### <a name="measurement-collection-frequency"></a>度量收集频率 

- **常规** -以一致的时间间隔收集指标，该时间间隔不会变化。

- **基于活动** -根据特定类型的事务发生的时间收集指标。 每个事务都有一个指标条目和一个时间戳。 它们不会定期收集，因此，在给定的时间段内有不同的记录数。 

### <a name="granularity"></a>粒度

最小时间间隔为1分钟，但基础系统可以更快地捕获数据，具体取决于指标。 例如，每隔15秒按固定间隔跟踪 CPU 百分比。 由于 HTTP 失败作为事务进行跟踪，因此它们可以轻松地超过一分钟。 其他指标（例如 SQL 存储）每隔20分钟捕获一次。 此选择由单个资源提供程序和类型决定。 大多数尝试提供可能的最小间隔。

### <a name="dimensions-splitting-and-filtering"></a>维度、拆分和筛选

为每个单独的资源捕获度量值。 不过，收集、存储和存储度量值的级别可能会有所不同。 此级别由 **指标维度** 中提供的其他指标来表示。 每个单独的资源提供程序都可用于定义其收集的数据的详细程度。 Azure Monitor 仅定义应显示和存储此类详细信息的方式。 

在指标资源管理器中绘制指标图表时，可以选择 "按维度拆分" 图表。  拆分图表意味着要查看基础数据以获取更多详细信息，并在指标资源管理器中查看数据图表或筛选。

例如， [ApiManagement/服务](metrics-supported.md#microsoftapimanagementservice) 的 *位置* 作为许多指标的维度。 

- **容量** 是一个这样的指标。 如果具有 *位置* 维度，则意味着基础系统将存储每个位置的容量记录，而不只是一个用于聚合量的指标记录。 然后，您可以在度量值图表中检索或拆分该信息。  

- 查看 **网关请求的总持续时间**，有2个维度 *位置* 和 *主机名*，并再次使你知道它所处的持续时间和主机名。  

- 一个更灵活的指标（ **请求**）有7个不同的维度。 
 
有关每个指标和可用维度的详细信息，请查看支持的 Azure Monitor [指标](metrics-supported.md) 文章。 此外，每个资源提供程序和类型的文档可能会提供有关维度和度量值的其他信息。

可以结合使用拆分和筛选来深入了解问题。 下面是一个图形示例，显示了资源组中一组 Vm 的 *平均磁盘写入字节数* 。 我们已经汇总了所有具有此指标的 Vm，但我们可能想要了解哪些 Vm 实际上负责早晨的高峰。 它们是否是相同的计算机？ 涉及多少台计算机？  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="显示 Contoso 酒店资源组中所有虚拟机的总磁盘写入字节数的屏幕截图" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*单击此部分中的图像可查看更大的版本。*

当我们应用拆分时，可以看到基础数据，但这有点混乱。 将有20个 Vm 聚合到上图中。 在这种情况下，我们使用鼠标将鼠标悬停在早晨上的大峰上，告诉我们 DCVM11 是原因。 但很难看到与该 VM 关联的数据的其余部分，因为其他 Vm 会打乱图表。 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="显示按虚拟机名称拆分的 Contoso 酒店资源组中所有虚拟机的磁盘写入字节数的屏幕截图" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

使用筛选功能可以清理图表，以查看实际发生的情况。 可以选中或取消选中要查看的 Vm。 请注意虚线。 后面的部分将介绍这些内容。

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="显示 Contoso 酒店资源组中所有虚拟机的磁盘写入字节数的屏幕截图，按虚拟机名称进行拆分和筛选" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

有关如何在指标资源管理器图表中显示拆分维度数据的详细信息，请参阅 [指标资源管理器的高级功能-筛选器和拆分](metrics-charts.md#apply-filters-to-charts)。

### <a name="null-and-zero-values"></a>NULL 和零值

当系统需要来自资源的指标数据但未接收到该数据时，它将记录一个 NULL 值。  NULL 值不同于零值，这在计算聚合和图表时变得很重要。 空值不计为有效的度量值。 

空值在不同的图表上以不同方式显示。 散点图跳过在图表上显示一个点。 条形图跳过显示条形图。 在折线图中，NULL 可以显示为 [点线或虚线](metrics-troubleshoot.md#chart-shows-dashed-line) ，如上一部分屏幕截图中所示。 计算包含 Null 值的平均值时，要取的数据点越少。  此行为有时会导致图表上出现意外的删除值，尽管该值通常小于转换为零并用作有效的时间。  

当未收到任何数据时，[自定义指标](metrics-custom-overview.md)始终使用 null。 使用 [平台指标](data-platform.md)时，每个资源提供程序都会根据给定指标的最大含义，决定是使用零还是 null。

Azure Monitor 警报使用资源提供程序写入指标数据库的值，因此了解资源提供程序如何通过先查看数据来处理 Null，这一点很重要。

## <a name="how-aggregation-works"></a>聚合的工作方式

前面系统中的度量值图表显示了不同类型的聚合数据。 系统将对数据进行预先聚合，以便在不进行大量重复计算的情况下，请求的图表可以更快地显示。  

在此示例中：

- 我们正在收集称为 **HTTP 失败** 的 **虚拟** 事务性指标 
- *服务器* 是 **HTTP 失败** 指标的维度。
- 我们有3个服务器-服务器 A、B 和 C。

为了简化说明，我们将仅从 SUM 聚合类型开始。 

### <a name="sub-minute-to-1-minute-aggregation"></a>子分钟到1分钟的聚合

首先收集原始指标数据并将其存储在 Azure Monitor 度量值数据库中。 在这种情况下，每个服务器都有一个与时间戳存储的事务记录，因为 *服务器* 是一个维度。 假设你可以查看为客户的最小时间段为1分钟，则这些时间戳首先聚合为每个单独服务器的1分钟指标值。  服务器 B 的聚合过程显示在下图中。 服务器 A 和 C 以相同的方式进行，并且具有不同的数据。  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="显示一分钟聚合的秒数事务条目的屏幕截图。 " border="false":::

生成的1分钟聚合值作为新条目存储在指标数据库中，以便可以收集这些值以便以后计算。 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="显示跨服务器维度的多个1分钟聚合条目的屏幕截图。分别显示 Server A、B 和 C" border="false":::

### <a name="dimension-aggregation"></a>维度聚合

然后按维度折叠1分钟计算，并再次将其存储为单个记录。   在这种情况下，所有单个服务器的所有数据都将聚合为1分钟的间隔指标，并存储在指标数据库中，以便在以后的聚合中使用。

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="显示 Server A、B 和 C 的多个1分钟聚合条目的屏幕截图，它们聚合到1分钟的所有服务器中条目" border="false":::

为清楚起见，下表显示了聚合方法。

| 周期   | 服务器 A | 服务器 B | 服务器 C | Sum (A + B + C) |
| -------- | -------- | -------- | -------- | --------   |  
| 分钟1 | 1        | 1        | 1        | 3          |
| 分钟2 | 0        | 5        | 1        | 6          |
| 分钟3 | 0        | 5        | 1        | 6          |
| 分钟4 | 2        | 3        | 4        | 9          |
| 分钟5 | 1        | 0        | 3        | 4          |
| 分钟6 | 1        | 0        | 4        | 5          |
| 分钟7 | 1        | 2        | 4        | 7          |
| 分钟8 | 0        | 1        | 0        | 1          |
| 分钟9 | 1        | 1        | 4        | 6          |
| 分钟10| 2        | 1        | 0        | 3          |

上面只显示了一个维度，但对于指标支持的 **所有维度** ，会发生此相同的聚合和存储过程。

- 将值收集到由该维度聚合的1分钟。 存储这些值。 
- 将维度折叠为1分钟的聚合总和。 存储这些值。 

接下来介绍称为网络适配器的 HTTP 失败的另一个维度。 假设每个服务器的适配器数量各不相同。

- 服务器 A 具有1个适配器
- 服务器 B 有2个适配器
- 服务器 C 具有3个适配器

我们要单独收集以下事务的数据。 它们将标记为：

- 一段时间
- 值
- 事务来自的服务器
- 事务来自的适配器

然后，将每个 subminute 流聚合为1分钟的时间序列值，并将其存储在 Azure Monitor 指标数据库中：

- 服务器 A，适配器1
- 服务器 B，适配器1
- 服务器 B，适配器2
- 服务器 C，适配器1
- 服务器 C，适配器2
- 服务器 C，适配器3

此外，还将存储以下折叠聚合：

- 服务器 A，适配器 1 (因为没有要折叠的内容，则会再次存储) 
- 服务器 B，适配器 1 + 2
- 服务器 C，适配器 1 + 2 + 3
- 全部为服务器，所有适配器

这表明具有大量维度的指标具有更多的聚合。 了解所有排列并不重要，只需了解原因。 系统需要同时存储单独的数据和聚合数据，以便快速检索任何图表上的访问权限。 系统会根据你选择的内容，选择最相关的存储聚合或基础原始数据。 

### <a name="aggregation-with-no-dimensions"></a>无维度的聚合

由于此指标具有维度 *服务器*，因此你可以通过拆分和筛选来访问上面的服务器 a、B 和 C 的基础数据，如本文前面所述。 如果度量值没有 *服务器* 作为维度，则您的客户只能访问关系图上以黑色显示的聚合1分钟总计。 也就是说，3、6、6、9等的值。系统也不会执行基础工作来聚合拆分值，它永远不会在指标资源管理器中使用它们，也不会通过指标 REST API 将其发送出去。 

## <a name="viewing-time-granularities-above-1-minute"></a>查看时间粒度超过1分钟

如果您要求更高粒度的指标，系统将使用1分钟的聚合总和来计算较大时间粒度的总和。  下面的虚线显示2分钟和5分钟时间的合计方法粒度。 同样，为了简单起见，我们只显示了 SUM 聚合类型。

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="显示跨服务器维度的多个1分钟聚合条目聚合到2分钟和5分钟时间段的屏幕截图。" border="false":::

对于2分钟的时间粒度。

| 周期       | 运算       
| -------------|-------------|  
| 分钟 1 & 2 |  (3 + 6) = 9 |
| 分钟 3 & 4 |  (6 + 9) = 15|
| 分钟 4 & 5 |  (4 + 5) = 9 |
| 分钟 6 & 7 |  (7 + 1) = 8 |
| & 9 的第8分钟 |  (6 + 3) = 9 |

适用于5分钟的时间粒度。

| 周期              |            运算        |
|---------------------|------------------------|  
| 1到5分钟  | 3 + 6 + 6 + 9 + 4 = 28 |
| 分钟6到10 | 5 + 7 + 1 + 6 + 3 = 22 |

系统使用存储的聚合数据，从而提供最佳性能。 

下面是上述1分钟聚合过程的更大关系图，其中的一些箭头旨在提高可读性。

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="显示前3个屏幕截图合并的屏幕截图。1分钟、2分钟和5分钟间隔内聚合的跨服务器维度的多个每分钟聚合条目。分别显示 Server A、B 和 C" border="false":::

## <a name="more-complex-example"></a>更复杂的示例

下面是一个更大的示例，它使用称为 HTTP 响应时间（以毫秒为单位）的虚构指标值。  这里我们介绍了其他级别的复杂性。

1. 显示 Sum、Count、Min 和 Max 的聚合，以及平均值的计算。
2. 我们会显示空值以及这些值如何影响计算。 

请看下面的示例。 框和箭头显示了如何聚合和计算这些值的示例。 

对于求和、计数、最小值和最大值，上一部分中所述的相同的1分钟 preaggregation 进程。  不过，Average 不是预先聚合的。 它使用聚合数据进行重新计算，以避免计算错误。
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="显示聚合和计算总和、计数、最小值、最大值和平均1分钟到10分钟的复杂示例的屏幕截图。" border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

请考虑上面突出显示的1分钟聚合的分钟6。 此分钟是指服务器 B 脱机并停止报告数据的点，可能是由于重新启动引起的。 

在上面的第6分钟，计算的1分钟聚合类型为： 

| 聚合类型 | Value        | 注释 |
|------------------|--------------|-------|
| Sum              | 53 + 20 = 73 | |
| 计数            | 2            | 显示 Null 的效果。  如果服务器已联机，则值为3。  |
| 最低要求          | 20           | |
| 最大值          | 53           | |
| 平均值          | 73/2       | Always 除以计数。 它永远不会存储并始终使用该粒度的聚合数字重新计算每个时间粒度。 请注意上面突出显示的 "5 分钟" 和 "10 分钟时间" 的重新计算粒度。 |

红色文本颜色指示可能被视为处于正常范围之外的值，并显示在时间粒度出现 (或失败时，它们是如何传播到) 的。 请注意，" *最小值* " 和 " *最大* 值" 指示存在基础异常，而 *平均* 和 *总和* 会在您的时间粒度下降时丢失该信息。  

您还可以看到，空值的计算结果比使用零时的平均值更好。  

> [!NOTE] 
> 虽然在此示例中不是这种情况，但在始终使用值1捕获度量值的情况下， *Count* 等于 *Sum* 。 当指标跟踪事务事件的发生次数时，这种情况很常见-例如，本文前面的示例中提到的 HTTP 失败数。

## <a name="next-steps"></a>后续步骤

- [指标资源管理器入门](metrics-getting-started.md)
- [高级指标资源管理器](metrics-charts.md)
