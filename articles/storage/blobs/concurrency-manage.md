---
title: 在 Blob 存储中管理并发
titleSuffix: Azure Storage
description: 了解如何通过在应用程序中实现乐观或悲观并发，将多个编写器管理到 blob。 乐观并发检查 blob 的 ETag 值，并将其与提供的 ETag 进行比较。 悲观并发使用独占租约将 blob 锁定到其他编写器。
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523406"
---
# <a name="managing-concurrency-in-blob-storage"></a>在 Blob 存储中管理并发

新式应用程序通常具有多个用户同时查看和更新数据。 应用程序开发人员需要认真考虑如何向最终用户提供可预测的体验，尤其是在多个用户可以更新相同数据的情况下。 开发人员通常考虑下面三个主要数据并发策略：  

- **开放式并发**：执行更新的应用程序将作为其更新的一部分，确定自应用程序上次读取该数据后数据是否已更改。 例如，如果两名查看 wiki 页的用户对该页面进行更新，则 wiki 平台必须确保第二次更新不会覆盖第一次更新。 它还必须确保两个用户都了解他们的更新是否成功。 此策略最常用于 Web 应用程序中。

- **悲观并发**：要执行更新的应用程序将对对象进行锁定，以防其他用户更新数据，直到锁定被释放。 例如，在主/辅助数据复制方案中，仅在主要副本执行更新的情况下，主数据库通常会长时间保留数据的排他锁，以确保任何其他人都不能对其进行更新。

- **最后一个编写器入选**：允许更新操作继续进行，而无需首先确定其他应用程序是否已在读取后更新数据的方法。 当对数据进行分区时，通常使用这种方法，使多个用户不会同时访问相同的数据。 该策略可能还适用于正在处理短期数据流的情况。

Azure 存储支持所有三个策略，但它在为开放式和悲观并发提供完全支持方面有独特的功能。 Azure 存储空间旨在采用强大的一致性模型，保证在服务执行插入或更新操作后，后续读取操作返回最新更新。

除了选择相应的并发策略外，开发人员还应了解存储平台如何隔离更改，尤其是跨事务对同一对象进行更改。 Azure 存储使用快照隔离允许在单个分区内同时与写入操作并发读取操作。 快照隔离可保证所有读取操作在进行更新时也会返回一致的数据快照。

您可以选择使用乐观并发模型或悲观并发模型来管理对 blob 和容器的访问。 如果未明确指定策略，则默认情况下，最后一个编写器入选。  

## <a name="optimistic-concurrency"></a>乐观并发

Azure 存储为存储的每个对象分配一个标识符。 每次对对象执行写入操作时，都会更新此标识符。 该标识符作为 HTTP GET 响应的一部分返回到客户端，该标头由 HTTP 协议定义。

执行更新的客户端可以将原始 ETag 与条件标头一起发送，以确保仅在满足特定条件时才会进行更新。 例如，如果指定了 **if-match** 标头，Azure 存储将验证更新请求中指定的 etag 值是否与要更新的对象的 etag 相同。 有关条件标头的详细信息，请参阅为 [Blob 服务操作指定条件标头](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)。

此进程的概述如下：  

1. 从 Azure 存储检索 blob。 响应包括用于标识对象的当前版本的 HTTP ETag 标头值。
1. 在更新 blob 时，请在写入请求的 **if-match** 条件标头中包括你在步骤1中收到的 ETag 值。 Azure 存储将请求中的 ETag 值与 blob 的当前 ETag 值进行比较。
1. 如果 blob 的当前 ETag 值与请求上提供的 **if-match** 条件标头中指定的值不同，则 Azure 存储将返回 HTTP 状态代码 412 (前提条件) 失败。 此错误向客户端表明，自客户端第一次检索 blob 后，其他进程已更新了 blob。
1. 如果 blob 的当前 ETag 值与请求的 **if-match** 条件标头中的 etag 的版本相同，则 Azure 存储将执行请求的操作，并更新该 blob 的当前 ETag 值。  

下面的代码示例演示如何在用于检查 blob 的 ETag 值的写入请求上构造 **if-match** 条件。 Azure 存储计算 blob 的当前 ETag 与请求提供的 ETag 是否相同，并且仅当两个 ETag 值匹配时才执行写入操作。 如果其他进程已在临时更新了 blob，则 Azure 存储将返回 HTTP 412 (前提条件) 状态消息失败。  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Azure 存储还支持其他条件标头，包括 **自修改** 后的（如修改后）-从 **开始，到** **-无-匹配**。 有关详细信息，请参阅[为 Blob 服务操作指定条件标头](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)。  

## <a name="pessimistic-concurrency-for-blobs"></a>Blob 的悲观并发

若要锁定 Blob 以供独占使用，您可以对该 Blob 获得租约。 获取租约时，可以指定租约的持续时间。 有限租约的有效期可能介于15到60秒之间。 租约也可以是无限的，其中的量为排他锁。 你可以续订有限租约以便对其进行扩展，并且可以在完成租约后释放该租约。 Azure 存储空间过期时，会自动释放有限租约。  

租约可支持不同的同步策略，包括独占写入/共享读取操作、独占写入/独占读取操作以及共享写入/独占读取操作。 存在租约时，Azure 存储会对租约持有者强制执行写入操作的独占访问权限。 但是，若要确保读取操作的独占性，开发人员必须确保所有客户端应用程序都使用租约 ID，并且一次只有一个客户端具有有效的租约 ID。 不包括租约 ID 的读取操作会导致共享读取。  

下面的代码示例演示如何获取 blob 的独占租约，通过提供租约 ID 来更新 blob 的内容，然后释放租约。 如果租约处于活动状态，并且写入请求中未提供租约 ID，则写入操作失败，错误代码为 412 (前提条件) 失败。  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>容器的悲观并发

容器上的租约启用了对 blob 支持的相同同步策略，包括独占写入/共享读取、独占写入/独占读取和共享写入/独占读取。 但对于容器，仅对删除操作强制执行排他锁。 要删除具有活动租约的容器，客户端必须将活动租约 ID 包括在删除请求中。 在没有租约 ID 的情况下，所有其他容器操作都将在租用的容器上成功完成。  

## <a name="next-steps"></a>后续步骤

* [为 Blob 服务操作指定条件标头](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Lease Container](/rest/api/storageservices/lease-container)（租赁容器）
* [租用 Blob](/rest/api/storageservices/lease-blob)
