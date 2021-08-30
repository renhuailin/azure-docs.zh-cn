---
title: 更改 Azure Cosmos DB 的 API for MongoDB 中的流
description: 了解如何更改 Azure Cosmos DB 的 API for MongoDB 中的流来获取对数据所做的更改。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 60d029edc23ef6001f9c750210a7655b8d36b753
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778311"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>更改 Azure Cosmos DB 的 API for MongoDB 中的流
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

可通过更改流 API 在 Azure Cosmos DB 的 API for MongoDB 中获取[更改源](../change-feed.md)支持。 通过使用更改流 API，你的应用程序可以获取对集合或对单个分片中的项的更改。 以后，可以根据结果采取进一步的措施。 对集合中的项所做的更改将按照其修改时间的顺序进行捕获，并按分片键保证排序顺序。

> [!NOTE]
> 若要使用更改流，请使用 3.6 或更高版本的服务器创建 Azure Cosmos DB 的用于 MongoDB 的 API 帐户。 如果对较早版本运行更改流示例，可能会看到“无法识别的管道阶段名称: $changeStream”错误。

## <a name="examples"></a>示例

下面的示例演示如何获取集合中所有项的更改流。 此示例在插入、更新或替换项时创建光标来监视项。 若要获取更改流，需要使用 `$match` 阶段、`$project` 阶段和 `fullDocument` 选项。 当前不支持监视使用更改流删除操作。 作为一种替代方法，你可以对要删除的项添加软标记。 例如，可以在名为“deleted”的项中添加属性。 如果要删除该项，可以将“deleted”设置为 `true` 并在该项上设置 TTL。 由于将“deleted”更新为 `true` 是一项更新，此更改将显示在更改流中。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

# <a name="java"></a>[Java](#tab/java)

以下示例演示如何在 Java 中使用更改流功能，有关完整示例，请参阅此 [GitHub 存储库](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/main/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java)。 此示例还演示如何使用 `resumeAfter` 方法来查找上次读取中的所有更改。 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

## <a name="changes-within-a-single-shard"></a>单个分片中的更改

以下示例演示如何获取对单个分片中的项所做的更改。 此示例获取项的更改，这些项的分片键等于“a”，分片键值等于“1”。 可以让不同的客户端从不同的分片并行读取更改。

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="scaling-change-streams"></a>缩放更改流
大规模使用更改流时，最好均匀分布负载。 利用 [GetChangeStreamTokens 自定义命令](../mongodb/custom-commands.md)在物理分片/分区之间分布负载。

## <a name="current-limitations"></a>当前限制

使用更改流时，以下限制适用：

* 输出文档中尚不支持 `operationType` 和 `updateDescription` 属性。
* 目前支持 `insert`、`update` 和 `replace` 操作类型。 但是，尚不支持删除操作或其他事件。

由于存在这些限制，需要按前面示例中所示指定 $match 阶段、$project 阶段和 fullDocument 选项。

与 Azure Cosmos DB SQL API 中的更改源不同，没有单独的[更改源处理器库](../change-feed-processor.md)来使用更改流，也不需要使用租用容器。 目前不支持使用 [Azure Functions 触发器](../change-feed-functions.md)来处理更改流。

## <a name="error-handling"></a>错误处理。

使用更改流时，支持以下错误代码和消息：

* **HTTP 错误代码 16500** - 当更改流受到限制时，它会返回一个空页。

* **NamespaceNotFound (OperationType 失效)** - 如果对不存在的集合运行更改流，或删除了集合，则会返回 `NamespaceNotFound` 错误。 由于 `operationType` 属性无法在输出文档中返回，因此会返回 `NamespaceNotFound` 错误，而不是 `operationType Invalidate` 错误。

## <a name="next-steps"></a>后续步骤

* [在 Azure Cosmos DB's API for MongoDB 中使用生存时间自动使数据过期](mongodb-time-to-live.md)
* [Azure Cosmos DB's API for MongoDB 中的索引编制](mongodb-indexing.md)
