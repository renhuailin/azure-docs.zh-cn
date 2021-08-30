---
title: Azure 数字孪生查询语言参考 - WHERE 子句
titleSuffix: Azure Digital Twins
description: Azure 数字孪生查询语言 WHERE 子句的参考文档
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: f9be1425ae614c266f17a95668faf7fb9d21d2f1
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492822"
---
# <a name="azure-digital-twins-query-language-reference-where-clause"></a>Azure 数字孪生查询语言参考：WHERE 子句

本文档包含有关[Azure 数字孪生查询语言](concepts-query-language.md)的 WHERE 子句的参考信息。

WHERE 子句是查询的最后一部分。 该子句用于筛选根据特定条件返回的项。

在查询时，此子句为可选项。

## <a name="core-syntax-where"></a>核心语法：WHERE

WHERE 子句与布尔条件一起用于筛选查询结果。 

条件可以是计算结果为布尔值的[函数](reference-query-functions.md)。 你还可以使用孪生和关系的属性（通过 `.` 访问）以及比较或包含类型[运算符](reference-query-operators.md)创建自己的布尔语句。

### <a name="syntax"></a>语法

使用属性和运算符：

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereSyntax":::

使用函数：

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereFunctionSyntax":::

### <a name="arguments"></a>参数

计算结果为 `Boolean` 值的条件。

### <a name="examples"></a>示例

下面是使用属性和运算符的示例。 以下查询在 WHERE 子句中指定仅返回“Room1”值为 `$dtId` 的孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereExample":::

下面是使用函数的示例。 以下查询使用 `IS_OF_MODEL` 函数在 WHERE 子句中指定仅返回模型为 `dtmi:sample:Room;1` 的孪生体。 有关 `IS_OF_MODEL` 函数的详细信息，请参阅 [Azure 数字孪生查询语言参考：函数](reference-query-functions.md#is_of_model)。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereFunctionExample":::