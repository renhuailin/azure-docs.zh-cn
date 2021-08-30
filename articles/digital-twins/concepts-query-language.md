---
title: 查询语言
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生查询语言的基础知识。
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 00bc85b06bd734a075ff9a9ca2baf3fe58e95623
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253629"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>关于 Azure 数字孪生的查询语言

回忆一下，Azure 数字孪生中心是依据数字孪生体和关系构建的[孪生图](concepts-twins-graph.md)。 

可以查询此图以获取有关数字孪生体及其包含的关系的信息。 这些查询是用类似于 SQL 的自定义查询语言编写的，称为 Azure 数字孪生查询语言。 此语言类似于具有许多可比较功能的 [IoT 中心查询语言](../iot-hub/iot-hub-devguide-query-language.md)。

本文介绍了查询语言及其功能的基础知识。 有关查询语法以及如何运行查询请求的详细示例，请参阅[查询孪生体图](how-to-query-graph.md)。

## <a name="about-the-queries"></a>关于查询

可以使用 Azure 数字孪生查询语言根据其属性（包括
* [标记属性](how-to-use-tags.md)）来检索数字孪生体
* 模型
* 关系
  - 关系属性

若要从客户端应用将查询提交到服务，请使用 Azure 数字孪生[查询 API](/rest/api/digital-twins/dataplane/query)。 使用 API 的一种方法是通过 Azure 数字孪生的一个 [SDK](concepts-apis-sdks.md#overview-data-plane-apis)。

[!INCLUDE [digital-twins-query-reference.md](../../includes/digital-twins-query-reference.md)]

## <a name="considerations-for-querying"></a>查询注意事项

编写 Azure 数字孪生查询时，请注意以下几点：
* 记得区分大小写：所有 Azure 数字孪生查询操作都区分大小写，因此请务必使用模型中定义的确切名称。 如果属性名称拼写错误或大小写不正确，则结果集为空，并且不返回任何错误。
* 转义单引号：如果查询文本的数据中包含单引号字符，则需要使用 `\` 字符对引号进行转义。 下面是一个处理 D'Souza 属性值的示例：

  :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="EscapedSingleQuote":::

* **考虑可能的延迟**：对图表中的数据进行更改后，可能会有长达 10 秒的延迟才会在查询中反映更改。 [GetDigitalTwin API](how-to-manage-twin.md#get-data-for-a-digital-twin) 不会遇到这种延迟，因此如果需要即时响应，请使用 API 调用而不是通过查询来立即查看更改。

## <a name="next-steps"></a>后续步骤

学习如何编写查询，并查看[查询孪生体图](how-to-query-graph.md)中的客户端代码示例。