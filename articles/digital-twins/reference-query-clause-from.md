---
title: Azure 数字孪生查询语言参考 - FROM 子句
titleSuffix: Azure Digital Twins
description: Azure 数字孪生查询语言 FROM 子句的参考文档
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 1cbdbad379887687c71bd8c52b5791bde4e08a08
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492876"
---
# <a name="azure-digital-twins-query-language-reference-from-clause"></a>Azure 数字孪生查询语言参考：FROM 子句

本文档包含有关 [Azure 数字孪生查询语言](concepts-query-language.md) FROM 子句的参考信息。

FROM 子句是查询的第二部分。 它指定查询将处理的集合和任何联接。

所有查询都需要此子句。

## <a name="select--from-digitaltwins"></a>SELECT ... FROM DIGITALTWINS

使用 `FROM DIGITALTWINS`（不区分大小写）引用实例中的整个数字孪生体集合。

你可以通过将名称条件到语句的末尾，选择为数字孪生体集合添加名称。

### <a name="syntax"></a>语法

基本：

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsSyntax":::

为集合命名：

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsNamedSyntax":::

### <a name="examples"></a>示例

下面是一个基本查询。 下面的查询将返回实例中的所有数字孪生体。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsExample":::

下面是一个使用已命名集合的查询。 下面的查询会为集合分配名称 `T`，并且仍会返回实例中的所有数字孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsNamedExample":::

## <a name="select--from-relationships"></a>SELECT ... FROM RELATIONSHIPS

使用 `FROM RELATIONSHIPS`（不区分大小写）引用实例中的整个关系集合。

你可以通过将名称条件到语句的末尾，选择为关系集合添加名称。

>[!NOTE]
> 此功能无法与 `JOIN` 相组合。

### <a name="syntax"></a>语法

基本：

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsSyntax":::

为集合命名：

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsNamedSyntax":::

### <a name="examples"></a>示例

以下是返回实例中所有关系的查询。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsExample":::

以下是返回来自孪生体 `A`、`B`、`C` 或 `D` 的所有关系的查询。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsFilteredExample":::

## <a name="using-from-and-join-together"></a>结合使用 FROM 和 JOIN

`FROM` 可以与 `JOIN` 子句相结合，表示 Azure 数字孪生图中的跨实体遍历。

有关 `JOIN` 子句以及如何创建图形遍历查询的详细信息，请参阅[Azure 数字孪生查询语言参考：JOIN 子句](reference-query-clause-join.md)。

## <a name="limitations"></a>限制

以下限制适用于使用 `FROM` 的查询。
* [无子查询](#no-subqueries)
* [选择 FROM RELATIONSHIPS 或 JOIN](#choose-from-relationships-or-join)

有关详细信息，请参阅以下部分。

### <a name="no-subqueries"></a>无子查询

`FROM` 语句内不支持子查询。

#### <a name="example-negative"></a>示例（反面）

下面的查询演示了由于此限制而无法执行的操作示例。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromNegativeExample":::

### <a name="choose-from-relationships-or-join"></a>选择 FROM RELATIONSHIPS 或 JOIN

`FROM RELATIONSHIPS` 功能无法与 `JOIN` 相结合. 必须选择这些选项中最适合你想要选择的信息的选项。


