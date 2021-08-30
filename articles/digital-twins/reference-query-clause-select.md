---
title: Azure 数字孪生查询语言参考 - SELECT 子句
titleSuffix: Azure Digital Twins
description: Azure 数字孪生查询语言 SELECT 子句的参考文档
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: eeba36b200028d4e90e22cd32c9bab6dbe3799e0
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492858"
---
# <a name="azure-digital-twins-query-language-reference-select-clause"></a>Azure 数字孪生查询语言参考：SELECT 子句

本文档包含有关 [Azure 数字孪生查询语言](concepts-query-language.md) SELECT 子句的参考信息。

SELECT 子句是查询的第一个部分。 它指定查询将返回的列的列表。

所有查询都需要此子句。

## <a name="select-"></a>SELECT *

在 select 语句中使用 `*` 字符按原样投影数字孪生体文档，而无需将其分配给结果集中的属性。

>[!NOTE]
> 仅当查询不使用 `JOIN` 时，`SELECT *` 才是有效语法。 有关使用 `JOIN` 的查询的详细信息，请参阅 [Azure 数字孪生查询语言参考：JOIN 子句](reference-query-clause-join.md)。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectSyntax":::

### <a name="returns"></a>返回

从查询返回的属性集。

### <a name="example"></a>示例

下面的查询将返回实例中的所有数字孪生体。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectExample":::

## <a name="select-columns-with-projections"></a>包含投影的 SELECT 列

可使用 SELECT 子句中的投影来选择查询将返回的列。 可指定孪生和关系的命名集合，或孪生和关系的属性。

基元属性和复杂属性现在都支持投影。

### <a name="syntax"></a>语法

投影集合：

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectCollectionSyntax":::

投影属性：

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertySyntax":::

### <a name="returns"></a>返回

投影中指定的孪生体、属性或关系的集合。

如果对于特定的数据行，投影中包含的属性不存在，则结果集中同样也不存在该属性。 有关此行为的示例，请参阅[投影属性示例：数据行不存在该属性](#project-property-example-property-not-present-for-a-data-row)。

### <a name="examples"></a>示例

#### <a name="example-scenario"></a>示例方案

对于以下示例，请考虑包含以下数据元素的孪生图：
* 名为 FactoryA 的工厂孪生体
    - 包含一个名为 `name` 的属性，其值为 `FactoryA`
* 名为 Contoso 的使用者孪生体
    - 包含一个名为 `name` 的属性，其值为 `Contoso`
* 从 FactoryA 到 Contoso 的 consumerRelationship 关系，称为 `FactoryA-consumerRelationship-Contoso`
    - 包含一个名为 `managedBy` 的属性，其值为 `Jeff`

以下是说明此情况的关系图：

:::row:::
    :::column:::
        :::image type="content" source="media/reference-query-clause-select/projections-graph.png" alt-text="显示上述示例图的关系图。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="project-collection-example"></a>投影集合示例

以下是一个示例查询，该查询投影该图的一个集合。 下面的查询将返回实例中的所有数字孪生体，方法是将整个孪生体集合命名为 `T`，并将 `T` 投影为要返回的集合。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectCollectionExample":::

以下是此查询返回的 JSON 有效负载：

```json
{
  "value": [
    {
      "result": [
        {
          "T": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          }
        },
        {
          "T": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-with-join-example"></a>使用 JOIN 的投影示例

投影通常用于返回在 `JOIN` 中指定的集合。 以下查询使用投影来返回使用者、工厂和关系数据。 有关此示例中使用的 `JOIN` 语法的详细信息，请参阅 [Azure 数字孪生查询语言参考：JOIN 子句](reference-query-clause-join.md)。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectJoinExample":::

以下是此查询返回的 JSON 有效负载：

```json
{
  "value": [
    {
      "result": [
        {
          "Consumer": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          },
          "Factory": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          },
          "Relationship": {
            "$etag": "W/\"f01e07c1-19e4-4bbe-a12d-f5761e86d3e8\"",
            "$relationshipId": "FactoryA-consumerRelationship-Contoso",
            "$relationshipName": "consumerRelationship",
            "$sourceId": "FactoryA",
            "$targetId": "Contoso",
            "managedBy": "Jeff"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-property-example"></a>投影属性示例

以下是投影属性的示例。 下面的查询使用投影来返回使用者孪生体的 `name` 属性和关系的 `managedBy` 属性。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertyExample":::

以下是此查询返回的 JSON 有效负载：

```json
{
  "value": [
    {
      "result": [
        {
          "managedBy": "Jeff",
          "name": "Contoso"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-property-example-property-not-present-for-a-data-row"></a>投影属性示例：数据行不存在属性

如果对于特定的数据行，投影中包含的属性不存在，则结果集中同样也不存在该属性。

对于本示例，请考虑一组表示用户的孪生体。 有些孪生体有与之关联的期限，而其他孪生体则没有。

以下是投影 `name` 和 `age` 属性的查询：

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertyNotPresentExample":::

结果可能如下所示，其中某些孪生体缺少 `age` 属性，因为这些孪生体不具有此属性。

```json
{
  "value": [
    {
      "result": [
        {
          "name": "John",
          "age": 27
        },
        {
          "name": "Keanu"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

## <a name="select-count"></a>SELECT COUNT

使用此方法对结果集中的项数进行计数并返回该数字。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountSyntax":::

### <a name="arguments"></a>参数

无。

### <a name="returns"></a>返回

一个 `int` 值。

### <a name="example"></a>示例

下面的查询返回实例中所有数字孪生体的计数。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountExample":::

下面的查询返回实例中所有关系的计数。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountRelationshipsExample":::

## <a name="select-top"></a>SELECT TOP

使用此方法仅返回满足查询要求的前面几项。

### <a name="syntax"></a>语法

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectTopSyntax":::

### <a name="arguments"></a>参数

一个 `int` 值，指定要选择的前面几项。

### <a name="returns"></a>返回

孪生体的集合。

### <a name="example"></a>示例

下面的查询仅返回实例中的前五个数字孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectTopExample":::