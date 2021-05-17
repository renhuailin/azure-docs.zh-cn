---
title: Azure 数字孪生查询语言参考 - 函数
titleSuffix: Azure Digital Twins
description: Azure 数字孪生查询语言函数的参考文档
author: baanders
ms.author: baanders
ms.date: 03/22/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 80b32907499d3c8588a7a565972f49923abbe5c7
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108296357"
---
# <a name="azure-digital-twins-query-language-reference-functions"></a>Azure 数字孪生查询语言参考：函数

本文档包含有关 [Azure 数字孪生查询语言](concepts-query-language.md)函数的参考信息。

## <a name="endswith"></a>ENDSWITH

用于确定给定字符串是否以特定其他字符串结尾的字符串函数。 

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="EndsWithSyntax":::

### <a name="arguments"></a>参数

* `<string-to-check>`：用于检查结尾内容的字符串
* `<ending-string>`：表示要检查的结尾内容的字符串

### <a name="returns"></a>返回

一个布尔值，该值指示第一个字符串表达式是否以第二个字符串表达式结尾。

### <a name="example"></a>示例

下面的查询返回所有 ID 以 `-small` 结尾的数字孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="EndsWithExample":::

## <a name="is_defined"></a>IS_DEFINED

用于检查是否定义了属性的检查和强制转换类型的函数。

仅当属性值为基元类型时才支持此函数。 基元类型包括字符串、布尔值、数字或 `null`。 不支持 `DateTime`、对象类型和数组。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsDefinedSyntax":::

### <a name="arguments"></a>参数

`<property>`：一个属性，用于确定其是否已定义。 属性必须为基元类型。

### <a name="returns"></a>返回

一个布尔值，该值指示属性是否已经分配了值。

### <a name="example"></a>示例

下面的查询返回所有定义了 Location 属性的数字孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsDefinedExample":::

## <a name="is_of_model"></a>IS_OF_MODEL

用于确定孪生体是否属于特定模型类型的检查和强制转换类型的函数。 包括从指定模型继承的模型。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsOfModelSyntax":::

### <a name="arguments"></a>参数

必需：
* `<model-ID>`：要检查的模型 ID。

可选：
* `<twin-collection>`：在存在多个孪生体集合时（例如使用 `JOIN` 时）指定搜索某一孪生体集合。
* `exact`：需要完全匹配。 如果未设置此参数，结果集将包括具有从指定模型继承的模型的孪生体。

### <a name="returns"></a>返回

一个布尔值，该值指示指定的孪生体是否与指定的模型类型匹配。

### <a name="example"></a>示例

下面的查询返回 DT 集合中完全属于 `dtmi:example:room;1` 模型类型的孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsOfModelExample":::

## <a name="is_bool"></a>IS_BOOL

用于确定表达式是否具有布尔值的检查和强制转换类型的函数。

如果处理查询结果的程序需要布尔值，而你想筛选出属性不是布尔值的情况，则此函数通常与其他谓词组合在一起。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolSyntax":::

### <a name="arguments"></a>参数

`<expression>`：用于检查其是否为布尔值的表达式。

### <a name="returns"></a>返回

一个布尔值，该值指示指定表达式的类型是否为布尔值。

### <a name="example"></a>示例

下面的查询选择具有布尔属性 `HasTemperature` 的数字孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolExample":::

下面的查询基于上面的示例选择具有布尔属性 `HasTemperature` 的数字孪生体，而该属性的值不是 `false`。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsBoolNotFalseExample":::

## <a name="is_number"></a>IS_NUMBER

用于确定表达式是否具有数值的检查和强制转换类型的函数。

如果处理查询结果的程序需要数值，而你项筛选出属性不是数字的情况，则此函数通常与其他谓词组合在一起。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNumberSyntax":::

### <a name="arguments"></a>参数

`<expression>`：用于检查其是否为数字的表达式。

### <a name="returns"></a>返回

一个布尔值，该值指示指定表达式的类型是否为数字。

### <a name="example"></a>示例

下面的查询选择具有数值属性 `Capacity` 且其值不等于 0 的数字孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNumberExample":::

## <a name="is_string"></a>IS_STRING

用于确定表达式是否具有字符串值的检查和强制转换类型的函数。 

如果处理查询结果的程序需要字符串值，而你想筛选出属性不是字符串的情况，则此函数通常与其他谓词组合在一起。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsStringSyntax":::

### <a name="arguments"></a>参数

`<expression>`：用于检查其是否为字符串的表达式。

### <a name="returns"></a>返回

一个布尔值，该值指示指定表达式的类型是否为字符串。

### <a name="example"></a>示例

下面的查询选择具有字符串属性 `Status` 属性且其值不等于 `Completed` 的数字孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsStringExample":::

## <a name="is_null"></a>IS_NULL

用于确定表达式的值是否为 `null` 的检查和强制转换类型的函数。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNullSyntax":::

### <a name="arguments"></a>参数

`<expression>`：用于检查其是否为 null 的表达式。

### <a name="returns"></a>返回

一个布尔值，该值指示指定表达式的类型是否为 `null`。

### <a name="example"></a>示例

下面的查询返回 Temperature 不为 null 值的孪生体。 有关此查询中使用的 `NOT` 运算符的详细信息，请参阅 [Azure 数字孪生查询语言参考：运算符](reference-query-operators.md#logical-operators)。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsNullExample":::

## <a name="is_primitive"></a>IS_PRIMITIVE

用于确定表达式的值是否为基元类型（字符串、布尔值、数值或 `null`）的检查和强制转换类型函数。

如果处理查询结果的程序需要基元类型的值，而你想筛选出属性不为基元的情况，则此函数通常与其他谓词组合在一起。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsPrimitiveSyntax":::

### <a name="arguments"></a>参数

`<expression>`：用于检查其是否为基元类型的表达式。

### <a name="returns"></a>返回

一个布尔值，该值指示指定表达式的类型是否为基元类型（字符串、布尔、数值或 `null`）。

### <a name="example"></a>示例

下面的查询返回 ID 为“ABC”的 Factory 的 `area` 属性（仅当 `area` 属性为基元类型时）。 若要详细了解如何在查询结果中投影某些列（如此查询对 `area` 的投影），请参阅 [Azure 数字孪生查询语言参考：SELECT 子句](reference-query-clause-select.md#select-columns-with-projections)。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsPrimitiveExample":::

## <a name="is_object"></a>IS_OBJECT

用于确定表达式的值是否为 JSON 对象类型的检查和强制转换类型的函数。

如果处理查询结果的程序需要 JSON 对象，而你想筛选出值不是 JSON 对象的情况，则此函数通常与其他谓词组合在一起。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsObjectSyntax":::

### <a name="arguments"></a>参数

`<expression>`：用于检查其是否为对象类型的表达式。

### <a name="returns"></a>返回

一个布尔值，该值指示指定表达式的类型是否为 JSON 对象。

### <a name="example"></a>示例

下面的查询选择所有数字孪生体，这是其中一个名为 `MapObject` 的对象并且不具有子属性 `TemperatureReading`。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="IsObjectExample":::

## <a name="startswith"></a>STARTSWITH

用于确定给定字符串是否以特定其他字符串开头的字符串函数。 

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="StartsWithSyntax":::

### <a name="arguments"></a>参数

* `<string-to-check>`：用于检查开头内容的字符串
* `<beginning-string>`：表示要检查的开头内容的字符串

### <a name="returns"></a>返回

一个布尔值，该值指示第一个字符串表达式是否以第二个字符串表达式开头。

### <a name="example"></a>示例

下面的查询返回所有 ID 以 `area1-` 开头的数字孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" ID="StartsWithExample":::
