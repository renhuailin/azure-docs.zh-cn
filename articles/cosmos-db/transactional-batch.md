---
title: 使用 .NET SDK Azure Cosmos DB 中的事务批处理操作
description: 了解如何使用 Azure Cosmos DB .NET SDK 中的 TransactionalBatch 来执行成功或失败的一组点操作。
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9f6692db2da3722507136a468d1dcbdc2985e73f
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347551"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>使用 .NET SDK Azure Cosmos DB 中的事务批处理操作
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

事务性批处理描述了一组需要在容器中成功或故障转移到同一个分区键的点操作。 在 .NET SDK 中， `TransactionalBatch` 类用于定义此批处理操作。 如果所有操作都按照事务批处理操作中的顺序成功完成，则将提交事务。 但是，如果有任何操作失败，则将回滚整个事务。

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Azure Cosmos DB 中的事务

典型数据库中的事务可以定义为作为单个逻辑工作单元执行的一系列操作。 每个事务提供 ACID (原子性、一致性、隔离和持续性) 属性保证。

* **原子** 性保证在事务内执行的所有操作都被视为单个单元，所有这些操作要么都已提交，要么都不是。
* **一致性** 确保数据始终在各个事务中处于有效状态。
* **隔离** 保证没有两个事务干扰彼此–许多商业系统都提供多个可以基于应用程序需求使用的隔离级别。
* **持久性** 确保在数据库中提交的任何更改将始终存在。
对于同一[逻辑分区键](partitioning-overview.md)中的操作，Azure Cosmos DB 支持[具有快照隔离的完全符合 ACID 的事务](database-transactions-optimistic-concurrency.md)。

## <a name="transactional-batch-operations-vs-stored-procedures"></a>事务性批处理操作与存储过程

Azure Cosmos DB 当前支持存储过程，这些存储过程也提供操作上的事务作用域。 但是，事务批处理操作具有以下优势：

* **语言选项** –事务批支持已在使用的 SDK 和语言，而需要使用 JavaScript 编写存储过程。
* **代码版本控制** –对应用程序代码进行版本控制，并将其加入到 CI/CD 管道上比协调存储过程的更新和确保在正确的时间进行滚动更新更为自然。 它还使回滚更改变得更容易。
* **性能** -与存储过程执行相比，最多可减少30% 的等效操作延迟。
* **内容序列化** –事务批处理中的每个操作都可以对其有效负载使用自定义序列化选项。

## <a name="how-to-create-a-transactional-batch-operation"></a>如何创建事务批处理操作

创建事务性批处理操作时，从一个容器实例开始，然后调用 `CreateTransactionalBatch` ：

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

接下来，需要对该批调用 `ExecuteAsync` ：

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

如果失败，则失败的操作将具有其相应错误的状态代码。 所有其他操作都将具有424状态代码 (失败的依赖项) 。 在下面的示例中，操作失败，因为它尝试创建 (409 HttpStatusCode) 的项。 状态代码可用于识别事务失败的原因。

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

## <a name="how-are-transactional-batch-operations-executed"></a>如何执行事务性批处理操作

`ExecuteAsync`调用方法时，会将对象中的所有操作 `TransactionalBatch` 分组、序列化为单个有效负载，并作为单个请求发送到 Azure Cosmos DB 服务。

服务接收请求并执行事务范围内的所有操作，并使用相同的序列化协议返回响应。 此响应是成功或失败，并为每个操作提供各个操作响应。

SDK 公开响应以验证结果，并根据需要提取每个内部操作结果。

## <a name="limitations"></a>限制

目前有两个已知限制：

* Azure Cosmos DB 请求大小限制将有效负载的大小限制 `TransactionalBatch` 为不超过 2 MB，最大执行时间为5秒。
* 当前限制为每个，最大值为100， `TransactionalBatch` 以确保按预期的性能和 sla 内的性能。

## <a name="next-steps"></a>后续步骤

* 了解有关[TransactionalBatch 可以执行的操作的](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)详细信息

* 请访问我们的 [示例](sql-api-dotnet-v3sdk-samples.md) ，了解使用 COSMOS DB .net SDK 的更多方法
