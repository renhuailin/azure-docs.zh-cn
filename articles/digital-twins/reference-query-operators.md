---
title: Azure 数字孪生查询语言参考 - 运算符
titleSuffix: Azure Digital Twins
description: Azure 数字孪生查询语言运算符参考文档
author: baanders
ms.author: baanders
ms.date: 03/22/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 06945efc6be8700955b1a475d00d21951102c214
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492786"
---
# <a name="azure-digital-twins-query-language-reference-operators"></a>Azure 数字孪生查询语言参考：运算符

本文档包含有关 [Azure 数字孪生查询语言](concepts-query-language.md)的运算符的参考信息。

## <a name="comparison-operators"></a>比较运算符

支持比较系列中的以下运算符。

* `=` 和 `!=`：用于比较表达式的相等性。
* `<` 和 `>`：用于表达式的有序比较。
* `<=` 和 `>=`：用于表达式的有序比较，其中包括相等性。

### <a name="example"></a>示例

下面是一个使用 `=` 的示例。 下面的查询返回温度值等于 80 的孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="EqualityExample":::

下面是一个使用 `<` 的示例。 下面的查询返回温度值小于 80 的孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ComparisonExample":::

下面是一个使用 `<=` 的示例。 下面的查询返回温度值小于或等于 80 的孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="OrderedComparisonExample":::

## <a name="contains-operators"></a>包含运算符

支持包含系列中的以下运算符。

* `IN`：如果给定的值在一组值中，则计算结果为 true。
* `NIN`：如果给定值不在一组值中，则计算结果为 true。

### <a name="example"></a>示例

下面是一个使用 `IN` 的示例。 下面的查询返回其 `owner` 属性是列表中几个选项之一的孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="InExample":::

## <a name="logical-operators"></a>逻辑运算符

支持逻辑系列中的以下运算符：
* `AND`：用于连接两个表达式，如果它们都为 true，则计算结果为 true。
* `OR`：用于连接两个表达式，如果它们其中至少一个为 true，则计算结果为 true。
* `NOT`：用于对表达式求反，如果未满足表达式条件，则计算结果为 true。

### <a name="example"></a>示例

下面是一个使用 `AND` 的示例。 下面的查询返回满足温度低于 80 且湿度低于 50 这两个条件的孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="AndExample":::

下面是一个使用 `OR` 的示例。 下面的查询返回至少满足温度低于 80 和湿度低于 50 这两个条件之一的孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="OrExample":::

下面是一个使用 `NOT` 的示例。 下面的查询返回不满足温度低于 80 这一条件的孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="NotExample":::

## <a name="limitations"></a>限制

以下限制适用于使用运算符的查询。
* [IN/NIN 的限制](#limit-for-innin)

有关详细信息，请参阅以下部分。

### <a name="limit-for-innin"></a>IN/NIN 的限制

对于可以包含在 `IN` 或 `NIN` 集内的值，其数量限制为 100 个值。
