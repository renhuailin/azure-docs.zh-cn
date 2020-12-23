---
title: 指标顾问常见问题解答
titleSuffix: Azure Cognitive Services
description: 有关指标顾问服务的常见问题解答。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420949"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>指标顾问常见问题解答

### <a name="what-is-the-cost-of-my-instance"></a>实例的成本是多少？

目前可在预览版中免费使用实例。

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>为什么无法创建资源？ “定价层”不可用且显示“已为此订阅创建 1个 S0”？

:::image type="content" source="media/pricing.png" alt-text="F0 资源已存在时显示的消息":::

在公共预览版中，只能在订阅下为每个区域创建一个指标顾问实例。

如果已在同一区域中使用相同的订阅创建了实例，可以尝试在不同的区域中或使用不同的订阅创建新实例。 也可以删除现有实例以创建新实例。

如果已删除现有实例，但仍出现错误，请在资源删除后等待约 20 分钟，然后再创建新实例。

## <a name="basic-concepts"></a>基本概念

### <a name="what-is-multi-dimensional-time-series-data"></a>什么是多维时序数据？

请参阅词汇表中的[多维指标](glossary.md#multi-dimensional-metric)定义。

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>指标顾问启动异常情况检测需要多少数据？

至少需要一个数据点才能触发异常情况检测。 但这并不能实现最佳的准确性。 此服务将使用在创建数据馈送时指定为“填充间隙”规则的值来假定以前的数据点的时间窗口。

建议在要检测的时间戳之前包含一些数据。
根据数据的粒度，建议的数据量如下变化。

| 粒度 | 建议的检测数据量 |
| ----------- | ------------------------------------- |
| 少于 5 分钟 | 4 天的数据 |
| 5 分钟至 1 天 | 28 天的数据 |
| 1 天以上至 31 天 | 4 年的数据 |
| 大于 31 天 | 48 年的数据 |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>为什么指标顾问无法从历史数据中检测到异常？

指标顾问旨在检测实时传送视频流数据。 此服务将回顾并对其运行异常情况检测的历史数据的最大长度受到限制。 这意味着只有在某个最早时间戳之后的数据点才会具有异常情况检测结果。 最早的时间戳取决于数据的粒度。

根据数据的粒度，将具有异常情况检测结果的历史数据的长度如下所示。

| 粒度 | 用于异常情况检测的历史数据的最大长度 |
| ----------- | ------------------------------------- |
| 少于 5 分钟 | 加入时间 - 13 小时 |
| 5 分钟至不到 1 小时 | 加入时间 - 4 天  |
| 1 小时至不到 1 天 | 加入时间 - 14 天  |
| 1 天 | 加入时间 - 28 天  |
| 1 天以上，不到 31 天 | 加入时间 - 2 年  |
| 大于 31 天 | 加入时间 - 24 年   |

### <a name="more-concepts-and-technical-terms"></a>更多概念和技术术语

另请参阅[词汇表](glossary.md)，以获取详细信息。

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>如何编写有效的查询以引入数据？  

要使指标顾问引入数据，你需要创建一个查询，使其返回单个时间戳对应数据的维度。 指标顾问将多次运行此查询，以从每个时间戳获取数据。 

请注意，在给定的时间戳位置，查询应为每个维度组合返回最多一条记录。 返回的所有记录必须具有相同的时间戳。 查询不应返回重复的记录。

例如，假设你为每日指标创建了以下查询： 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

确保为时序使用正确的粒度。 对于每小时指标，将使用： 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

请注意，这些查询仅返回单个时间戳位置的数据，并且包含指标顾问引入的所有维度组合。 

:::image type="content" source="media/query-result.png" alt-text="带有一个时间戳的查询结果" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>如何将峰值和低谷检测为异常？

如果预定义了硬阈值，则实际上可以在[异常情况检测配置](how-tos/configure-metrics.md#anomaly-detection-methods)中手动设置“硬阈值”。
如果没有阈值，则可以使用由 AI 提供支持的“智能检测”。 有关详细信息，请参阅[优化检测配置](how-tos/configure-metrics.md#tune-the-detecting-configuration)。

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>如何将常规（周期性）模式的不一致检测为异常？

“智能检测”能够了解数据模式，包括周期性模式。 然后，它将那些不符合常规模式的数据点检测为异常。 有关详细信息，请参阅[优化检测配置](how-tos/configure-metrics.md#tune-the-detecting-configuration)。

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>如何将平线检测为异常？

如果数据通常极不稳定且波动很大，你希望在数据变得十分稳定甚至变成一条平线时收到警告，则可以将“更改阈值”配置为在更改极小时检测此类数据点。
有关详细信息，请参阅[异常情况检测配置](how-tos/configure-metrics.md#anomaly-detection-methods)。

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>如何通过电子邮件设置电子邮件设置并启用警报？

1.  具有订阅管理员或资源组管理员权限的用户需要导航到在 Azure 门户中创建的指标顾问资源，然后选择 " **访问控制 (" IAM)** "选项卡。 
2.  选择 " **添加角色分配** "
3.  选择 " **认知服务指标顾问管理员** " 角色，按下图所示选择帐户。
4.  单击 " **保存** " 按钮，已成功地添加为指标顾问资源的管理员。 请注意，所有上述操作都需要由订阅管理员或资源组管理员执行。 

:::image type="content" source="media/access-control.png" alt-text="&quot;访问控制&quot; (IAM) &quot;菜单&quot; 页，其中包含 &quot;添加角色分配&quot; 选项，后跟一个带有 &quot;认知服务指标&quot; 管理员管理员访问角色的 &quot;分配对选定用户的访问权限&quot; 框，后跟所选 UI 的 &quot;保存&quot; 按钮以演示搜索用户和添加特定级别的访问权限的步骤。" lightbox="media/access-control.png":::


5.  传播权限可能需要长达一分钟的时间。 然后，选择 "指标顾问" 工作区，并选择左侧导航面板中的 " **电子邮件设置** " 选项。 填写所需的项，尤其是与 SMTP 相关的信息。 
6.  选择 " **保存** "，并将其设置为电子邮件配置。 你可以创建新的挂钩并订阅指标异常，以获得近乎实时的警报。 

## <a name="advanced-concepts"></a>高级概念

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>指标顾问如何为多维指标构建事件树？

指标可以按维度划分为多个时序。 例如，为团队拥有的所有服务监视指标 `Response latency`。 可使用 `Service` 类别丰富指标的维度，因此我们按 `Service1` 和 `Service2` 等划分 `Response latency`。 每个服务都可以部署在多个数据中心的不同计算机上，因此可以按 `Machine` 和 `Data center` 进一步划分指标。

|服务| 数据中心| 计算机  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

从 `Response latency` 总数开始，我们可以按 `Service`、`Data center` 和 `Machine` 向下钻取指标。 但是，对于服务所有者而言，使用路径 `Service` -> `Data center` -> `Machine` 可能更有意义；而对于基础结构工程师而言，使用路径 `Data Center` -> `Machine` -> `Service` 可能更有意义。 这完全取决于用户的个人业务需求。 

在指标顾问中，用户可以指定其要从分层拓扑的一个节点向下钻取或汇总的任何路径。 更准确地说，分层拓扑是有向无环图而非树结构。 完整的分层拓扑由所有潜在的维度组合组成，如下所示： 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="分层拓扑图，由多个相互连接的顶点和边组成，这些顶点和边的维度标注为 S、DC 和 M，其对应数字范围为 1 到 6" lightbox="media/dimension-combinations-view.png":::

从理论上讲，如果维度 `Service` 具有 `Ls` 个非重复值，维度 `Data center` 具有 `Ldc` 个非重复值，维度 `Machine` 具有 `Lm` 个非重复值，则分层拓扑中可能有 `(Ls + 1) * (Ldc + 1) * (Lm + 1)` 个维度组合。 

但通常并非所有维度组合都是有效的，这可能会显着降低复杂性。 目前，如果用户自己聚合指标，则不限制维度的数量。 如果需要使用指标顾问提供的汇总功能，则维度的数量不应超过 6。 但是，我们将按指标维度扩展的时序数量限制为小于 10,000。

诊断页面中的“事件树”工具仅显示已检测到异常的节点，而不显示整个拓扑。 这是为了帮助你专注于当前问题。 它也可能会不显示指标内的所有异常，而是会基于贡献显示最常见的异常。 这样，我们可以快速找出异常数据的影响、范围和传播路径。 这大大减少了需要重点关注的异常数量，并帮助用户了解并找到其关键问题。 
 
例如，当 `Service = S2 | Data Center = DC2 | Machine = M5` 上发生异常时，异常的偏差会影响也已检测到异常的父节点 `Service= S2`，但异常不会影响 `DC2` 上的整个数据中心以及 `M5` 上的所有服务。 将按照以下屏幕截图中的方式构建事件树，最常见的异常是在 `Service = S2` 上捕获的，根本原因可以按两条通向 `Service = S2 | Data Center = DC2 | Machine = M5` 的路径进行分析。

 :::image type="content" source="media/root-cause-paths.png" alt-text="5个标记的顶点，其中的两个不同路径由边缘和一个标记为 S2 的常见节点连接。最常见的异常在服务 = S2 上捕获，根本原因可以通过两个路径来进行分析，这两个路径会导致服务 = S2 |数据中心 = DC2 |计算机 = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>后续步骤
- [指标顾问概述](overview.md)
- [使用 Web 门户](quickstarts/web-portal.md)