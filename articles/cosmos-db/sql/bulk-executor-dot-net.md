---
title: 使用批量执行程序 .NET 库在 Azure Cosmos DB 中执行批量导入和更新操作
description: 使用批量执行程序 .NET 库，批量导入和更新 Azure Cosmos DB 文档。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 57d61d8f1a1de83349d844aa06d62ebbff6c0de5
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113660"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>使用批量执行程序 .NET 库在 Azure Cosmos DB 中执行批量操作
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!NOTE]
> 本文介绍的这个批量执行工具库是为使用 .NET SDK 2.x 版本的应用程序保留的。 对于新应用程序，可以使用 [.NET SDK 版本 3.x](tutorial-sql-api-dotnet-bulk-import.md) 直接提供的批量支持  ，它不需要任何外部库。 

> 如果你当前正在使用批量执行工具库，并计划迁移到更新 SDK 上的批量支持，请使用[迁移指南](how-to-migrate-from-bulk-executor-library.md)中的步骤来迁移应用程序。

本教程提供有关使用批量执行程序 .NET 库在 Azure Cosmos 容器中导入和更新文档的说明。 若要了解批量执行程序库及它如何帮助你利用大量吞吐量和存储，请参阅[批量执行程序库概述](../bulk-executor-overview.md)一文。 本教程将讲解一个示例 .NET 应用程序，该应用程序可将随机生成的文档批量导入 Azure Cosmos 容器。 导入之后，它会显示如何通过指定要对特定文档字段执行的修补操作，来批量更新导入的数据。

目前，批量执行程序库仅受 Azure Cosmos DB SQL API 和 Gremlin API 帐户支持。 本文介绍如何配合使用 SQL API 帐户和批量执行程序 .NET 库。 若要了解如何配合使用 Gremlin API 帐户和批量执行程序 .NET 库，请参阅[在 Azure Cosmos DB Gremlin API 中执行批量操作](../graph/bulk-executor-graph-dotnet.md)。

## <a name="prerequisites"></a>先决条件

* 如果尚未安装 Visual Studio 2019，可以下载并使用 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 无需 Azure 订阅即可[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，也无需缴纳费用或承诺金。 或者，可以通过 `https://localhost:8081` 终结点使用 [Azure Cosmos DB 仿真器](../local-emulator.md)。 [对请求进行身份验证](../local-emulator.md#authenticate-requests)中提供了主密钥。

* 使用 .NET 快速入门文章的[创建数据库帐户](create-sql-api-dotnet.md#create-account)部分所述的步骤创建 Azure Cosmos DB SQL API 帐户。

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，我们从 GitHub 下载示例 .NET 应用程序来接着处理代码。 此应用程序针对 Azure Cosmos 帐户中存储的数据执行批量操作。 若要克隆该应用程序，请打开命令提示符，导航到要将该应用程序复制到的目录，然后运行以下命令：

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

克隆的存储库包含两个示例：“BulkImportSample”和“BulkUpdateSample”。 可以打开任一示例应用程序，使用 Azure Cosmos DB 帐户的连接字符串更新 App.config 文件中的连接字符串，生成解决方案，然后运行它。

“BulkImportSample”应用程序生成随机文档并将其批量导入 Azure Cosmos 帐户。 “BulkUpdateSample”应用程序通过指定要对特定文档字段执行的修补操作，来批量更新导入的文档。 在后续部分，我们将查看其中每个示例应用中的代码。

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>将数据批量导入到 Azure Cosmos 帐户

1. 导航到“BulkImportSample”文件夹并打开“BulkImportSample.sln”文件。  

2. 从 App.config 文件检索 Azure Cosmos DB 的连接字符串，如以下代码所示：  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   批量导入程序会创建新的数据库和容器，并在 App.config 文件中指定数据库名称、容器名称与吞吐量值。

3. 接下来，使用直接 TCP 连接模式初始化 DocumentClient 对象：  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. 根据等待时间和限制请求使用较大重试值初始化 BulkExecutor 对象。 然后，这些值将设置为 0，以将阻塞控制权传递给 BulkExecutor（在其生存期内都会保留此控制权）。  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. 应用程序调用 BulkImportAsync API。 .NET 库提供批量导入 API 的两个重载 - 一个重载接受序列化的 JSON 文档列表，另一个重载接受反序列化的 POCO 文档列表。 若要详细了解其中每个重载方法的定义，请参阅 [API 文档](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync)。

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync 方法接受以下参数：**
   
   |**参数**  |**说明** |
   |---------|---------|
   |enableUpsert    |   用于对文档启用更新插入操作的标志。 如果已存在具有给定 ID 的文档，则会更新该文档。 此值默认设置为 false。      |
   |disableAutomaticIdGeneration    |    用于禁用自动生成 ID 的标志。 此值默认设置为 true。     |
   |maxConcurrencyPerPartitionKeyRange    | 每个分区键范围的最大并发度，设置为 null 会导致库使用默认值 20。 |
   |maxInMemorySortingBatchSize     |  从在每个阶段中传递给 API 调用的文档枚举器提取的最大文档数。 对于在批量导入之前发生的内存中排序阶段，将此参数设置为 null 会导致库使用默认最小值 (documents.count, 1000000)。       |
   |cancellationToken    |    用于正常退出批量导入操作的取消令牌。     |

   **批量导入响应对象定义** 批量导入 API 调用的结果包含以下属性：

   |**参数**  |**说明**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  从提供给批量导入 API 调用的总文档数中成功导入的文档总数。       |
   |TotalRequestUnitsConsumed (double)   |   批量导入 API 调用消耗的请求单位 (RU) 总数。      |
   |TotalTimeTaken (TimeSpan)    |   批量导入 API 调用完成执行所花费的总时间。      |
   |BadInputDocuments (List\<object>)   |     未在批量导入 API 调用中成功导入的格式不当文档列表。 修复返回的文档，并重试导入。 格式不当的文档包括其 ID 值不是字符串（null 或其他任何数据类型被视为无效）的文档。    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>批量更新 Azure Cosmos 帐户中的数据

可以使用 BulkUpdateAsync API 更新现有文档。 此示例将 `Name` 字段设置为新值，并从现有文档中删除 `Description` 字段。 有关完整的受支持更新操作集，请参阅 [API 文档](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate)。

1. 导航到“BulkUpdateSample”文件夹并打开“BulkUpdateSample.sln”文件。  

2. 定义更新项以及相应的字段更新操作。 此示例使用 `SetUpdateOperation` 更新 `Name` 字段，并使用 `UnsetUpdateOperation` 从所有文档中删除 `Description` 字段。 还可以执行其他操作，例如，根据特定的值递增文档字段、将特定的值推送到数组字段，或者从数组字段中删除特定的值。 若要了解批量更新 API 提供的不同方法，请参阅 [API 文档](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate)。

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. 应用程序调用 BulkUpdateAsync API。 若要了解 BulkUpdateAsync 方法的定义，请参阅 [API 文档](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync)。  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync 方法接受以下参数：**

   |**参数**  |**说明** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   每个分区键范围的最大并发度，将此参数设置为 null 会让库使用默认值 (20)。   |
   |maxInMemorySortingBatchSize    |    从在每个阶段中传递给 API 调用的更新项枚举器提取的最大更新项数。 对于批量导入之前发生的每个内存中排序阶段，将此参数设置为 null 会导致库使用默认最小值 (updateItems.count, 1000000)。     |
   | cancellationToken|用于正常退出批量更新操作的取消令牌。 |

   **批量更新响应对象定义** 批量更新 API 调用的结果包含以下属性：

   |**参数**  |**说明** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   从提供给批量更新 API 调用的总文档数中成功更新的文档数。      |
   |TotalRequestUnitsConsumed (double)   |    批量更新 API 调用消耗的请求单位 (RU) 总数。    |
   |TotalTimeTaken (TimeSpan)   | 批量更新 API 调用完成执行所花费的总时间。 |
    
## <a name="performance-tips"></a>性能提示 

使用批量执行程序库时，请注意以下几点，以获得更好的性能：

* 为获得最佳性能，请从 Azure Cosmos 帐户写入区域中的 Azure 虚拟机运行应用程序。  

* 建议在单个虚拟机中，为整个应用程序实例化对应于特定 Azure Cosmos 容器的单个 `BulkExecutor` 对象。  

* 原因是单个批量操作 API 执行会消耗客户端计算机的大量 CPU 和网络 IO（发生这种情况的原因是在内部生成了多个任务）。 在执行批量操作 API 调用的应用程序进程中，请避免生成多个并发任务。 如果单个虚拟机上运行的单个批量操作 API 调用无法占用整个容器的吞吐量（如果容器吞吐量超过 100 万 RU/秒），最好是创建独立的虚拟机来并发执行批量操作 API 调用。  

* 确保在实例化 BulkExecutor 对象之后调用 `InitializeAsync()` 方法，以提取目标 Cosmos 容器的分区映射。  

* 在应用程序的 App.Config 中，确保启用 **gcServer** 以获得更好的性能
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* 库会发出跟踪，可将其收集到日志文件或控制台。 若要启用上述两项功能，请将以下代码添加到应用程序的 App.Config 文件。

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>后续步骤

* 若要了解 NuGet 包的详细信息和发行说明，请参阅[批量执行程序 SDK 详细信息](sql-api-sdk-bulk-executor-dot-net.md)。
