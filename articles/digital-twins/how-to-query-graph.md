---
title: 查询孪生图
titleSuffix: Azure Digital Twins
description: 有关信息，请参阅如何查询 Azure 数字孪生克隆图。
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: df7462cf047dd113c34669d9a5f68f2589cc50f4
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672987"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>查询 Azure 数字孪生克隆图形

本文提供了有关使用 **Azure 数字孪生查询语言** 查询克隆 [图形](concepts-twins-graph.md) 的信息的查询示例和更详细说明。  (以了解查询语言和其功能的完整列表，请参阅 [*概念：查询语言*](concepts-query-language.md)。 ) 

本文从示例查询开始，其中阐释了用于数字孪生的查询语言结构和常见查询操作。 然后，它介绍了如何使用 Azure 数字孪生 [查询 API](/rest/api/digital-twins/dataplane/query) 或 [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)在编写查询后运行查询。

> [!TIP]
> 如果使用 API 或 SDK 调用来运行下面的示例查询，则需要将查询文本紧缩到一行中。

## <a name="show-all-digital-twins"></a>显示所有数字孪生

下面是一个基本查询，它将返回实例中所有数字孪生的列表：

```sql
SELECT *
FROM DIGITALTWINS
```

## <a name="query-by-property"></a>按属性查询

获取数字孪生按 **属性** (包括 ID 和元数据) ：

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> 使用元数据字段查询数字克隆的 ID `$dtId` 。

还可以基于 **是否定义了某个属性** 来获取孪生。 下面是一个查询，用于获取具有定义的 *Location* 属性的孪生：

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

这可以帮助你按 *标记* 属性获取孪生，如 [向数字孪生添加标记](how-to-use-tags.md)中所述。 下面是一个查询，用于获取用 *red* 标记的所有孪生：

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(tags.red)
```

还可以基于 **属性的类型** 来获取孪生。 下面是一个可获取孪生的查询，其 *温度* 属性为数字：

```sql
SELECT * FROM DIGITALTWINS T WHERE IS_NUMBER(T.Temperature)
```

## <a name="query-by-model"></a>按模型查询

`IS_OF_MODEL`运算符可用于基于克隆的 [**模型**](concepts-models.md)进行筛选。

如果克隆满足以下任一条件，则它会考虑 [继承](concepts-models.md#model-inheritance) 和模型 [版本控制](how-to-manage-model.md#update-models)，并对给定的双子值求值结果为 **true** ：

* 该整数直接实现了提供给的模型 `IS_OF_MODEL()` ，而克隆上的模型的版本号 *大于或等于* 提供的模型的版本号
* "克隆" 实现了一个模型，该模型将提供给的模型进行 *扩展* `IS_OF_MODEL()` ，而克隆的扩展模型版本号 *大于或等于* 提供的模型的版本号

例如，如果您查询了模型的孪生 `dtmi:example:widget;4` ，则查询将返回基于 **小组件** 模型 **版本4或更高版本** 的所有孪生，还基于 **从小组件继承** 的任何模型的版本 **4 或更高** 版本孪生。

`IS_OF_MODEL` 可以采用几个不同的参数，本部分的其余部分专用于其不同的重载选项。

最简单的用法 `IS_OF_MODEL` 仅采用 `twinTypeName` 参数： `IS_OF_MODEL(twinTypeName)` 。
下面是传递此参数中的值的查询示例：

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1')
```

若要在有多个 (时指定要搜索的非克隆集合（如 `JOIN`) 使用时一样），请添加 `twinCollection` 参数： `IS_OF_MODEL(twinCollection, twinTypeName)` 。
下面是添加此参数值的查询示例：

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1')
```

若要执行完全匹配，请添加 `exact` 参数： `IS_OF_MODEL(twinTypeName, exact)` 。
下面是添加此参数值的查询示例：

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1', exact)
```

你还可以将所有这三个参数同时传递： `IS_OF_MODEL(twinCollection, twinTypeName, exact)` 。
下面是一个用于为所有三个参数指定值的查询示例：

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1', exact)
```

## <a name="query-by-relationship"></a>按关系查询

当基于数字孪生 **关系** 进行查询时，Azure 数字孪生查询语言具有特殊的语法。

关系将被提取到子句中的查询范围内 `FROM` 。 "经典" SQL 类型语言中的一个重要区别在于，此子句中的每个表达式 `FROM` 不是一个表; 相反， `FROM` 子句表示跨实体关系遍历，并使用 Azure 数字孪生版本编写 `JOIN` 。

回忆一下，通过 Azure 数字孪生 [模型](concepts-models.md) 功能，关系不能独立于孪生存在。 这意味着，Azure 数字孪生查询语言与 `JOIN` 常规 SQL 略有不同 `JOIN` ，因为此处的关系不能单独查询，必须绑定到一个完全不同的位置。
为了引入这种差异， `RELATED` 子句中使用关键字 `JOIN` 来引用克隆的一组关系。

以下部分提供了此类外观的几个示例。

> [!TIP]
> 从概念上讲，这项功能模拟了 CosmosDB 的以文档为中心的功能， `JOIN` 可以在文档中的子对象上执行。 CosmosDB 使用 `IN` 关键字指示用于 `JOIN` 循环访问当前上下文文档内的数组元素。

### <a name="relationship-based-query-examples"></a>基于关系的查询示例

若要获取包含关系的数据集，请使用单个 `FROM` 语句，后跟 N `JOIN` 个语句，其中的 `JOIN` 语句表示上一个或语句的结果上的关系 `FROM` `JOIN` 。

下面是一个基于关系的示例查询。 此代码段选择 *ID* 属性为 "ABC" 的所有数字孪生，并通过 *包含* 关系与这些数字孪生相关的所有数字孪生。

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

> [!NOTE]
> 开发人员无需将此 `JOIN` 与子句中的键值相关联 `WHERE` (或使用定义) 以内联方式指定键值 `JOIN` 。 此关联由系统自动计算，因为关系属性本身标识目标实体。

### <a name="query-the-properties-of-a-relationship"></a>查询关系的属性

类似于通过 DTDL 描述的数字孪生的方式，关系也可以具有属性。 您可以基于孪生的 **关系的属性** 来查询。
使用 Azure 数字孪生查询语言，可以通过将别名分配给子句内的关系，来筛选和投影关系 `JOIN` 。

例如，假设有一个具有 *reportedCondition* 属性的 *servicedBy* 关系。 在下面的查询中，为此关系提供了一个 "R" 别名，以便引用其属性。

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC'
AND R.reportedCondition = 'clean'
```

在上面的示例中，请注意， *reportedCondition* 是 *servicedBy* 关系本身的属性， (不属于具有 *servicedBy* 关系) 的某些数字克隆。

### <a name="query-with-multiple-joins"></a>具有多个联接的查询

单个查询最多支持五个 `JOIN` 。 这使您可以一次遍历多个级别的关系。

下面是多联接查询的一个示例，它获取房间1和2中的灯具面板中包含的所有光源电灯泡。

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="count-items"></a>计数项

您可以使用子句来计算结果集中的项数 `Select COUNT` ：

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

添加 `WHERE` 子句以计算符合特定条件的项的数目。 下面是使用基于每个克隆模型类型的已应用筛选器进行计数的示例 (有关此语法的详细信息，请参阅下面的 [*按模型查询*](#query-by-model)) ：

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

您还可以将 `COUNT` 与子句一起使用 `JOIN` 。 下面是一个查询，它对房间1和2的灯具面板中包含的所有光源电灯泡进行计数：

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="filter-results-select-top-items"></a>筛选结果：选择前几项

您可以使用子句在查询中选择多个 "top" 项 `Select TOP` 。

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

## <a name="filter-results-specify-return-set-with-projections"></a>筛选结果：指定具有投影的返回集

通过在语句中使用投影 `SELECT` ，您可以选择查询将返回的列。

>[!NOTE]
>此时不支持复杂属性。 若要确保投影属性有效，请将投影与支票组合在一起 `IS_PRIMITIVE` 。

下面是使用投影返回孪生和关系的查询示例。 下面的查询从一个方案投影 *使用者*、*工厂* 和 *边缘*，其中 ID 为 *ABC* 的 *工厂* 通过 *factory* 的关系与 *使用者* 相关，而该关系显示为 *边缘*。

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

你还可以使用投影来返回克隆的属性。 下面的 *查询使用 ID* 为 *ABC* 的 *使用者* 的 "*名称*" 属性，通过 "*工厂*" 的关系来项目。

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

您还可以使用投影来返回关系的属性。 与前面的示例类似，下面的查询使用 ID 为 *ABC* 到工厂的关系，将 *与工厂* 相关的 *使用者* 的 *Name* 属性进行投影 *。* 但现在，它还返回该关系的两个属性： *prop1* 和 *prop2*。 它通过命名关系 *边缘* 并收集其属性来实现此功能。  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

还可以使用别名来简化使用投影的查询。

下面的查询执行与上一示例相同的操作，但它将属性名称的别名为 `consumerName` 、、 `first` `second` 和 `factoryArea` 。

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

下面是一个类似的查询，它查询与上述相同的集，但仅将 *Consumer.name* 属性投影为 `consumerName` ，并将整个 *工厂* 投影为一个克隆。

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

## <a name="build-efficient-queries-with-the-in-operator"></a>用 IN 运算符生成高效查询

您可以通过生成孪生数组并使用运算符查询来大幅减少所需的查询数 `IN` 。 

例如，假设 *建筑物* 包含 *楼层* ， *地面* 包含 *房间*。 若要在一座热的建筑物中搜索房间，一种方法是执行以下步骤。

1. 根据关系在大楼中查找楼层 `contains` 。

    ```sql
    SELECT Floor
    FROM DIGITALTWINS Building
    JOIN Floor RELATED Building.contains
    WHERE Building.$dtId = @buildingId
    ```

2. 若要查找房间，而不是逐个考虑并运行 `JOIN` 查询来查找每个房间的房间，可以在下面的查询中使用名为 *Floor* 的建筑物 (中的楼层集合进行查询) 。

    在客户端应用中：
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    在查询中：
    
    ```sql
    
    SELECT Room
    FROM DIGITALTWINS Floor
    JOIN Room RELATED Floor.contains
    WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
    AND Room. Temperature > 72
    AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
    
    ```

## <a name="other-compound-query-examples"></a>其他复合查询示例

您可以使用组合运算符 **组合** 以上任意类型的查询，以便在单个查询中包含更多详细信息。 下面是一些其他查询多个类型为一次的克隆说明符的复合查询示例。

| 说明 | 查询 |
| --- | --- |
| 在 *房间 123* 具有的设备中，返回服务于操作员角色的 MxChip 设备 | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contoso:com:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| 获取具有名为 *id1* 的关系 *的孪生* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| 获取 *floor11* 包含的此聊天室模型的所有聊天室 | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contoso:com:DigitalTwins:Room;1')` |

## <a name="run-queries-with-the-api"></a>通过 API 运行查询

一旦您决定了查询字符串，就可以通过调用 [**查询 API**](/rest/api/digital-twins/dataplane/query)来执行它。

可以直接调用 API，也可以使用适用于 Azure 数字孪生的 [sdk](how-to-use-apis-sdks.md#overview-data-plane-apis) 之一。

下面的代码段演示了从客户端应用程序 [) SDK 调用的 .net (c #](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) ：

```csharp
    string adtInstanceEndpoint = "https://<your-instance-hostname>";

    var credential = new DefaultAzureCredential();
    DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceEndpoint), credential);

    // Run a query for all twins   
    string query = "SELECT * FROM DIGITALTWINS";
    AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
```

此调用以 [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) 对象的形式返回查询结果。

查询调用支持分页。 下面是一个完整的示例，该示例使用 `BasicDigitalTwin` 作为查询结果类型以及错误处理和分页：

```csharp
try
{
    await foreach(BasicDigitalTwin twin in result)
        {
            // You can include your own logic to print the result
            // The logic below prints the twin's ID and contents
            Console.WriteLine($"Twin ID: {twin.Id} \nTwin data");
            IDictionary<string, object> contents = twin.Contents;
            foreach (KeyValuePair<string, object> kvp in contents)
            {
                Console.WriteLine($"{kvp.Key}  {kvp.Value}");
            }
        }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 数字孪生 api 和 sdk](how-to-use-apis-sdks.md)的详细信息，包括用于运行本文中查询的查询 API。
