---
title: 使用 .NET 在对 Blob 存储的请求中指定客户提供的密钥 - Azure 存储
description: 了解如何使用 .NET 在对 Blob 存储的请求中指定客户提供的密钥。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693516"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>使用 .NET 在对 Blob 存储的请求中指定客户提供的密钥

对 Azure Blob 存储发出请求的客户端可以选择在单个请求中提供 AES-256 加密密钥。 在请求中包含加密密钥可以精细控制 Blob 存储操作的加密设置。 客户提供的密钥可以存储在 Azure Key Vault 或其他密钥存储中。

本文介绍如何使用 .NET 在请求中指定客户提供的密钥。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

若要详细了解如何使用 Azure 标识客户端库进行身份验证，请参阅[用于 .NET 的 Azure 标识客户端库](/dotnet/api/overview/azure/identity-readme)。

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>使用客户提供的密钥写入到 Blob

下面的示例在使用用于 blob 存储的 v12 客户端库上传 blob 时提供了 AES-256 密钥。 该示例使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 对象授权 Azure AD 的写入请求，但也可以使用共享密钥凭据授权该请求。

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>后续步骤

- [在对 Blob 存储的请求中提供加密密钥](encryption-customer-provided-keys.md)
- [静态数据的 Azure 存储加密](../common/storage-service-encryption.md)
