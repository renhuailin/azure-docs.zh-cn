---
title: Azure 数字孪生查询语言参考 - JOIN 子句
titleSuffix: Azure Digital Twins
description: Azure 数字孪生查询语言 JOIN 子句的参考文档
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 7dc6827f7ebd7b034ffc00906629bafe04036fbd
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109789562"
---
# <a name="azure-digital-twins-query-language-reference-join-clause"></a>Azure 数字孪生查询语言参考：JOIN 子句

本文档包含有关 [Azure 数字孪生查询语言](concepts-query-language.md) JOIN 子句的参考信息。

如果希望进行查询以遍历 Azure 数字孪生图，可在 Azure 数字孪生查询语言中使用 `JOIN` 子句作为 [FROM 子句](reference-query-clause-from.md)的一部分。

在查询时，此子句为可选项。

## <a name="core-syntax-join--related"></a>核心语法：与 JOIN...相关 
由于 Azure 数字孪生中的关系是数字孪生体的一部分，而不是独立实体，因此，可在 `JOIN` 查询中使用 `RELATED` 关键字，以从孪生体集合引用特定类型的关系集。 可以为此关系集分配集合名称。

然后，该查询必须使用 `WHERE` 子句来指定可用于支持关系查询的特定孪生体。 这可通过在源或目标孪生体的 `$dtId` 值上进行筛选来完成。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="JoinSyntax":::

### <a name="example"></a>示例

以下查询通过包含关系，选择与 ID 为 ABC 的孪生体相关的所有数字孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="JoinExample":::

## <a name="multiple-joins"></a>多个 JOIN

单个查询最多支持五个 `JOIN`，这允许一次遍历多个级别的关系。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MultiJoinSyntax":::

### <a name="example"></a>示例

以下查询基于包含 LightPanel 的 Room，每个 LightPanel 包含多个 LightBulb。 该查询将获取房间 1 和 2 的 LightPanel 中包含的所有 LightBulb。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MultiJoinExample":::

## <a name="limitations"></a>限制

以下限制适用于使用 `JOIN` 的查询。
* [深度限制为 5](#depth-limit-of-five)
* [无 OUTER JOIN 语义](#no-outer-join-semantics)
* [需要源孪生体](#twins-required)

有关详细信息，请参阅以下部分。

### <a name="depth-limit-of-five"></a>深度限制为 5

图形遍历深度限制为每个查询五个 `JOIN` 级别。

#### <a name="example"></a>示例

以下查询说明了 Azure 数字孪生查询中可以使用的最大 `JOINs` 数。 该查询可获取 Buliding1 中的所有 LightBulb。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="MaxJoinExample":::

### <a name="no-outer-join-semantics"></a>无 OUTER JOIN 语义

不支持 `OUTER JOIN` 语义，这意味着，如果关系的排名为零，则会从输出结果集中消除整个“行”。

#### <a name="example"></a>示例

请考虑以下说明建筑物遍历的查询。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="NoOuterJoinExample":::

如果 Building1 不包含楼层，则此查询将返回空结果集（而不是返回一行，其值用于 Building，`undefined` 用于 Floor）。

### <a name="twins-required"></a>需要孪生体

无法将 Azure 数字孪生中的关系作为独立实体进行查询；还需要提供有关关系来源的源孪生体的信息。 这些信息会通过 `RELATED` 关键字，纳入 Azure 数字孪生默认 `JOIN` 的用法中。 

具有 `JOIN` 子句的查询还必须按 `WHERE` 子句中任何孪生体的 `$dtId` 属性进行筛选，以明确可用于支持关系查询的孪生体。