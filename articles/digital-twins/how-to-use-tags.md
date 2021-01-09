---
title: 向数字孪生添加标记
titleSuffix: Azure Digital Twins
description: 请参阅如何在数字孪生上实现标记
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9a1a55bdf21b74116450ca32f66d891f1aa206d3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045404"
---
# <a name="add-tags-to-digital-twins"></a>向数字孪生添加标记 

您可以使用标记的概念进一步识别和分类您的数字孪生。 特别是，用户可能希望在其 Azure 数字孪生实例中复制现有系统中的标记，例如 [Needle-in-the-haystack 标记](https://project-haystack.org/doc/TagModel)。 

本文档介绍可用于实现数字孪生上的标记的模式。

首先将标记作为属性添加到描述数字克隆的 [模型](concepts-models.md) 中。 然后，在根据模型创建的克隆上，将设置该属性。 之后，可以在 [查询](concepts-query-language.md) 中使用标记来识别和筛选孪生。

## <a name="marker-tags"></a>标记标记 

**标记标记** 是一个简单的字符串，用于标记或分类数字输出，如 "blue" 或 "red"。 此字符串是标记的名称，标记标记没有有意义的值—标记只有在其存在 (或缺少) 时才有意义。 

### <a name="add-marker-tags-to-model"></a>向模型添加标记标记 

标记标记作为从到的 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 映射建模 `string` `boolean` 。 省略了布尔值 `mapValue` ，因为标记的存在都是重要的。 

下面是将标记标记实现为属性的非整数模型摘录：

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>向数字孪生添加标记标记

如果该 `tags` 属性是数字克隆模型的一部分，则可以通过设置此属性的值来设置数字克隆中的标记标记。 

下面是一个示例，用于填充 `tags` 三个孪生的标记：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

### <a name="query-with-marker-tags"></a>带有标记标记的查询

向数字孪生添加标记后，可以使用标记在查询中筛选孪生。 

下面是一个查询，用于获取已标记为 "red" 的所有孪生： 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

您还可以将标记与更复杂的查询组合在一起。 下面是一个查询，用于获取所有舍入的孪生（而非红色）： 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>值标记 

**值标记** 是一个键/值对，用于为每个标记赋值，如 `"color": "blue"` 或 `"color": "red"` 。 一旦创建了值标记，它也可以通过忽略标记的值，用作标记标记。 

### <a name="add-value-tags-to-model"></a>向模型添加值标记 

值标记作为从到的 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 映射建模 `string` `string` 。 和都非常 `mapKey` `mapValue` 重要。 

下面是从实现值标记作为属性的非整数模型摘录：

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>向数字孪生添加值标记

与标记标记一样，您可以通过在模型中设置此属性的值，在数字克隆中设置值标记 `tags` 。 若要将值标记用作标记标记，可以将 `tagValue` 字段设置为空字符串值 (`""`) 。 

下面是一个填充三个孪生的值的示例 `tags` ：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

请注意 `red` ， `purple` 在此示例中，和用作标记标记。

### <a name="query-with-value-tags"></a>查询值标记

与标记标记一样，可以使用值标记来筛选查询中的孪生。 你还可以将值标记和标记标记一起使用。

在上面的示例中，正用作标记 `red` 标记。 请记住，这是一个查询，用于获取已标记为 "red" 的所有孪生： 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

下面是一个查询，用于获取) 的小型 (值标记，而不是红色的所有实体： 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>后续步骤

详细了解如何设计和管理数字克隆模型：
* [*操作说明：管理自定义模型*](how-to-manage-model.md)

阅读有关查询克隆图形的详细信息：
* [*操作方法：查询双子图形*](how-to-query-graph.md)