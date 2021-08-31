---
title: 查询孪生图
titleSuffix: Azure Digital Twins
description: 了解如何查询 Aszure 数字孪生孪生图以获取信息。
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9d8a72f4457c6759dc93fcdeae03abd1d4c8b168
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729824"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>查询 Azure 数字孪生孪生图

本文提供了查询示例和说明来介绍如何使用 Azure 数字孪生查询语言查询[孪生图](concepts-twins-graph.md)以获取信息。 （如需查询语言简介，请参阅[查询语言](concepts-query-language.md)。）

本文包含示例查询，它们说明了数字孪生体的查询语言结构和常见查询操作。 本文还介绍了在编写查询后如何使用 Azure 数字孪生[查询 API](/rest/api/digital-twins/dataplane/query) 或 [SDK](concepts-apis-sdks.md#overview-data-plane-apis) 运行查询。

> [!NOTE]
> 如果通过 API 或 SDK 调用运行以下示例查询，则需将查询文本压缩为一行。

[!INCLUDE [digital-twins-query-reference.md](../../includes/digital-twins-query-reference.md)]

## <a name="show-all-digital-twins"></a>显示所有数字孪生

下面是基本查询，它将会返回该实例中所有数字孪生体的列表：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>按属性查询

按属性（包括 ID 和元数据）获取数字孪生：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty1":::

如以上查询所示，将使用元数据字段 `$dtId` 查询数字孪生体的 ID。

>[!TIP]
> 如果使用 Cloud Shell 针对以 `$` 开头的元数据字段运行查询时，应使用反撇号将 `$` 转义，让 Cloud Shell 知道它不是变量，应将其作为查询文本中的文本来使用。

还可以根据是否定义了某个属性来获取孪生。 下面的查询获取定义了 Location 属性的孪生体：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty2":::

此查询有助于按孪生体的“tag”属性来获取孪生体，如[为数字孪生体添加标记](how-to-use-tags.md)中所述。 下面的查询获取所有标记为“red”的孪生体：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryMarkerTags1":::

还可以根据属性类型获取孪生。 下面的查询获取 Temperature 属性为数字的孪生体：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty3":::

>[!TIP]
> 如果属性的类型为 `Map`，则可以直接在查询中使用映射键和值，如下所示：
> :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>按模型查询

`IS_OF_MODEL` 运算符可用于基于孪生体的模型进行筛选。

它考虑了[继承](concepts-models.md#model-inheritance)和模型[版本控制](how-to-manage-model.md#update-models)，并且如果指定孪生满足以下任一条件，则它的评估结果为 true：

* 孪生直接实现提供给 `IS_OF_MODEL()` 的模型，并且孪生中的模型的版本号大于或等于提供的模型的版本号
* 孪生实现的模型可扩展提供给 `IS_OF_MODEL()` 的模型，并且孪生的扩展模型版本号大于或等于提供的模型的版本号 

例如，如果查询模型 `dtmi:example:widget;4` 的孪生，则查询将根据版本 4 或更高版本的小组件模型返回所有孪生，并且还将根据从小组件继承的版本 4 或更高版本的任意模型返回孪生   。

`IS_OF_MODEL` 可以采用多个不同的参数，因此本节的其余部分将专门介绍其各种重载选项。

`IS_OF_MODEL` 的最简单用法仅使用 `twinTypeName` 参数：`IS_OF_MODEL(twinTypeName)`。
下面的查询示例将值传入此参数：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel1":::

若要在存在多个孪生集合时（例如在使用 `JOIN` 时）指定搜索某个孪生集合，请添加 `twinCollection` 参数：`IS_OF_MODEL(twinCollection, twinTypeName)`。
下面的查询示例为此参数添加值：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel2":::

若要进行完全匹配，请添加 `exact` 参数：`IS_OF_MODEL(twinTypeName, exact)`。
下面的查询示例为此参数添加值：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel3":::

你也可以同时传递所有三个参数：`IS_OF_MODEL(twinCollection, twinTypeName, exact)`。
下面的查询示例为所有三个参数指定值：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>按关系查询

当基于数字孪生体的关系进行查询时，Azure 数字孪生查询语言有一个特殊的语法。

关系将被拉取到 `FROM` 子句中的查询范围内。 与“经典”SQL 类型语言不同，`FROM` 子句中的每个表达式都不是表；更确切地说，`FROM` 子句表达的是跨实体关系遍历。 为了跨关系遍历，Azure 数字孪生使用自定义版本的 `JOIN`。

请回忆一下，在使用 Azure 数字孪生[模型](concepts-models.md)功能时，关系不是独立于孪生而存在的，这就意味着此处的关系不能单独被查询，而是必须绑定到某个孪生体。
为了反映这个事实，在 `JOIN` 子句中使用了关键字 `RELATED`，以便在来自孪生体集合的某种特定类型关系的集中拉取。 然后，该查询必须在 `WHERE` 子句中筛选，以指明要在关系查询中使用哪个（些）特定孪生体（使用孪生体的 `$dtId` 值）。

下节提供了示例进行介绍。

### <a name="basic-relationship-query"></a>基本关系查询

下面是基于示例关系的查询。 此代码片段选择 ID 属性为“ABC”的所有数字孪生体，以及所有通过“包含”关系与这些数字孪生体相关的数字孪生体 。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship1":::

关系的类型（上述示例中的 `contains`）是通过使用来自关系的 [DTDL 定义](concepts-models.md#basic-relationship-example)的“name”字段来指明的。

> [!NOTE]
> 开发人员无需将此 `JOIN` 与 `WHERE` 子句中的键值相关联（也不需要使用 `JOIN` 定义以内联方式指定键值）。 此关联由系统自动计算，因为关系属性本身标识目标实体。

### <a name="query-by-the-source-or-target-of-a-relationship"></a>按关系的源或目标进行查询

可使用关系查询结构来标识作为关系的源或目标的数字孪生体。

例如，你可从源孪生体开始，然后按照其关系查找关系的目标孪生体。 下面的查询示例查找来自孪生体 source-twin 的源关系的目标孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationshipSource":::

也可以从关系的目标开始，追溯关系以查找源孪生体。 下面是一个查询示例，该查询查找孪生体 target-twin 的源关系的源孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationshipTarget":::

### <a name="query-the-properties-of-a-relationship"></a>查询关系的属性

与数字孪生体通过 DTDL 描述属性的方式类似，关系也可以具有属性。 可根据孪生体关系的属性查询孪生体。
使用 Azure 数字孪生查询语言，可以通过在 `JOIN` 子句中将别名分配给关系来筛选和投影关系。

例如，考虑具有 reportedCondition 属性的 servicedBy 关系 。 在下面的查询中，将为此关系提供一个别名“R”，以便引用其属性。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship2":::

在上述示例中，注意 reportedCondition 为何是 servicedBy 关系本身的属性（以及为何不是某些具有 servicedBy 关系的数字孪生的属性）  。

### <a name="query-with-multiple-joins"></a>使用多个 JOIN 查询

在一个查询中，最多支持五个 `JOIN`，这样你就可以一次穿越多个级别的关系。 

要查询多种级别的关系，请使用单个 `FROM` 语句，然后使用多个 `JOIN` 语句，其中 `JOIN` 语句表示与先前 `FROM` 或 `JOIN` 语句的结果相关的关系。

下面是多联接查询的示例，它获取房间 1 和 2 中照明面板中包含的所有灯泡数。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>项目计数

可以使用 `Select COUNT` 子句计算结果集中的项目数：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount1":::

添加 `WHERE` 子句以计算满足特定条件的项目数。 以下示例使用基于孪生模型类型的应用筛选器进行计数（若要详细了解此语法，请参阅下方的按模型查询）：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount2":::

还可以将 `COUNT` 与 `JOIN` 子句结合使用。 下面的查询统计房间 1 和 2 中照明面板中包含的所有灯泡的数量：

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>筛选结果：选择排名靠前的项目

可以使用 `Select TOP` 子句在查询中选择多个“排名靠前”的项目。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>筛选结果：使用投影指定返回集

通过在 `SELECT` 语句中使用投影，可以选择查询将返回的列。 基元属性和复杂属性现在都支持投影。 有关 Azure 数字孪生投影的详细信息，请参阅 [SELECT 子句参考文档](reference-query-clause-select.md#select-columns-with-projections)。

下面的查询示例使用投影来返回孪生体和关系。 以下查询对方案中的客户、工厂和边缘进行了投影，其中 ID 为 ABC 的工厂通过 Factory.customer 关系与客户相关联，而该关系则表示为边缘  。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections1":::

你还可以使用投影返回孪生的属性。 以下查询对客户的 Name 属性进行了投影，这些客户通过 Factory.customer 关系与 ID 为 ABC 的工厂相关联  。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections2":::

你还可以使用投影返回关系的属性。 与先前的示例类似，以下查询对客户的 Name 属性进行了投影，这些客户通过 Factory.customer 关系与 ID 为 ABC 的工厂相关联；但现在该查询还返回了该关系的 prop1 和 prop2 两个属性    。 它通过将关系命名为边缘并收集其属性来实现这一点。  

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections3":::

你也可以使用别名来简化使用投影的查询。

以下查询执行与先前示例相同的操作，但会对属性名称采用以下别名：`consumerName`、`first`、`second` 和 `factoryArea`。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections4":::

下面的类似查询对上面的同一个集进行查询，但只将 Consumer.name 属性投影为 `consumerName`，并将整个工厂投影为孪生体。

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>使用 IN 运算符生成高效的查询

通过使用 `IN` 运算符生成孪生数组并进行查询，可以大大减少所需的查询数。 

例如，假设建筑物包含楼层，而楼层包含房间   。 若要搜索建筑物内室温高的房间，可以按照以下步骤进行操作。

1. 根据 `contains` 关系查找建筑物中的楼层。

    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="INOperatorWithout":::

2. 若要查找房间，可以查询建筑物中楼层的集合（以下查询中命名为“楼层”），而不是考虑逐个楼层并运行 `JOIN` 查询查找每个楼层的房间。

    在客户端应用中：
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    在查询中：
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>其他复合查询示例

可以使用合并运算符合并所有上述类型的查询，从而在单个查询中加入更多详细信息。 下面是一些复合查询的其他示例，它们一次查询多种类型的孪生体描述符。

* 从房间 123 拥有的设备中，返回充当操作员角色的 MxChip 设备
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples1":::
* 获取具有“包含”关系的孪生，以及另一个 ID 为 id1 的孪生 
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples2":::
* 获取此房间模型中 floor11 所包含的所有房间
    :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>使用 API 运行查询

在针对查询字符串做出决定后，通过发起对[查询 API](/rest/api/digital-twins/dataplane/query) 的调用来执行该查询。

可以直接调用 API，也可以使用适用于 Azure 数字孪生的其中一个 [SDK](concepts-apis-sdks.md#overview-data-plane-apis)。

以下代码片段说明了客户端应用中的 [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 调用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

此调用中使用的查询返回数字孪生的列表，上述示例中使用 [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) 对象来表示它。 每个查询的数据返回类型取决于使用 `SELECT` 语句指定的术语：
* 以 `SELECT * FROM ...` 开头的查询将返回数字孪生的列表（可以将其序列化为 `BasicDigitalTwin` 对象或已创建的其他自定义数字孪生类型）。
* 以格式 `SELECT <A>, <B>, <C> FROM ...` 开头的查询将返回包含键 `<A>`、`<B>` 和 `<C>` 的字典。
* 可以设计其他格式的 `SELECT` 语句，以返回自定义数据。 可以考虑创建自己的类来处理自定义结果集。 

### <a name="query-with-paging"></a>使用分页查询

查询调用支持分页。 下面的完整示例使用 `BasicDigitalTwin` 作为具有错误处理和分页的查询结果类型：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 数字孪生 API 和 SDK](concepts-apis-sdks.md)，包括本文中用于运行查询的查询 API。
