---
title: 开始在 Visual Studio（云服务）中使用表存储
description: 在使用 Visual Studio 连接服务连接到存储帐户后，如何开始在 Visual Studio 的云服务项目中使用 Azure 表存储
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cad09a5359bd5d9bd2041eb92d0e994adf80080
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823083"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>开始使用 Azure 表存储和 Visual Studio 连接服务（云服务项目）
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>概述

[!INCLUDE [Cloud Services (classic) deprecation announcement](../cloud-services/includes/deprecation-announcement.md)]


本文介绍通过使用 Visual Studio 中的“添加连接服务”对话框在云服务项目中创建或引用 Azure 存储帐户之后，如何开始在 Visual Studio 中使用 Azure 表存储。 执行“添加连接服务”操作会安装相应的 NuGet 程序包，以访问项目中的 Azure 存储，并将存储帐户的连接字符串添加到项目配置文件中。

Azure 表存储服务使用户可以存储大量结构化数据。 该服务是一个 NoSQL 数据存储，接受来自 Azure 云内部和外部的通过验证的呼叫。 Azure 表最适合存储结构化非关系型数据。

若要开始，首先需要在存储帐户中创建表。 我们将展示如何使用代码创建 Azure 表，以及如何执行基本的表和实体操作，例如添加、修改、读取和删除表实体。 示例是使用 C\# 代码编写的并使用了[适用于 .NET 的 Microsoft Azure 存储客户端库](/previous-versions/azure/dn261237(v=azure.100))。

**注意：** 执行 Azure 存储调用的一些 API 是异步的。 有关详细信息，请参阅[使用 Async 和 Await 进行异步编程](/previous-versions/hh191443(v=vs.140))。 下面的代码假定正在使用异步编程方法。

* 有关以编程方式操作表的详细信息，请参阅 [Get started with Azure Table storage using .NET](../cosmos-db/tutorial-develop-table-dotnet.md)（通过 .NET 开始使用 Azure 表存储）。
* 有关 Azure 存储的常规信息，请参阅[存储文档](https://azure.microsoft.com/documentation/services/storage/)。
* 有关 Azure 云服务的常规信息，请参阅[云服务文档](https://azure.microsoft.com/documentation/services/cloud-services/)。
* 有关编写 ASP.NET 应用程序的详细信息，请参阅 [ASP.NET](https://www.asp.net)。

## <a name="access-tables-in-code"></a>使用代码访问表
若要在云服务项目中访问表，需要在访问 Azure 表存储的任何 C# 源文件中包含以下项。

1. 请确保 C# 文件顶部的命名空间声明包括以下 **using** 语句。
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Azure.Data.Table;
    using System.Collections.Generic
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. 获取一个 AzureStorageConnectionString 对象，用于创建一个执行帐户级别操作（例如创建和删除表）的 TableServiceClient。
   
    ```csharp
    string storageConnString = "_AzureStorageConnectionString"
    ```

   > [!NOTE]
   > 在下列示例中，在代码的前面使用上述全部代码。
   
3. 获取 TableServiceClient 对象，以引用存储帐户中的表对象。
   
    ```csharp
    // Create the table service client.
    TableServiceClient tableServiceClient = new TableServiceClient(storageConnString);
    ```

4. 获取 TableClient 引用对象，以引用特定的表和实体。
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    TableClient peopleTable = tableServiceClient.GetTableClient("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>使用代码创建表
若要创建 Azure 表，只需在获取 TableClient 对象后添加对 CreateIfNotExistsAsync 的调用，如“使用代码访问表”部分所述。

```csharp
// Create the TableClient if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>将实体添加到表
要将实体添加到表，请创建用于定义实体的属性的类。 以下代码定义了将客户的名字和姓氏分别用作行键和分区键的 **CustomerEntity** 实体类。

```csharp
public class CustomerEntity : ITableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

涉及实体的 AddEntity 操作是使用前面在“使用代码访问表”中创建的 TableClient 对象完成的。 AddEntity 方法表示要执行的操作。 以下代码示例演示如何创建 TableClient 对象和 CustomerEntity 对象。 为准备此操作，AddEntity 会将客户实体插入表中。

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Inserts the customer entity.
peopleTable.AddEntity(customer1)
```


## <a name="insert-a-batch-of-entities"></a>插入一批实体
可以通过单个写入操作将多个实体插入表中。 以下代码示例会创建两个实体对象（“Jeff Smith”和“Ben Smith”），并使用 AddRange 方法将它们添加到 addEntitiesBatch 对象，然后通过调用 TableClient.SubmitTransactionAsync 来启动操作。

```csharp
// Create a list of 2 entities with the same partition key.
List<CustomerEntity> entityList = new List<CustomerEntity>
{
    new CustomerEntity("Smith", "Jeff")
    {
        { "Email", "Jeff@contoso.com" },
        { "PhoneNumber", "425-555-0104" }
    },
    new CustomerEntity("Smith", "Ben")
    {
        { "Email", "Ben@contoso.com" },
        { "PhoneNumber", "425-555-0102" }
    },
};

// Create the batch.
List<TableTransactionAction> addEntitiesBatch = new List<TableTransactionAction>();

// Add the entities to be added to the batch.
addEntitiesBatch.AddRange(entityList.Select(e => new TableTransactionAction(TableTransactionActionType.Add, e)));

// Submit the batch.
Response<IReadOnlyList<Response>> response = await peopleTable.SubmitTransactionAsync(addEntitiesBatch).ConfigureAwait(false);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>获取分区中的所有实体
若要查询表以获取某个分区中的所有实体，请使用 Query 方法。 以下代码示例指定了一个筛选器，以筛选分区键为“Smith”的实体。 此示例会将查询结果中每个实体的字段输出到控制台。

```csharp
Pageable<CustomerEntity> queryResultsFilter = peopleTable.Query<CustomerEntity>(filter: "PartitionKey eq 'Smith'");

// Print the fields for each customer.
foreach (CustomerEntity qEntity in queryResultsFilter)
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", qEntity.PartitionKey, qEntity.RowKey, qEntity.Email, qEntity.PhoneNumber);
}
```


## <a name="get-a-single-entity"></a>获取单个实体
可以编写查询以获取单个特定实体。 以下代码使用 GetEntityAsync 方法来指定名为“Ben Smith”的客户。 此方法仅返回一个实体，而不是一个集合，而 GetEntityAsync.Result 中的返回值则是一个 CustomerEntity 对象。 在查询中指定分区键和行键是从 **表** 服务中检索单个实体的最快方法。

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

// Print the phone number of the result.
if (singleResult.Result != null)
    Console.WriteLine(((CustomerEntity)singleResult.Result).PhoneNumber);
else
    Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>删除条目
可以在找到实体后将其删除。 以下代码查找名为“Ben Smith”的客户实体，如果找到，会将其删除。

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

CustomerEntity deleteEntity = (CustomerEntity)singleResult.Result;

// Delete the entity given the partition and row key.
if (deleteEntity != null)
{
    await peopleTable.DeleteEntity(deleteEntity.PartitionKey, deleteEntity.RowKey);

    Console.WriteLine("Entity deleted.");
}

else
    Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>后续步骤
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
