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
ms.openlocfilehash: 742cff544886a1499bccfa575684edef708da7bd
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028353"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>关于 Azure 数字孪生的查询语言

回忆一下，Azure 数字孪生中心是从数字孪生和关系构建的 [双子图形](concepts-twins-graph.md)。 

可以查询此关系图以获取有关它所包含的数字孪生和关系的信息。 这些查询使用类似于 SQL 的自定义查询语言编写，称为 **Azure 数字孪生查询语言**。 这类似于具有许多可比较功能的 [IoT 中心查询语言](../iot-hub/iot-hub-devguide-query-language.md) 。

本文介绍了查询语言及其功能的基础知识。 有关查询语法的详细信息以及如何运行查询请求的详细信息，请参阅 [*如何：查询双子图形*](how-to-query-graph.md)。

## <a name="about-the-queries"></a>关于查询

你可以使用 Azure 数字孪生查询语言根据其 .。。
* 属性 (包括 [标记属性](how-to-use-tags.md)) 
* 模型
* 关系
  - 关系的属性

若要从客户端应用将查询提交到服务，请使用 Azure 数字孪生 [**查询 API**](/rest/api/digital-twins/dataplane/query)。 使用 API 的一种方法是通过 Azure 数字孪生的一个 [sdk](how-to-use-apis-sdks.md#overview-data-plane-apis) 。

## <a name="reference-expressions-and-conditions"></a>参考：表达式和条件

本部分介绍可用于写入 Azure 数字孪生查询的运算符和函数。 有关演示如何使用这些功能的示例查询，请参阅 [*如何：查询双子图形*](how-to-query-graph.md)。

> [!NOTE]
> 所有 Azure 数字孪生查询操作都区分大小写，因此请谨慎使用模型中定义的确切名称。 如果属性名称拼写错误或大小写不正确，则结果集为空，并且不返回任何错误。

### <a name="operators"></a>运算符

支持以下运算符：

| 系列 | 运算符 |
| --- | --- |
| 逻辑 |`AND`, `OR`, `NOT` |
| 比较 | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| 包含 | `IN`, `NIN` |

### <a name="functions"></a>函数

支持以下类型检查和强制转换函数：

| 函数 | 描述 |
| -------- | ----------- |
| `IS_DEFINED` | 返回一个布尔，它指示属性是否已经分配了值。 仅当该值为基元类型时才支持此功能。 基元类型包括字符串、布尔值、数字或 `null`。 `DateTime`不支持、对象类型和数组。 |
| `IS_OF_MODEL` | 返回一个布尔值，该值指示指定的上值是否与指定的模型类型匹配 |
| `IS_BOOL` | 返回一个布尔值，指示指定表达式的类型是否为布尔表达式。 |
| `IS_NUMBER` | 返回一个布尔值，指示指定表达式的类型是否为数字。 |
| `IS_STRING` | 返回一个布尔值，指示指定表达式的类型是否为字符串。 |
| `IS_NULL` | 返回一个布尔值，指示指定表达式的类型是否为 null。 |
| `IS_PRIMITIVE` | 返回一个布尔值，指示指定表达式的类型是否为基元（字符串、布尔值、数字或 `null`）。 |
| `IS_OBJECT` | 返回一个布尔值，指示指定表达式的类型是否为 JSON 对象。 |

支持以下字符串函数：

| 函数 | 描述 |
| -------- | ----------- |
| `STARTSWITH(x, y)` | 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式开头。 |
| `ENDSWITH(x, y)` | 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。 |

## <a name="query-limitations"></a>查询限制

本部分介绍查询语言的限制。

* 计时：实例的更改在查询中反映之前，可能会有最多10秒的延迟。 例如，如果使用 DigitalTwins API 完成了创建或删除孪生等操作，则结果可能不会立即反映在查询 API 请求中。 等待一小段时间应该足以解决问题。
* 语句内不支持子查询 `FROM` 。
* `OUTER JOIN` 不支持语义，这意味着，如果关系的排名为零，则将从输出结果集中消除整个 "行"。
* Graph 遍历深度限制为每个查询的五个 `JOIN` 级别。
* 操作的源 `JOIN` 受到限制：查询必须声明查询开始处的孪生。

## <a name="next-steps"></a>后续步骤

了解如何编写查询，以及 [*如何在操作方法：查询双子图*](how-to-query-graph.md)中查看客户端代码示例。