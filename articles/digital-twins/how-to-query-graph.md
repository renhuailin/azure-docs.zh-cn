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
ms.openlocfilehash: a38af4c942de280e7b1c094885a1ede6774ead56
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433210"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>查询 Azure 数字孪生孪生图

本文提供了查询示例和更详细的说明来介绍如何使用 Azure 数字孪生查询语言查询[孪生图](concepts-twins-graph.md)以获取信息。 （如需获取查询语言简介及其功能的完整列表，请参阅[概念：查询语言](concepts-query-language.md)。）

本文首先提供示例查询，说明数字孪生的查询语言结构和常见查询操作。 然后，本文介绍了在编写查询后如何使用 Azure 数字孪生[查询 API](/rest/api/digital-twins/dataplane/query) 或 [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) 运行查询。

> [!NOTE]
> 如果通过 API 或 SDK 调用运行以下示例查询，则需将查询文本压缩为一行。

## <a name="show-all-digital-twins"></a>显示所有数字孪生

下方是一个基本查询，它将返回实例中所有数字孪生的列表：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>按属性查询

按属性（包括 ID 和元数据）获取数字孪生：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty1":::

> [!NOTE]
> 使用元数据字段 `$dtId` 查询数字孪生体的 ID。

还可以根据是否定义了某个属性来获取孪生。 以下查询可获取定义了 Location 属性的孪生：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty2":::

如[向数字孪生添加标签](how-to-use-tags.md)中所述，这有助于按孪生的 tag 属性获取该孪生。 以下查询可获取标记为 red 的所有孪生：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

还可以根据属性类型获取孪生。 以下查询可获取 Temperature 属性为数字的孪生：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty3":::

>[!TIP]
> 如果属性的类型为 `Map`，则可以直接在查询中使用映射键和值，如下所示：
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>按模型查询

`IS_OF_MODEL` 运算符可用于基于孪生体的[模型](concepts-models.md)进行筛选。

它考虑了[继承](concepts-models.md#model-inheritance)和模型[版本控制](how-to-manage-model.md#update-models)，并且如果指定孪生满足以下任一条件，则它的评估结果为 true：

* 孪生直接实现提供给 `IS_OF_MODEL()` 的模型，并且孪生中的模型的版本号大于或等于提供的模型的版本号
* 孪生实现的模型可扩展提供给 `IS_OF_MODEL()` 的模型，并且孪生的扩展模型版本号大于或等于提供的模型的版本号 

例如，如果查询模型 `dtmi:example:widget;4` 的孪生，则查询将根据版本 4 或更高版本的小组件模型返回所有孪生，并且还将根据从小组件继承的版本 4 或更高版本的任意模型返回孪生   。

`IS_OF_MODEL` 可以采用多个不同的参数，因此本节的其余部分将专门介绍其各种重载选项。

`IS_OF_MODEL` 的最简单用法仅使用 `twinTypeName` 参数：`IS_OF_MODEL(twinTypeName)`。
以下查询示例将值传入此参数：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel1":::

若要在存在多个孪生集合时（例如使用 `JOIN` 时）指定搜索某一孪生集合，请添加 `twinCollection` 参数：`IS_OF_MODEL(twinCollection, twinTypeName)`。
以下查询示例将为此参数添加值：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel2":::

若要进行完全匹配，请添加 `exact` 参数：`IS_OF_MODEL(twinTypeName, exact)`。
以下查询示例将为此参数添加值：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel3":::

你也可以同时传递所有三个参数：`IS_OF_MODEL(twinCollection, twinTypeName, exact)`。
以下查询示例为所有三个参数指定值：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>按关系查询

当基于数字孪生体的关系进行查询时，Azure 数字孪生查询语言有一个特殊的语法。

关系将被拉取到 `FROM` 子句中的查询范围内。 与“经典”SQL 类型语言的重要区别如下：此 `FROM` 子句中的每个表达式均不是表；确切地说，`FROM` 子句表示跨实体关系遍历，并且是使用 `JOIN` 的 Azure 数字孪生版本编写的。

回想一下，由于 Azure 数字孪生[模型](concepts-models.md)功能，关系并不独立于孪生存在。 这意味着 Azure 数字孪生查询语言的 `JOIN` 与常规 SQL `JOIN` 略有不同，因为此处的关系不能独立查询，必须绑定到孪生体。
要弥补这种差异，可在 `JOIN` 子句中使用关键字 `RELATED` 来引用孪生体的一组关系。

下节提供了多个示例进行介绍。

> [!TIP]
> 从概念上讲，此功能模仿以文档为中心的 CosmosDB 功能，可以对文档内的子对象执行 `JOIN`。 CosmosDB 使用 `IN` 关键字来指示 `JOIN` 用于迭代当前上下文文档中的数组元素。

### <a name="relationship-based-query-examples"></a>基于关系的查询示例

要获取包含关系的数据集，请使用单个 `FROM` 语句，然后使用多个 `JOIN` 语句，其中 `JOIN` 语句表示与先前 `FROM` 或 `JOIN` 语句的结果相关的关系。

下方是基于示例关系的查询。 此代码片段选择 ID 属性为“ABC”的所有数字孪生体，以及所有通过“包含”关系与这些数字孪生体相关的数字孪生体 。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship1":::

> [!NOTE]
> 开发人员无需将此 `JOIN` 与 `WHERE` 子句中的键值相关联（也不需要使用 `JOIN` 定义以内联方式指定键值）。 此关联由系统自动计算，因为关系属性本身标识目标实体。

### <a name="query-the-properties-of-a-relationship"></a>查询关系的属性

与数字孪生体通过 DTDL 描述属性的方式类似，关系也可以具有属性。 可根据孪生体关系的属性查询孪生体。
使用 Azure 数字孪生查询语言，可以通过在 `JOIN` 子句中将别名分配给关系来筛选和投影关系。

例如，考虑具有 reportedCondition 属性的 servicedBy 关系 。 在以下查询中，将为此关系提供一个别名“R”，以便引用其属性。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship2":::

在上述示例中，注意 reportedCondition 为何是 servicedBy 关系本身的属性（以及为何不是某些具有 servicedBy 关系的数字孪生的属性）  。

### <a name="query-with-multiple-joins"></a>使用多个 JOIN 查询

单个查询最多支持五个 `JOIN`。 这使你可以一次遍历多个级别的关系。

以下示例说明多联接查询如何获取房间 1 和 2 中照明配电盘所包含的所有灯泡数。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>项目计数

可以使用 `Select COUNT` 子句计算结果集中的项目数：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount1":::

添加 `WHERE` 子句以计算满足特定条件的项目数。 以下示例使用基于孪生模型类型的应用筛选器进行计数（若要详细了解此语法，请参阅下方的[按模型查询](#query-by-model)）：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount2":::

还可以将 `COUNT` 与 `JOIN` 子句结合使用。 以下查询可计算房间 1 和 2 中照明配电盘所包含的所有灯泡的数量：

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>筛选结果：选择排名靠前的项目

可以使用 `Select TOP` 子句在查询中选择多个“排名靠前”的项目。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>筛选结果：使用投影指定返回集

通过在 `SELECT` 语句中使用投影，可以选择查询将返回的列。

>[!NOTE]
>目前，不支持复杂属性。 为确保投影属性有效，请将投影与 `IS_PRIMITIVE` 检查合并。

以下示例说明查询如何使用投影返回孪生和关系。 以下查询对方案中的客户、工厂和边缘进行了投影，其中 ID 为 ABC 的工厂通过 Factory.customer 关系与客户相关联，而该关系则表示为边缘       。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections1":::

你还可以使用投影返回孪生的属性。 以下查询对客户的 Name 属性进行了投影，这些客户通过 Factory.customer 关系与 ID 为 ABC 的工厂相关联    。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections2":::

你还可以使用投影返回关系的属性。 与先前的示例类似，以下查询对客户的 Name 属性进行了投影，这些客户通过 Factory.customer 关系与 ID 为 ABC 的工厂相关联；但现在该查询还返回了该关系的 prop1 和 prop2 两个属性      。 它通过将关系命名为边缘并收集其属性来实现这一点。  

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections3":::

你也可以使用别名来简化使用投影的查询。

以下查询执行与先前示例相同的操作，但会对属性名称采用以下别名：`consumerName`、`first`、`second` 和 `factoryArea`。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections4":::

以下类似查询可查询上述集合，但仅将 Consumer.name 属性投影为 `consumerName`，并将整个工厂投影为孪生 。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>使用 IN 运算符生成高效的查询

通过使用 `IN` 运算符生成孪生数组并进行查询，可以大大减少所需的查询数。 

例如，假设建筑物包含楼层，而楼层包含房间   。 若要搜索建筑物内室温高的房间，可以按照以下步骤进行操作。

1. 根据 `contains` 关系查找建筑物中的楼层。

    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWithout":::

2. 若要查找房间，可以查询建筑物中楼层的集合（以下查询中命名为“楼层”），而不是考虑逐个楼层并运行 `JOIN` 查询查找每个楼层的房间。

    在客户端应用中：
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    在查询中：
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>其他复合查询示例

可以使用合并运算符合并所有上述类型的查询，从而在单个查询中加入更多详细信息。 以下是复合查询的其他部分示例，这些查询一次可查询多种类型的孪生描述符。

* 从房间 123 拥有的设备中，返回充当操作员角色的 MxChip 设备
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples1":::
* 获取具有“包含”关系的孪生，以及另一个 ID 为 id1 的孪生 
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples2":::
* 获取此房间模型中 floor11 所包含的所有房间
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>使用 API 运行查询

确定查询字符串后，可以调用[查询 API](/rest/api/digital-twins/dataplane/query) 来执行它。

可以直接调用 API，也可以使用适用于 Azure 数字孪生的其中一个 [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)。

以下代码片段说明了客户端应用中的 [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) 调用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

此调用中使用的查询返回数字孪生的列表，上述示例中使用 [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin) 对象来表示它。 每个查询的数据返回类型取决于使用 `SELECT` 语句指定的术语：
* 以 `SELECT * FROM ...` 开头的查询将返回数字孪生的列表（可以将其序列化为 `BasicDigitalTwin` 对象或已创建的其他自定义数字孪生类型）。
* 以格式 `SELECT <A>, <B>, <C> FROM ...` 开头的查询将返回包含键 `<A>`、`<B>` 和 `<C>` 的字典。
* 可以设计其他格式的 `SELECT` 语句，以返回自定义数据。 可以考虑创建自己的类来处理自定义程度非常高的结果集。 

### <a name="query-with-paging"></a>使用分页查询

查询调用支持分页。 以下完整示例使用 `BasicDigitalTwin` 作为使用错误处理和分页后的查询结果类型：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 数字孪生 API 和 SDK](how-to-use-apis-sdks.md)，包括本文中用于运行查询的查询 API。
