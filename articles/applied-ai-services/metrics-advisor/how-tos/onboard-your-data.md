---
title: 将数据馈送载入指标顾问
titleSuffix: Azure Cognitive Services
description: 如何开始将数据馈送载入指标顾问。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: mbullwin
ms.openlocfilehash: 403fd0b41d2fc9e0db56aaa4eadab3c27e5d1991
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341119"
---
# <a name="how-to-onboard-your-metric-data-to-metrics-advisor"></a>操作说明：将指标数据载入指标顾问

请参阅本文，了解如何将数据载入指标顾问。 

## <a name="data-schema-requirements-and-configuration"></a>数据架构要求和配置

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]
如果不确定某些术语，请参阅[术语表](../glossary.md)。

## <a name="avoid-loading-partial-data"></a>避免加载部分数据

加载部分数据是由于指标顾问中存储的数据与数据源中存储的数据不一致导致的。 在指标顾问完成数据拉取后，当数据源更新时，可能会发生这种情况。 指标顾问只从给定数据源拉取一次数据。

例如，如果某个指标已载入到指标顾问中用于监视，则会发生这种情况。 指标顾问成功获取时间戳 A 处的指标数据，并对其执行异常情况检测。 但是，如果该特定时间戳 A 的指标数据在引入数据后已刷新， 则不会检索新的数据值。

你可以尝试[回填](manage-data-feeds.md#backfill-your-data-feed)历史数据（稍后介绍）以减少不一致情况，但如果已触发这些时间点的警报，则不会触发新的异常警报。 此过程可能会向系统添加更多工作负荷，并且不是自动的。

为了避免加载部分数据，建议采用两种方法：

* 在单个事务中生成数据：

    确保同一时间戳的所有维度组合的指标值在单个事务中存储到数据源。 在上面的示例中，请等待所有数据源中的数据准备就绪，然后在单个事务中将其加载到指标顾问中。 指标顾问可以定期轮询数据馈送，直至成功检索到数据（或检索到部分数据）。

* 通过为“引入时间偏移”参数设置适当的值来延迟数据引入：

    为数据馈送设置“引入时间偏移”参数以延迟引入，直到数据完全准备就绪。 对于不支持事务的某些数据源（例如 Azure 表存储），这可能很有用。 有关详细信息，请参阅[高级设置](manage-data-feeds.md#advanced-settings)。

## <a name="start-by-adding-a-data-feed"></a>从添加数据馈送开始

登录到指标顾问门户并选择你的工作区后，请单击“开始”。 然后，在工作区的主页上，从左侧菜单中单击“添加数据馈送”。

### <a name="add-connection-settings"></a>添加连接设置

#### <a name="1-basic-settings"></a>1. 基本设置
接下来，你将输入一组用于连接时序数据源的参数。 
* **源类型**：用于存储时序数据的数据源的类型。
* **粒度**：时序数据中连续数据点之间的间隔。 指标顾问目前支持：“每年”、“每月”、“每周”、“每日”、“每小时”和“自定义”。 自定义选项支持的最小间隔为 300 秒。
  * **秒**：当“granularityName”设置为“Customize”时的秒数。
* **引入数据的时间 (UTC)** ：数据引入的基线开始时间。 `startOffsetInSeconds` 通常用于添加偏移量以帮助实现数据一致性。

#### <a name="2-specify-connection-string"></a>2. 指定连接字符串
接下来，你需要指定数据源的连接信息。 有关其他字段和连接不同类型的数据源的详细信息，请参阅[操作说明：连接不同的数据源](../data-feeds-from-different-sources.md)。

#### <a name="3-specify-query-for-a-single-timestamp"></a>3. 指定单个时间戳的查询
<!-- Next, you'll need to specify a query to convert the data into the required schema, see [how to write a valid query](../tutorials/write-a-valid-query.md) for more information.  -->

有关不同类型的数据源的详细信息，请参阅[操作说明：连接不同的数据源](../data-feeds-from-different-sources.md)。

### <a name="load-data"></a>加载数据

输入连接字符串和查询字符串后，选择“加载数据”。 在此操作中，指标顾问将检查连接和加载数据的权限，检查需要在查询中使用的必要参数（@IntervalStart 和 @IntervalEnd），并检查来自数据源的列名。 

如果此步骤中出现错误：
1. 首先检查连接字符串是否有效。 
2. 接着检查是否有足够的权限，以及是否授予了引入辅助角色 IP 地址访问权限。
3. 然后检查查询中是否使用了所需的参数（@IntervalStart 和 @IntervalEnd）。 


### <a name="schema-configuration"></a>架构配置

加载数据架构后，选择适当的字段。

如果省略了某个时间点的时间戳，则指标顾问会改用引入数据点时的时间戳。 对于每个数据馈送，最多可将一列指定为时间戳。 如果你收到一条消息，指出不能将某个列指定为时间戳，请检查你的查询或数据源，并检查查询结果中是否有多个时间戳，而不仅仅是检查预览数据。 执行数据引入操作时，指标顾问每次只能使用给定源中的一个时序数据区块（例如一天、一小时 - 具体取决于粒度）。

|选择  |说明  |注释  |
|---------|---------|---------|
| **显示名称** | 取代原始列名称显示在工作区中的名称。 | 可选。|
|**Timestamp**     | 数据点的时间戳。 如果省略，则指标顾问将在数据点引入时使用时间戳。 对于每个数据馈送，最多可将一列指定为时间戳。        | 可选。 最多只能指定一列。 如果收到“无法将列指定为时间戳”错误，请检查查询或数据源中是否存在重复的时间戳。      |
|度量      |  数据馈送中的数值。 对于每个数据馈送，可以指定多个度量值，但至少应选择一列作为度量值。        | 应至少指定一列。        |
|**维度**     | 分类值。 不同值的组合标识特定的一维时序，例如：国家/地区、语言、租户。 你可以选择零个或零个以上的列作为维度。 注意：选择非字符串列作为维度时要格外小心。 | 可选。        |
|**忽略**     | 忽略所选列。        | 可选。 对于支持使用查询获取数据的数据源，没有“忽略”选项。       |

如果希望忽略列，建议更新查询或数据源以排除这些列。 还可以通过以下方式来忽略列：使用“忽略列”，然后对特定列使用“忽略” 。 如果某个列应当为维度，但被错误地设置为“已忽略”，则指标顾问最终可能会引入部分数据。 例如，假设来自查询的数据如下所示：

| 行 ID | 时间戳 | Country | 语言 | 收入 |
| --- | --- | --- | --- | --- |
| 1 | 2019/11/10 | 中国 | ZH-CN | 10000 |
| 2 | 2019/11/10 | 中国 | EN-US | 1000 |
| 3 | 2019/11/10 | US | ZH-CN | 12000 |
| 4 | 2019/11/11 | US | EN-US | 23000 |
| ... | ...| ... | ... | ... |

如果“国家/地区”是一个维度并且“语言”设置为“忽略”，则第一行和第二行的时间戳将具有相同的维度  。 指标顾问将任意使用两行中的一个值。 在这种情况下，指标顾问不会聚合行。

配置架构后，选择“验证架构”。 在此操作中，指标顾问将执行以下检查：
- 查询数据的时间戳是否处于单个间隔。 
- 在一个指标间隔内是否有相同维度组合返回的重复值。  

### <a name="automatic-roll-up-settings"></a>自动汇总设置

> [!IMPORTANT]
> 如果想要启用根本原因分析和其他诊断功能，则需要配置“自动汇总设置”。 启用后，将无法更改自动汇总设置。

指标顾问可以在引入过程中自动对每个维度执行聚合（例如 SUM、MAX、MIN），然后生成一个用于根本原因分析和其他诊断功能的层次结构。 

请考虑下列情形：

* “我不需要包含数据的汇总分析。”

    你不需要使用指标顾问汇总。

* “我的数据已汇总，并且维度值由以下内容表示：NULL 或空（默认值）、仅 NULL、其他。”

    此选项意味着指标顾问无需汇总数据，因为行已经求和。 例如，如果选择“仅 NULL”，则会将以下示例中的第二个数据行视为所有国家/地区和语言 EN-US 的聚合；但是，“国家/地区”为空值的第四个数据行会被视为一个普通行，这可能表示数据不完整。
    
    | Country | 语言 | 收入 |
    |---------|----------|--------|
    | 中国   | ZH-CN    | 10000  |
    | (NULL)  | EN-US    | 999999 |
    | US      | EN-US    | 12000  |
    |         | EN-US    | 5000   |

* “我需要指标顾问通过计算 Sum/Max/Min/Avg/Count 来汇总数据，并用 {some string} 表示。”

    某些数据源（例如 Cosmos DB 或 Azure Blob 存储）不支持 group by 或 cube 之类的特定计算。 指标顾问提供了汇总选项，用于在引入过程中自动生成多维数据集。
    此选项意味着你需要指标顾问使用你选择的算法来计算汇总，并使用指定的字符串在指标顾问中表示汇总。 这不会更改数据源中的任何数据。
    例如，假设你有一组时序，它们代表维度（国家/地区、区域）的销售指标。 对于给定的时间戳，它可能如下所示：


    | Country       | 区域           | Sales |
    |---------------|------------------|-------|
    | Canada        | Alberta          | 100   |
    | Canada        | British Columbia | 500   |
    | United States | Montana          | 100   |


    在使用“求和”启用自动汇总后，指标顾问会计算维度组合，并在引入数据期间对指标求和。 结果可能是：

    | Country       | 区域           | Sales |
    | ------------ | --------------- | ---- |
    | Canada        | Alberta          | 100   |
    | Null          | Alberta          | 100   |
    | Canada        | British Columbia | 500   |
    | Null          | British Columbia | 500   |
    | United States | Montana          | 100   |
    | Null          | Montana          | 100   |
    | Null          | Null             | 700   |
    | Canada        | Null             | 600   |
    | United States | Null             | 100   |

    `(Country=Canada, Region=NULL, Sales=600)` 表示在加拿大（所有区域）的销售量总和为 600。

    下面是以 SQL 语言进行的转换。

    ```mssql
    SELECT
        dimension_1,
        dimension_2,
        ...
        dimension_n,
        sum (metrics_1) AS metrics_1,
        sum (metrics_2) AS metrics_2,
        ...
        sum (metrics_n) AS metrics_n
    FROM
        each_timestamp_data
    GROUP BY
        CUBE (dimension_1, dimension_2, ..., dimension_n);
    ```

    使用自动汇总功能之前，请考虑以下事项：

    * 如果要使用“SUM”来聚合数据，请确保你的指标在每个维度中都是可相加的。 下面是“不可相加”指标的一些示例：
      - 基于小数的指标。 这包括比率、百分比等。例如，你不应添加每个州的失业率来计算整个国家/地区的失业率。
      - 维度中的重叠。 例如，你不应该将每项运动中的人数相加来计算喜欢运动的人数，因为其中有重叠，一个人可以喜欢多项运动。
    * 为了确保整个系统的正常运行，多维数据集的大小是受限的。 目前的限制为 1,000,000。 如果你的数据超过该限制，则对应于该时间戳的引入会失败。

## <a name="advanced-settings"></a>高级设置

可以通过多个高级设置以自定义方式（例如指定引入偏移或并发）启用数据引入。 有关详细信息，请参阅数据馈送管理文章中的[高级设置](manage-data-feeds.md#advanced-settings)部分。

## <a name="specify-a-name-for-the-data-feed-and-check-the-ingestion-progress"></a>指定数据馈送的名称并检查引入进度
 
为数据馈送提供自定义名称，该名称将显示在工作区中。 然后，单击“提交”。 在数据馈送详细信息页中，可以使用引入进度栏查看状态信息。

:::image type="content" source="../media/datafeeds/ingestion-progress.png" alt-text="引入进度栏" lightbox="../media/datafeeds/ingestion-progress.png":::


若要检查引入失败详细信息，请执行以下操作： 

1. 单击“显示详细信息”。
2. 单击“状态”，然后选择“失败”或“错误”。
3. 将鼠标悬停在失败的引入上，查看显示的详细信息消息。

:::image type="content" source="../media/datafeeds/check-failed-ingestion.png" alt-text="检查失败的引入":::

“失败”状态指示稍后将为此数据源重试引入。
“错误”状态指示指标顾问不会针对此数据源进行重试。 若要重新加载数据，需要手动触发回填/重载。

还可以通过单击“刷新进度”来重载某个引入的进度。 在数据引入完成后，你可以随时单击指标，查看异常情况检测结果。

## <a name="next-steps"></a>后续步骤
- [管理数据馈送](manage-data-feeds.md)
- [不同数据源的配置](../data-feeds-from-different-sources.md)
- [配置指标并微调检测配置](configure-metrics.md)
