---
title: Azure Cosmos DB 附件
description: 本文概述了 Azure Cosmos DB 附件。
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: d4fd96c9112d3ae9d1e2590d1ca99275c7eead07
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102475"
---
# <a name="azure-cosmos-db-attachments"></a>Azure Cosmos DB 附件
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB 附件是特殊项，它们包含对与外部 blob 或媒体文件关联的元数据的引用。

Azure Cosmos DB 支持两种类型的附件：

* **非托管附件**：是一个包装器，用于包装存储在外部服务（例如 Azure 存储、OneDrive 等）中的 blob 的 URI 引用。 此方法类似于将 URI 属性存储在标准 Azure Cosmos DB 项中。
* **托管附件**：是由 Azure Cosmos DB 在内部管理并存储并通过系统生成的 mediaLink 公开的 blob。


> [!NOTE]
> 附件是一项旧功能。 如果你已在使用此功能，则其支持范围限定为提供持续的功能。
> 
> 建议使用 Azure Blob 存储作为专门的 blob 存储服务来存储 blob 数据，而不是使用附件。 你可以继续将与 blob 相关的元数据连同引用 URI 链接一起作为项属性存储在 Azure Cosmos DB 中。 将此数据存储在 Azure Cosmos DB 中可以查询元数据并链接到存储在 Azure Blob 存储中的 blob。
> 
> Microsoft 承诺在完全弃用附件之前提供最少 36 个月的通知，这将在以后公布。

## <a name="known-limitations"></a>已知的限制

Azure Cosmos DB 的托管附件不同于其对标准项的支持 - 对于标准项，它提供无限制的可伸缩性、全局分发以及与其他 Azure 服务的集成。

- 并非所有版本的 Azure Cosmos DB SDK 都支持附件。
- 每个数据库帐户的托管附件限制为 2 GB 存储。
- 托管附件与 Azure Cosmos DB 的全局分发不兼容，它们不会跨区域复制。

> [!NOTE]
> 适用于 MongoDB 版本 3.2 的 Azure Cosmos DB API 使用 GridFS 的托管附件，并受到相同的限制。
>
> 我们建议使用 MongoDB GridFS 功能集的开发人员升级到适用于 MongoDB 版本 3.6 或更高版本的 Azure Cosmos DB API，它与附件分离并提供更好的体验。 此外，使用 MongoDB GridFS 功能集的开发人员还应考虑使用 Azure Blob 存储，它用于存储 Blob 内容，并提供其成本比 GridFS 更低的扩展功能。

## <a name="migrating-attachments-to-azure-blob-storage"></a>将附件迁移到 Azure Blob 存储

建议通过以下步骤将 Azure Cosmos DB 附件迁移到 Azure Blob 存储：

1. 将源 Azure Cosmos DB 容器中的附件数据复制到目标 Azure Blob 存储容器。
2. 验证目标 Azure Blob 存储容器中已上传的 blob 数据。
3. 如果适用，将对 Azure Blob 存储中包含的 blob 的 URI 引用添加为 Azure Cosmos DB 数据集中的字符串属性。
4. 重构你的应用程序代码，以便从新的 Azure Blob 存储容器读取和写入 blob。

以下 dotnet 代码示例展示了在完成迁移流的过程中，如何使用 Azure Cosmos DB 的 .NET SDK v2 和 Azure Blob 存储 .NET SDK v12 将附件从 Azure Cosmos DB 复制到 Azure Blob 存储。 请确保为源 Azure Cosmos DB 帐户和目标 Azure Blob 存储容器替换 `<placeholder values>`。

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;
                    PartitionKey docPartitionKey = new PartitionKey(document.Id);

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = docPartitionKey,
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;

                            // Clean up attachment from Azure Cosmos DB.
                            // Warning: please verify you've succesfully migrated attachments to blog storage prior to cleaning up Azure Cosmos DB.
                            // await cosmosClient.DeleteAttachmentAsync(
                            //     attachment.SelfLink,
                            //     new RequestOptions { PartitionKey = docPartitionKey }
                            // );

                            // Console.WriteLine("Cleaned up attachment ... Document Id: {0} , Attachment Id: {1}", document.Id, attachment.Id);
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>后续步骤

- 开始使用 [Azure Blob 存储](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- 获取通过 [Azure Cosmos DB 的 .NET SDK v2](/dotnet/api/microsoft.azure.documents.attachment) 使用附件的参考信息
- 获取通过 [Azure Cosmos DB 的 Java SDK v2](/java/api/com.microsoft.azure.documentdb.attachment) 使用附件的参考信息
- 获取通过 [Azure Cosmos DB 的 REST API](/rest/api/cosmos-db/attachments) 使用附件的参考信息
