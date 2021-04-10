---
title: 查询语言
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生查询语言的基础知识。
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: fc9cd95063f84a9af7f989af9a65ce8f99852dc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490970"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>关于 Azure 数字孪生的查询语言

回忆一下，Azure 数字孪生中心是依据数字孪生体和关系构建的[孪生图](concepts-twins-graph.md)。 

可以查询此图以获取有关数字孪生体及其包含的关系的信息。 这些查询是用类似于 SQL 的自定义查询语言编写的，称为 Azure 数字孪生查询语言。 这类似于具有许多可比较功能的 [IoT 中心查询语言](../iot-hub/iot-hub-devguide-query-language.md)。

本文介绍了查询语言及其功能的基础知识。 有关查询语法以及如何运行查询请求的详细信息，请参阅[操作方法：查询孪生图](how-to-query-graph.md)。

## <a name="about-the-queries"></a>关于查询

可以使用 Azure 数字孪生查询语言根据其属性（包括
* [标记属性](how-to-use-tags.md)）来检索数字孪生体
* 模型
* 关系
  - 关系属性

若要从客户端应用将查询提交到服务，请使用 Azure 数字孪生[查询 API](/rest/api/digital-twins/dataplane/query)。 使用 API 的一种方法是通过 Azure 数字孪生的一个 [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)。

### <a name="considerations-for-querying"></a>查询注意事项

编写 Azure 数字孪生查询时，请注意以下几点：
* 记得区分大小写：所有 Azure 数字孪生查询操作都区分大小写，因此请务必使用模型中定义的确切名称。 如果属性名称拼写错误或大小写不正确，则结果集为空，并且不返回任何错误。
* 转义单引号：如果查询文本的数据中包含单引号字符，则需要使用 `\` 字符对引号进行转义。 下面是一个处理 D'Souza 属性值的示例：

  :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="EscapedSingleQuote":::

## <a name="reference-expressions-and-conditions"></a>参考：表达式和条件

本部分介绍可用于编写 Azure 数字孪生查询的运算符和函数。 有关演示如何使用这些功能的示例查询，请参阅[操作方法：查询孪生图](how-to-query-graph.md)。

### <a name="operators"></a>运算符

支持以下运算符：

| 系列 | 运算符 |
| --- | --- |
| 逻辑 |`AND`, `OR`, `NOT` |
| 比较 | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| Contains | `IN`, `NIN` |

### <a name="functions"></a>函数

支持以下检查和强制转换类型的函数：

| 函数 | 说明 |
| -------- | ----------- |
| `IS_DEFINED` | 返回一个布尔，它指示属性是否已经分配了值。 仅当该值为基元类型时才支持此功能。 基元类型包括字符串、布尔值、数字或 `null`。 不支持 `DateTime`、对象类型和数组。 |
| `IS_OF_MODEL` | 返回一个布尔值，该值指示指定的孪生体是否与指定的模型类型匹配 |
| `IS_BOOL` | 返回一个布尔值，指示指定表达式的类型是否为布尔表达式。 |
| `IS_NUMBER` | 返回一个布尔值，指示指定表达式的类型是否为数字。 |
| `IS_STRING` | 返回一个布尔值，指示指定表达式的类型是否为字符串。 |
| `IS_NULL` | 返回一个布尔值，指示指定表达式的类型是否为 null。 |
| `IS_PRIMITIVE` | 返回一个布尔值，指示指定表达式的类型是否为基元（字符串、布尔值、数字或 `null`）。 |
| `IS_OBJECT` | 返回一个布尔值，指示指定表达式的类型是否为 JSON 对象。 |

支持以下字符串函数：

| 函数 | 说明 |
| -------- | ----------- |
| `STARTSWITH(x, y)` | 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式开头。 |
| `ENDSWITH(x, y)` | 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。 |

## <a name="query-limitations"></a>查询限制

本部分介绍查询语言的限制。

* 计时：实例更改在查询中反映出来之前，可能会有 10 秒的延迟。 例如，如果使用 DigitalTwins API 完成创建或删除孪生体等操作，结果可能不会立即反映在查询 API 请求中。 等待一小段时间应足以解决问题。
* `FROM` 语句内不支持子查询。
* 不支持 `OUTER JOIN` 语义，这意味着，如果关系的排名为零，则会从输出结果集中消除整个“行”。
* 图形遍历深度限制为每个查询五个 `JOIN` 级别。
* 无法将 Azure 数字孪生中的关系作为独立实体进行查询；还需要提供有关关系来源的源孪生体的信息。 这意味着，用于查询关系的 `JOIN` 操作存在一些限制，以确保查询声明查询开始的孪生体。 相关示例请参阅“操作方法：查询孪生图”一文中的[按关系进行查询](how-to-query-graph.md#query-by-relationship)。

## <a name="next-steps"></a>后续步骤

学习如何编写查询，并查看[操作方法：查询孪生图](how-to-query-graph.md)中的客户端代码示例。