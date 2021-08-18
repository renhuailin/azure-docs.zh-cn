---
title: 自定义查询规范
description: 了解如何使用自定义查询以编程方式从 Microsoft 商业市场的产品/服务的分析表中提取数据。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8d003980d7a8a1a5a5477a7d22ec547db13b52ad
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748291"
---
# <a name="custom-query-specification"></a>自定义查询规范

合作伙伴可以使用此查询规范轻松地构建自定义查询，以便从分析表中提取数据。 查询可用于仅选择符合特定条件的所需列和指标。 语言规范的核心是可在其基础上编写自定义查询的数据集定义。

## <a name="datasets"></a>数据集

自定义查询可以针对具有列和指标的数据集而运行，其方式与针对某个具有表和列的数据库运行某些查询的方式相同。 可通过数据集 API 获取可用于构建查询的数据集的完整列表。

下面是一个显示为 JSON 的数据集示例。

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>数据集的各个部分

- 数据集名称类似于数据库表名称。 例如，ISVUsage。 数据集包含可以选择的列（如 MarketplaceSubscriptionId）的列表。
- 数据集还包含指标，指标类似于数据库中的聚合函数。 例如，NormalizedUsage。
- 存在可以导出其范围内数据的固定时间跨度。

### <a name="formulating-a-query-on-a-dataset"></a>在数据集上构建查询

下面是一些示例查询，它们演示了如何提取各种类型的数据。

| 查询 | 说明 |
| ------------ | ------------- |
| **SELECT** MarketplaceSubscriptionId,CustomerId **FROM** ISVUsage **TIMESPAN LAST_MONTH** | 此查询会获取过去 1 个月内的每个 `MarketplaceSubscriptionId` 及其相应的 `CustomerId`。 |
| **SELECT** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **FROM** ISVUsage **ORDER BY** EstimatedExtendedChargeCC **LIMIT** 10 | 此查询会获取前 10 个订阅（每个订阅下销售的许可证数按降序排序）。 |
| **SELECT** CustomerId, NormalizedUsage, RawUsage **FROM** ISVUsage **WHERE** NormalizedUsage > 100000 **ORDER BY** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | 此查询会获取 NormalizedUsage 大于 100,000 的所有客户的 NormalizedUsage 和 RawUsage。 |
| **SELECT** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **FROM** ISVUsage **WHERE** CustomerId IN (‘2a31c234-1f4e-4c60-909e-76d234f93161’, ‘80780748-3f9a-11eb-b378-0242ac130002’) | 此查询会通过两个 `CustomerId` 值（`2a31c234-1f4e-4c60-909e-76d234f93161` 和 `80780748-3f9a-11eb-b378-0242ac130002`）获取 `MarketplaceSubscriptionId` 以及每个月的规范化用量。 |
|||

## <a name="query-specification"></a>查询规范

此部分介绍查询定义和结构。

### <a name="grammar-reference"></a>语法参考

下表介绍了查询中使用的符号。

| 符号 | 含义 |
| ------------ | ------------- |
| ? | 可选 |
| * | 零个或更多 |
| + | 一个或更多 |
| &#124; | 或/列表之一 |
| | |

### <a name="query-definition"></a>查询定义

查询语句包含以下子句：SelectClause、FromClause、WhereClause?、OrderClause?、LimitClause? 和 TimeSpan?。

- **SelectClause**：**SELECT** ColumOrMetricName (, ColumOrMetricName)*
    - **ColumOrMetricName**：在数据集中定义的列和指标
- **FromClause**：**FROM** DatasetName
    - **DatasetName**：在数据集中定义的数据集名称
- **WhereClause**：**WHERE** FilterCondition (**AND** FilterCondition)*
    - **FilterCondition**：ColumOrMetricName 运算符值
        - **Operator**： = | > | < | >= | <= | != | LIKE | NOT LIKE | IN | NOT IN
        - **Value**：Number | StringLiteral | MultiNumberList | MultiStringList 
            - **Number**：-? [0-9]+ (. [0-9] [0-9]*)?
            - **StringLiteral**：'[a-zA-Z0-9_]*'
            - **MultiNumberList**：(Number  (,Number)*)
            - **MultiStringList**：(StringLiteral (,StringLiteral)*)
- **OrderClause**：**ORDER BY** OrderCondition (,OrderCondition)*
    - **OrderCondition**：ColumOrMetricName (**ASC** | **DESC**)*
    - **LimitClause**：**LIMIT** [0-9]+
    - **TimeSpan**：**TIMESPAN** ( TODAY | YESTERDAY | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | LIFETIME)

### <a name="query-structure"></a>查询结构

报表查询由多个部分组成：

- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
- TIMESPAN

下面对每个部分进行了介绍。

#### <a name="select"></a>SELECT

查询的此部分指定将导出的列。 可以选择的列是数据集的 `selectableColumns` 和 `availableMetrics` 部分列出的字段。 如果所选字段列表中包含指标列，则会为非指标列的每个不同组合计算指标。 

**示例**：
- **SELECT** `OfferName`, `NormalizedUsage`

#### <a name="distinct"></a>DISTINCT

在 SELECT 后添加 DISTINCT 关键字可确保最终导出的数据不会有重复的行。 DISTINCT 关键字的工作方式与是否选择了指标列无关。

**示例**：
- **SELECT DISTINCT** `MarketplaceSubscriptionId, OfferType`

#### <a name="from"></a>FROM

查询的此部分指示需要从中导出数据的数据集。 此处提供的数据集名称需要是数据集 API 返回的有效数据集名称。

**示例**：
- FROM `ISVUsage`
- FROM `ISVOrder`

#### <a name="where"></a>WHERE

查询的此部分用于指定数据集上的筛选条件。 最终的导出的文件中将仅显示与此子句中列出的所有条件匹配的行。 筛选条件可以位于 `selectableColumns` 和 `availableMetrics` 中列出的任意列上。 仅当运算符为 `IN` 或 `NOT IN` 时，筛选条件中指定的值才能是数字列表或字符串列表。 这些值始终可以作为文本字符串提供，并且会转换为列的原生类型。 需要使用 `AND` 运算将多个筛选条件分开。

**示例**：

- MarketplaceSubscriptionId = ‘868368da-957d-4959-8992-3c12dc7e6260’
- CustomerName **LIKE** ‘%Contosso%’
- CustomerId **NOT IN** (1000, 1001, 1002)
- OrderQuantity=100
- OrderQuantity=‘100’
    - MarketplaceSubscriptionId=’7b487ac0-ce12-b732-dcd6-91a1e4e74a50’ AND CustomerId=’ 0f8b7fa0-eb83-a183-1225-ca153ef807aa’

#### <a name="order-by"></a>ORDER BY

查询的此部分指定已导出行的排序条件。 可以在其上定义排序的列需要来自数据集的 `selectableColumns` 和 `availableMetrics`。 如果未指定排序方向，则列上的排序方向默认为 `DESC`。 可以通过使用逗号来分隔条件，在多个列上定义排序。

**示例**：

- **ORDER BY** NormalizedUsage **ASC**, EstimatedExtendedCharge(CC) **DESC**
- **ORDER BY** CustomerName **ASC**, NormalizedUsage

#### <a name="limit"></a>LIMIT

查询的此部分指定要导出的行数。 指定的数字必须是非零正整数。

#### <a name="timespan"></a>TIMESPAN

查询的此部分指定一个持续时间，该时间范围内的数据需要导出。 可能的值应来自数据集定义中的 `availableDateRanges` 字段。

### <a name="case-sensitivity-in-query-specification"></a>查询规范中的区分大小写

规范完全不区分大小写。 可以使用大写或小写来指定预定义关键字、列名称和值。

## <a name="see-also"></a>请参阅

- [系统查询列表](analytics-system-queries.md)
