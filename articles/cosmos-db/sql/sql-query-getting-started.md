---
title: Azure Cosmos DB 中的 SQL 查询入门
description: 了解如何使用 SQL 查询从 Azure Cosmos DB 查询数据。 可以将示例数据上传到 Azure Cosmos DB 中的容器并对其进行查询。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: tisande
ms.openlocfilehash: 2fa13f931801c08fd450e889744b1eb49638ca80
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206244"
---
# <a name="getting-started-with-sql-queries"></a>SQL 查询入门
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

在 Azure Cosmos DB SQL API 帐户中，有两种读取数据的方法：

**点读取** - 可以对单个项 ID 和分区键进行键/值查找。 项 ID 和分区键的组合是键，项本身是值。 对于 1 KB 大小的文档，点读取通常花费 1 个[请求单位](../request-units.md)，且延迟不超过 10 毫秒。 点读取返回单个项。

以下是一些如何使用各个 SDK 进行点读取的示例：

- [.NET SDK](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)
- [Java SDK](/java/api/com.azure.cosmos.cosmoscontainer.readitem#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](/javascript/api/@azure/cosmos/item#read-requestoptions-)
- [Python SDK](/python/api/azure-cosmos/azure.cosmos.containerproxy#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

**SQL 查询** - 可以使用结构化查询语言 (SQL) 作为 JSON 查询语言来编写查询，以查询数据。 查询始终至少花费 2.3 个请求单位，并且与点读取相比，查询的延迟通常更高且变化更大。 查询可以返回许多项。

Azure Cosmos DB 上的大部分读取密集型工作负荷使用点读取和 SQL 查询的组合。 如果只需读取单个项，则点读取比查询成本更低且速度更快。 点读取不需要使用查询引擎来访问数据，并且可以直接读取数据。 当然，不可能所有工作负荷都仅使用点读取来读取数据，因此支持 SQL 作为查询语言和[架构不可知索引编制](../index-overview.md)方式提供了一种更灵活的数据访问方法。

以下是一些如何使用各个 SDK 进行 SQL 查询的示例：

- [.NET SDK](../sql-api-dotnet-v3sdk-samples.md#query-examples)
- [Java SDK](../sql-api-java-sdk-samples.md#query-examples)
- [Node.js SDK](../sql-api-nodejs-samples.md#item-examples)
- [Python SDK](../sql-api-python-samples.md#item-examples)

本文档的其余部分说明如何开始在 Azure Cosmos DB 中编写 SQL 查询。 可以通过 SDK 或 Azure 门户运行 SQL 查询。

## <a name="upload-sample-data"></a>上传示例数据

在 SQL API Cosmos DB 帐户中，打开[数据资源管理器](../data-explorer.md)以创建名为 `Families` 的容器。 创建容器后，使用数据结构浏览器来查找并打开它。 在 `Families` 容器中，你会在容器名称下面看到 `Items` 选项。 打开此选项，你会在屏幕中心的菜单栏中看到一个按钮，创建“新建项”。 你将使用此功能创建下面的 JSON 项。

### <a name="create-json-items"></a>创建 JSON 项

以下 2 个 JSON 项是关于 Andersen 和 Wakefield 系列的文档。 它们包括家长、孩子及其宠物、地址和注册信息。 

第一个项包含字符串、数字、布尔、数组和嵌套属性：

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

第二个项使用 `givenName` 和 `familyName`，而不是使用 `firstName` 和 `lastName`：

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>查询 JSON 项

尝试对此 JSON 数据执行一些查询来了解 Azure Cosmos DB 的 SQL 查询语言的一些重要方面。

以下查询返回其中的 `id` 字段与 `AndersenFamily` 匹配的项。 由于它是一个 `SELECT *` 查询，因此该查询的输出是完整的 JSON 项。 有关 SELECT 语法的详细信息，请参阅 [SELECT 语句](sql-query-select.md)。

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

查询结果为：

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

以下查询将 JSON 输出的格式重新设置为不同的形式。 当地址中的城市名称与州名称相同时，该查询将使用两个选定的字段 `Name` 和 `City` 来投影新的 JSON `Family` 对象。 “NY, NY”符合这种情况。

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

查询结果为：

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

以下查询返回家庭中 `id` 匹配 `WakefieldFamily` 的所有孩子的名字，按城市排序。

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

其结果是：

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>备注

上述示例演示了 Cosmos DB 查询语言的几个方面：  

* 由于 SQL API 适用于 JSON 值，因此它可以处理三种形式的实体，而不是行和列。 可以引用任意深度的树节点（例如 `Node1.Node2.Node3…..Nodem`），类似于 ANSI SQL 中的 `<table>.<column>` 的两部分引用。

* 由于查询语言适用于无架构数据，因此，必须动态绑定类型系统。 相同的表达式在不同项上可能会产生不同的类型。 查询的结果是有效的 JSON 值，但不保证它是固定的架构。  

* Azure Cosmos DB 仅支持严格的 JSON 项。 类型系统和表达式仅限于处理 JSON 类型。 有关详细信息，请参阅 [JSON 规范](https://www.json.org/)。  

* Cosmos 容器是 JSON 项的一个无架构集合。 容器项内部以及跨容器项的关系是按包含关系隐式捕获的，而不是按主键和外键关系捕获的。 此功能对于 [Azure Cosmos DB 中的联接](sql-query-join.md)中所述的项内联接很重要。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](../introduction.md)
- [Azure Cosmos DB .NET 示例](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 子句](sql-query-select.md)
