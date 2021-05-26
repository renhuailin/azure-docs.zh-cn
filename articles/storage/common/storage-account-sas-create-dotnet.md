---
title: 使用 .NET 创建帐户 SAS
titleSuffix: Azure Storage
description: 了解如何使用 .NET 客户端库创建帐户共享访问签名 (SAS)。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 3e08962c0cff2e9aa04601f89e677334878e2f53
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110461855"
---
# <a name="create-an-account-sas-with-net"></a>使用 .NET 创建帐户 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文介绍如何使用存储帐户密钥通过[用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage)创建帐户 SAS。

## <a name="create-an-account-sas"></a>创建帐户 SAS

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

帐户 SAS 将使用帐户访问密钥进行签名。 使用 [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) 类创建用于为 SAS 签名的凭据。 接下来，新建 [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) 对象，并调用 [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) 以获取 SAS 令牌字符串。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

若要为容器创建帐户 SAS，请调用 [CloudStorageAccount.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) 方法。

以下代码示例创建一个帐户 SAS，该 SAS 对 Blob 和文件服务是有效的，并授予客户端读取、写入和列表权限，使其能够访问服务级别 API。 帐户 SAS 将协议限制为 HTTPS，因此请求必须使用 HTTPS 发出。 请务必将尖括号中的占位符值替换为你自己的值：

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>通过客户端使用帐户 SAS

若要使用帐户 SAS 访问 Blob 服务的服务级别 API，请使用存储帐户的 SAS 和 Blob 存储终结点构造 Blob 服务客户端对象。

### <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnetv11)

在此代码片段中，将 `<storage-account>` 占位符替换为存储帐户的名称。

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>后续步骤

- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](storage-sas-overview.md)
- [创建帐户 SAS](/rest/api/storageservices/create-account-sas)
