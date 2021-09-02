---
title: 使用 .NET SDK 在 Azure Cosmos DB 中执行事务性批处理操作
description: 了解如何使用 Azure Cosmos DB .NET SDK 中的 TransactionalBatch 来执行一组会成功或失败的点操作。
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f3bef945fe5e676b221650d9f43a9955e4483133
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113548"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>使用 .NET SDK 在 Azure Cosmos DB 中执行事务性批处理操作
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

事务性批处理描述了一组需要一起成功或失败且在容器中具有相同分区键的点操作。 在 .NET SDK 中，`TransactionalBatch` 类用于定义该批操作。 如果所有操作都按照事务性批处理操作中描述的顺序成功完成，则会提交事务。 但是，如果任何操作失败，则会回滚整个事务。

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>什么是 Azure Cosmos DB 中的事务

典型数据库中的事务可以定义为一系列作为工作的单个逻辑单元执行的操作。 每个事务都提供 ACID（原子性、一致性、隔离性、持久性）属性保证。

* 原子性保证将一个事务内部执行的所有操作视为一个单位，这些操作要么全部提交，要么都不提交。
* 一致性确保数据始终在各个事务之间处于有效状态。
* 隔离性保证不会存在两个事务互相干扰的情况 – 许多商务系统提供多个可以基于应用程序需求使用的隔离级别。
* 持久性确保数据库中提交的任何更改始终存在。
对于同一[逻辑分区键](../partitioning-overview.md)内的操作，Azure Cosmos DB 支持[具有快照隔离功能的完全符合 ACID 的事务](database-transactions-optimistic-concurrency.md)。

## <a name="transactional-batch-operations-vs-stored-procedures"></a>事务性批处理操作与存储过程

Azure Cosmos DB 当前支持存储过程，这些存储过程也提供了操作的事务作用域。 但是，事务性批处理操作具有以下优势：

* 语言选项 - 你使用的 SDK 和语言支持事务性批处理，而存储过程需要使用 JavaScript 进行编写。
* 代码版本控制 - 对应用程序代码进行版本控制，并将其加入到 CI/CD 管道，这比对存储过程的更新进行协调并确保在恰当的时间进行滚动更新要自然得多。 使用此功能，对更改进行回滚也变得更加容易。
* 性能 - 与存储过程执行相比，将等效操作的延迟降低了高达 30%。
* 内容序列化 - 事务性批处理中的每个操作都可以利用其有效负载的自定义序列化选项。

## <a name="how-to-create-a-transactional-batch-operation"></a>如何创建事务性批处理操作

创建事务性批处理操作时，请从容器实例开始并调用 `CreateTransactionalBatch`：

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

接下来需对批处理调用 `ExecuteAsync`：

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

收到响应后，检查其是否成功，并提取结果：

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

如果失败，则失败的操作会有其相应错误的状态代码。 其他所有操作都会有 424 状态代码（失败的依赖项）。 在下面的示例中，操作失败，因为它尝试创建已存在的项 (409 HttpStatusCode.Conflict)。 可通过状态代码查明导致事务失败的原因。

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>事务性批处理操作如何执行

调用 `ExecuteAsync` 方法时，会对 `TransactionalBatch` 对象中的所有操作进行分组，将其序列化为单个有效负载，并将其作为单个请求发送到 Azure Cosmos DB 服务。

服务接收请求并在事务作用域内执行所有操作，然后使用相同的序列化协议返回响应。 此响应是成功或失败，并为每个操作提供各项操作响应。

SDK 会将响应公开，以便你验证结果并根据需要提取每个内部操作结果。

## <a name="limitations"></a>限制

目前有两个已知限制：

* Azure Cosmos DB 请求大小限制约束 `TransactionalBatch` 有效负载的大小不能超过 2 MB，最大执行时间为 5 秒。
* 为了确保性能符合预期并满足 SLA，每个 `TransactionalBatch` 的当前限制为 100 个操作。

## <a name="next-steps"></a>后续步骤

* 详细了解[可以使用 TransactionalBatch 执行的操作](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)

* 访问[示例](sql-api-dotnet-v3sdk-samples.md)来了解使用 Cosmos DB .NET SDK 的更多方式
