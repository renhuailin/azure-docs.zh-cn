---
title: 向数字孪生体添加标记
titleSuffix: Azure Digital Twins
description: 了解如何在数字孪生体中实施标记
author: baanders
ms.author: baanders
ms.date: 8/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8059178543bde38cbb1429f98f2f0205fb07c347
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771173"
---
# <a name="add-tags-to-digital-twins"></a>向数字孪生体添加标记 

你可以使用标记概念进一步识别你的数字孪生体并对其进行归类。 特别是，用户可能希望在其 Azure 数字孪生实例中复制现有系统中的标记，例如 [Haystack 标记](https://project-haystack.org/doc/appendix/tags)。 

本文档介绍了可用于在数字孪生体中实施标记的模式。

首先将标记作为属性添加到描述数字孪生体的[模型](concepts-models.md)中。 然后，在基于模型创建孪生体时，在孪生体上设置该属性。 之后，可以在[查询](concepts-query-language.md)中使用标记来标识和筛选孪生体。

## <a name="marker-tags"></a>记号标记 

**记号标记** 是一个简单字符串，用来对数字孪生体进行标记或归类，例如“blue”或“red”。 此字符串是标记的名称，记号标记没有有意义的值 — 标记的重要性仅体现在它的存在（或缺失）。 

### <a name="add-marker-tags-to-model"></a>向模型中添加记号标记 

记号标记建模为从 `string` 到 `boolean` 的 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 映射。 布尔值 `mapValue` 会被忽略，因为此标记只要存在就够了。 

下面是将记号标记作为属性实现的一个孪生体模型的摘录：

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>向数字孪生体中添加记号标记

在 `tags` 属性成为数字孪生体的模型的一部分后，你可以通过设置此属性的值在数字孪生体中设置记号标记。 

下面是有关如何为使用 [.NET SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 的孪生体设置标记 `tags` 的代码示例：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesCsharp":::

根据上面的示例创建带有标记属性的孪生体后，此孪生体将如下所示：

```JSON
{
  "$dtId": "myTwinID",
  "$etag": "W/\"e7429259-6833-46b4-b443-200a77a468c2\"",
  "$metadata": {
    "$model": "dtmi:example:Room;1",
    "Temperature": {
      "lastUpdateTime": "2021-08-03T14:24:42.0850614Z"
    },
    "tags": {
      "lastUpdateTime": "2021-08-03T14:24:42.0850614Z"
    }
  },
  "Temperature": 75,
  "tags": {
    "VIP": true,
    "oceanview": true
  }
}
```

>[!TIP]
> 使用 CLI 或 API [查询](how-to-query-graph.md)孪生体时，你会看到此孪生体的 JSON 表示形式。

### <a name="query-with-marker-tags"></a>使用记号标记进行查询

将记号添加到数字孪生体后，可以在查询中使用标记来筛选孪生体。 

下面的查询获取已标记为“red”的所有孪生体： 

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerTags1":::

还可以对标记进行组合以实现更复杂的查询。 下面的查询获取所有是圆形但不是红色的孪生体： 

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>值标记 

**值标记** 是一个键值对，用于为每个标记赋值，例如 `"color": "blue"` 或 `"color": "red"`。 在创建值标记后，还可以通过忽略标记的值将其用作记号标记。 

### <a name="add-value-tags-to-model"></a>向模型中添加值标记 

值标记建模为从 `string` 到 `string` 的 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 映射。 `mapKey` 和 `mapValue` 都很重要。 

下面是将值标记作为属性实现的一个孪生体模型的摘录：

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>向数字孪生体中添加值标记

与记号标记一样，你可以通过在模型中设置此 `tags` 属性的值，在数字孪生体中设置值标记。 若要将值标记用作记号标记，可以将 `tagValue` 字段设置为空字符串值 (`""`)。 

下面是两个孪生体的 JSON 正文，其中的值标记表示其大小。 此示例中的孪生体还包含用作记号标记的“red”或“purple”的值标记。

示例 Twin1，包含表示大的值标记和一个“red”记号标记：

```JSON
{
  "$dtId": "Twin1",
  "$etag": "W/\"d3997593-cc5f-4d8a-8683-957becc2bcdd\"",
  "$metadata": {
    "$model": "dtmi:example:ValueTags;1",
    "tags": {
      "lastUpdateTime": "2021-08-03T14:43:02.3150852Z"
    }
  },
  "tags": {
    "red": "",
    "size": "large"
  }
}
```

示例 Twin2，包含表示小的值标记和一个“purple”记号标记：
```JSON
{
  "$dtId": "Twin2",
  "$etag": "W/\"e215e586-b14a-4234-8ddb-be69ebfef878\"",
  "$metadata": {
    "$model": "dtmi:example:ValueTags;1",
    "tags": {
      "lastUpdateTime": "2021-08-03T14:43:53.1517123Z"
    }
  },
  "tags": {
    "purple": "",
    "size": "small"
  }
}
```

### <a name="query-with-value-tags"></a>使用值标记进行查询

与记号标记一样，你可以在查询中使用值标记来筛选孪生体。 还可以将值标记和记号标记一起使用。

在上面的示例中，`red` 用作记号标记。 请记住，下面的查询获取已标记为“red”的所有孪生体： 

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerTags1":::

下面的查询获取所有小型（值标记）且非红色的实体： 

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>后续步骤

详细了解如何设计和管理数字孪生体模型：
* [管理 DTDL 模型](how-to-manage-model.md)

详细了解如何查询孪生图：
* [查询孪生图](how-to-query-graph.md)
