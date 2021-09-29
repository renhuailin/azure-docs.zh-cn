---
title: 在 Blob 存储中管理并发
titleSuffix: Azure Storage
description: 了解如何通过在应用程序中实现乐观或悲观并发，来管理 blob 的多个编写器。 乐观并发检查 blob 的 ETag 值，并将其与提供的 ETag 进行比较。 悲观并发使用独占租约将 blob 锁定到其他编写器。
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 20f6e4b02e75686d98456a97490ef261ee929a1b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600297"
---
# <a name="managing-concurrency-in-blob-storage"></a>在 Blob 存储中管理并发

新型应用程序通常允许多名用户同时查看和更新数据。 应用程序开发人员需要仔细考虑如何为他们的最终用户提供可预测的体验，尤其是在多名用户可以更新相同数据的情况下。 开发人员通常考虑下面三个主要数据并发策略：

- 乐观并发：执行更新的应用程序将在其更新过程中确定数据是否自该应用程序上次读取此数据以来已发生更改。 例如，如果两名查看 wiki 页面的用户对该页面进行更新，则 wiki 平台必须确保第二次更新不会覆盖第一次更新。 此外还必须确保两名用户都了解其更新是否成功。 此策略最常用于 Web 应用程序中。

- 悲观并发：要执行更新的应用程序会对对象上锁，以防其他用户在该锁释放前更新数据。 例如，在进行主/辅数据复制且只有主对象执行更新的情况下，该对象通常会长时间以独占的形式锁定数据，以确保其他任何对象都不能更新该数据。

- 以最后写入者为准：一种方法，它允许更新操作继续进行，而不需要首先确定其他应用程序是否自数据被读取以来已更新该数据。 当数据分区时，通常会使用此方法，这样就不可能有多个用户同时访问相同的数据。 该策略可能还适用于正在处理短期数据流的情况。

Azure 存储支持所有三个策略，但是它在为乐观和悲观并发提供全面支持的能力方面与众不同。 Azure 存储旨在采用强大的一致性模型，确保在服务执行插入或更新操作后，后续读取操作会返回最新更新。

除了选择相应的并发策略，开发人员还应了解存储平台如何隔离更改，尤其是跨事务对相同对象进行的更改。 Azure 存储使用快照隔离，以允许在单个分区中并发执行读取操作与写入操作。 快照隔离保证所有读取操作返回的数据快照是一致的，即使在进行更新时也是如此。

可以选择使用乐观并发模型或悲观并发模型，来管理对 Blob 和容器的访问。 如果未显式指定策略，则默认情况下以最后一次写入为准。

## <a name="optimistic-concurrency"></a>乐观并发

Azure 存储会为每个已存储的对象分配一个标识符。 只要对对象执行写入操作，就会更新此标识符。 该标识符作为 HTTP GET 响应的一部分在 ETag 标头（通过 HTTP 协议定义）中返回到客户端。

执行更新的客户端可以将原始 ETag 连同条件标头一起发送，以确保只有在满足特定条件的情况下才会进行更新。 例如，如果指定了 If-Match 标头，Azure 存储会验证更新请求中指定的 ETag 的值与所更新对象的 ETag 的值是否相同。 有关条件标头的详细信息，请参阅[为 Blob 服务操作指定条件标头](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)。

此进程的概述如下：

1. 从 Azure 存储检索 blob。 响应包括用于标识对象的当前版本的 HTTP ETag 标头值。
1. 在更新 blob 时，应将在步骤 1 中获得的 ETag 值包括在写入请求的 If-Match 条件标头中。 Azure 存储会将请求中的 ETag 值与 blob 当前的 ETag 值进行比较。
1. 如果 blob 当前的 ETag 值不同于请求中提供的 If-Match 条件标头中指定的 ETag 值，则 Azure 存储会返回 HTTP 状态代码412（“不满足前提条件”）。 此错误向客户端表明，另一进程在客户端首先检索 blob 后已更新该 blob。
1. 如果 blob 的当前 ETag 值与请求的 If-Match 条件标头中的 ETag 的版本相同，则 Azure 存储会执行请求的操作，并更新该 blob 的当前 ETag 值。

下面的代码示例演示如何在用于检查 blob 的 ETag 值的写入请求中构造 If-Match 条件。 Azure 存储会评估 blob 的当前 ETag 是否与请求中提供的 ETag 相同，只有在两个 ETag 值匹配时才执行写入操作。 如果其他进程已在此期间更新该 blob，则 Azure 存储会返回 HTTP 412（“不满足前提条件”）状态消息。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

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

Azure 存储还支持条件标头，其中包括 If-Modified-Since、If-Unmodified-Since 和 If-None-Match  。 有关详细信息，请参阅[为 Blob 服务操作指定条件标头](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)。

## <a name="pessimistic-concurrency-for-blobs"></a>Blob 的悲观并发

若要锁定 Blob 以供独占使用，您可以对该 Blob 获得租约。 获取租约时，可以指定租期。 有限期租约的有效期可为 15 到 60 秒。 租约也可以是无限期的，这相当于一个排他锁。 可续订有限期租约来延长租约，也可在租约完成后将其释放。 Azure 存储在有限期租约到期时会自动释放这些租约。

租约允许各种同步策略受支持，包括独占写入/共享读取操作、独占写入/独占读取操作和共享写入/独占读取操作。 存在租约时，Azure 存储会对租约持有者强制执行针对写入操作的独占访问权限。 但是，若要确保读取操作的独占性，开发人员需要确保所有客户端应用程序都使用一个租约 ID，并且一次只有一个客户端具有有效的租约 ID。 不包括租约 ID 的读取操作会导致共享读取。

下面的代码示例演示了如何获取 blob 的独占租约，通过提供租约 ID 来更新 blob 的内容，然后释放租约。 如果租约有效，但写入请求中未提供租约 ID，则写入操作会失败，并出现错误代码 412（“不满足前提条件”）。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

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

容器的租约允许 blob 支持的那些同步策略，包括独占写入/共享读取、独占写入/独占读取和共享写入/独占读取。 但是，对于容器，只会对删除操作强制执行排他锁。 要删除具有活动租约的容器，客户端必须将活动租约 ID 包括在删除请求中。 在没有租约 ID 的情况下，对租赁容器的所有其他容器操作都会成功。

## <a name="next-steps"></a>后续步骤

- [为 Blob 服务操作指定条件标头](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
- [Lease Container](/rest/api/storageservices/lease-container)（租赁容器）
- [租用 Blob](/rest/api/storageservices/lease-blob)
