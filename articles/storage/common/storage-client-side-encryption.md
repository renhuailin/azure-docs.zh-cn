---
title: 针对 Microsoft Azure 存储使用 .NET 的客户端加密 | Microsoft Docs
description: 用于 .NET 的 Azure 存储客户端库支持客户端加密以及与 Azure 密钥保管库集成，以便最大程度地保护 Azure 存储应用程序。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 577a288d8dbe6afd7c05aa78e7055bdae288ce82
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589295"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Microsoft Azure 存储的客户端加密和 Azure 密钥保管库

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>概述

[用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage)支持在上传到 Azure 存储之前加密客户端应用程序中的数据，以及在下载到客户端时解密数据。 此库还支持与 [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)集成，以便管理存储帐户密钥。

有关使用客户端加密和 Azure 密钥保管库完成加密 blob 过程的分步教程，请参阅[使用 Azure 密钥保管库在 Microsoft Azure 存储中加密和解密 blob](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)。

有关使用 Java 的客户端加密，请参阅[针对 Microsoft Azure 存储使用 Java 的客户端加密](storage-client-side-encryption-java.md)。

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>通过信封技术加密和解密

加密和解密的过程遵循信封技术。

### <a name="encryption-via-the-envelope-technique"></a>通过信封技术加密

通过信封技术加密的工作方式如下：

1. Azure 存储客户端库生成内容加密密钥 (CEK)，这是一次性使用对称密钥。
2. 使用此 CEK 对用户数据进行加密。
3. 然后，使用密钥加密密钥 (KEK) 对此 CEK 进行包装（加密）。 KEK 由密钥标识符标识，可以是非对称密钥对或对称密钥，还可以在本地托管或存储在 Azure 密钥保管库中。

    存储客户端库本身永远无法访问 KEK。 该库调用密钥保管库提供的密钥包装算法。 用户可以根据需要选择使用自定义提供程序进行密钥包装/解包。

4. 然后，将已加密的数据上传到 Azure 存储服务。 已包装的密钥以及一些附加加密元数据要么存储为元数据（在 Blob 上），要么以内插值替换已加密的数据（消息队列和表实体）。

### <a name="decryption-via-the-envelope-technique"></a>通过信封技术解密

通过信封技术解密的工作方式如下：

1. 客户端库假定用户在本地或 Azure 密钥保管库中管理密钥加密密钥 (KEK)。 用户不需要知道用于加密的特定密钥。 但是，可以设置和使用一个密钥解析程序，将不同的密钥标识符解析为密钥。
2. 客户端库下载已加密数据以及存储在服务中的任何加密材料。
3. 然后，使用密钥加密密钥 (KEK) 对已包装的内容加密密钥 (CEK) 进行解包（解密）。 这里同样，客户端库无法访问 KEK。 它只是调用自定义提供程序或 Key Vault 提供程序的解包算法。
4. 然后，使用内容加密密钥 (CEK) 解密已加密的用户数据。

## <a name="encryption-mechanism"></a>加密机制

存储客户端库使用 [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 来加密用户数据。 具体而言，是使用 AES 的[加密块链接 (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) 模式。 每个服务的工作方式都稍有不同，因此我们会在此讨论其中每个服务。

### <a name="blobs"></a>Blob

目前，客户端库仅支持整个 Blob 的加密。 对于下载，支持完整下载和范围下载。

在加密过程中，客户端库将生成 16 字节的随机初始化向量 (IV) 和 32 字节的随机内容加密密钥 (CEK) 并将使用此信息对 Blob 数据执行信封加密。 然后，已包装的 CEK 和一些附加加密元数据将与服务上的已加密 Blob 一起存储为 Blob 元数据。

> [!WARNING]
> 如果要针对 Blob 编辑或上传自己的元数据，需要确保此元数据已保留。 如果在没有此元数据的情况下上传新元数据，则已包装的 CEK、IV 和其他元数据将丢失，而 Blob 内容将永远无法再检索。

下载整个 blob 时，已包装的 CEK 会解包，并与 IV（在本示例中存储为 Blob 元数据）一起使用以将解密后的数据返回给用户。

下载已加密 Blob 中的任意范围需要调整用户提供的范围，以便获取少量可用于成功解密所请求范围的附加数据。

所有 Blob 类型（块 Blob、页 Blob 和追加 Blob）都可以使用此方案进行加密/解密。

### <a name="queues"></a>队列

由于队列消息可以采用任何格式，客户端库定义一个自定义格式，其在消息文本中包括初始化向量 (IV) 和已加密的内容加密密钥 (CEK)。

在加密过程中，客户端库将生成 16 字节的随机 IV 和 32 字节的随机 CEK，并使用此信息对队列消息文本执行信封加密。 然后，将已包装的 CEK 和一些附加加密元数据添加到已加密的队列消息中。 此修改后的消息（如下所示）将存储在服务中。

```xml
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

在解密过程中，将从队列消息中提取已包装的密钥并将其解包。 还将从队列消息中提取 IV，与解包的密钥一起使用来对队列消息数据进行解密。 请注意，加密元数据很少（500 字节以下），因此虽然它计入队列消息的 64KB 限制，但影响应是可管理的。 请注意，加密的消息将采用 base64 编码，如上面的代码片段所示，它还将扩展正在发送的消息的大小。

### <a name="tables"></a>表

> [!NOTE]
> 表服务仅在 9.x 版的 Azure 存储客户端库中受支持。

客户端库支持对插入和替换操作的实体属性进行加密。

> [!NOTE]
> 当前不支持合并。 由于属性的子集可能以前已使用不同的密钥加密，因此只合并新属性和更新元数据会导致数据丢失。 合并需要进行额外的服务调用以从服务中读取预先存在的实体，或者需要为属性使用一个新密钥，由于性能方面的原因，这两种方案都不适用。

表数据加密的工作方式如下：

1. 用户指定要加密的属性。
2. 客户端库为每个实体生成 16 个字节的随机初始化向量 (IV) 和 32 个字节的随机内容加密密钥 (CEK)，并通过为每个属性派生新的 IV 对要加密的单独属性执行信封加密。 加密的属性存储为二进制数据。
3. 然后，已包装的 CEK 和一些附加加密元数据将存储为两个附加保留属性。 第一个保留属性 (_ClientEncryptionMetadata1) 是一个字符串属性，保存有关 IV、版本和已包装密钥的信息。 第二个保留属性 (_ClientEncryptionMetadata2) 是一个二进制属性，保存有关已加密属性的信息。 第二个属性 (_ClientEncryptionMetadata2) 中的信息本身是已加密。
4. 由于加密需要这两个附加保留属性，用户现在可能只有 250 个自定义属性，而不是 252 个。 实体的总大小必须小于 1 MB。

请注意，只有字符串属性可以加密。 如果要对其他类型的属性进行加密，必须将它们转换为字符串。 加密的字符串作为二进制属性存储在服务中，并在解密之后转换回字符串。

对于表，除了加密策略以外，用户还必须指定要加密的属性。 可以通过指定 [EncryptProperty] 特性（适用于从 TableEntity 派生的 POCO 实体）或在请求选项中指定加密解析程序来完成此操作。 加密解析程序是一个委托，它接受分区键、行键和属性名称并返回一个布尔值以指示是否应加密该属性。 在加密过程中，客户端库会使用此信息来确定是否应在写入到网络时加密属性。 该委托还可以围绕如何加密属性实现逻辑的可能性。 （例如，如果 X，则加密属性 A，否则加密属性 A 和 B。）请注意，在读取或查询实体时，不需要提供此信息。

### <a name="batch-operations"></a>批处理操作

在批处理操作中，将对该批处理操作中的所有行使用同一 KEK，因为客户端库仅允许每个批处理操作使用一个选项对象（因此是一个策略/KEK）。 但是，客户端库会为批处理中的每行在内部生成一个新的随机 IV 和随机 CEK。 用户还可以选择通过在加密解析程序中定义此行为来加密批处理中的每个操作的不同属性。

### <a name="queries"></a>查询

> [!NOTE]
> 由于实体已加密，因此不能运行根据已加密属性进行筛选的查询。  如果尝试运行，结果将会不正确，因为该服务会尝试将已加密的数据与未加密的数据进行比较。
>
> 若要执行查询操作，必须指定一个能够解析结果集中的所有密钥的密钥解析程序。 如果查询结果中包含的实体不能解析为提供程序，则客户端库会引发错误。 对于执行服务器端投影的任何查询，在默认情况下，客户端库将为所选列添加特殊的加密元数据属性（_ClientEncryptionMetadata1 和 _ClientEncryptionMetadata2）。

## <a name="azure-key-vault"></a>Azure Key Vault

Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 通过 Azure 密钥保管库，用户可以使用受硬件安全模块 (HSM) 保护的密钥，来加密密钥和机密（例如身份验证密钥、存储帐户密钥、数据加密密钥、.PFX 文件和密码）。 有关详细信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)。

存储客户端库使用核心库中的 Key Vault 接口，以便在整个 Azure 上提供一个通用框架来管理密钥。 用户可以利用 Key Vault 库来获得其提供的所有其他优势，例如，围绕简单且无缝的对称/RSA 本地和云密钥提供程序的有用功能，以及有关聚合和缓存的帮助功能。

### <a name="interface-and-dependencies"></a>接口和依赖项

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Key Vault 集成有两个必需的包：

- Azure.Core 包含 `IKeyEncryptionKey` 和 `IKeyEncryptionKeyResolver` 接口。 用于 .NET 的存储客户端库已将其定义为一个依赖项。
- Azure.Security.KeyVault.Keys (v4.x) 包含 Key Vault REST 客户端，以及用于客户端加密的加密客户端。

密钥保管库专为高价值主密钥设计，每个密钥保管库的限流限制的设计也考虑了这一点。 截至 Azure.Security.KeyVault.Keys 4.1.0，都没有支持密钥缓存的 `IKeyEncryptionKeyResolver` 实现。 如果在限制条件下需要缓存，则应按照[本示例](/samples/azure/azure-sdk-for-net/azure-key-vault-proxy/)中的步骤将缓存层注入到 `Azure.Security.KeyVault.Keys.Cryptography.KeyResolver` 实例中。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

有三个密钥保管库包：

- Microsoft.Azure.KeyVault.Core 包含 IKey 和 IKeyResolver。 它是没有依赖项的小型包。 用于 .NET 的存储空间客户端库将其定义为一个依赖项。
- Microsoft.Azure.KeyVault (v3.x) 包含 Key Vault REST 客户端。
- Microsoft.Azure.KeyVault.Extensions (v3.x) 包含扩展代码，代码中包含加密算法和 RSAKey 与 SymmetricKey 的实现。 它依赖于 Core 和 KeyVault 命名空间，并提供用于定义聚合解析程序（在用户想要使用多个密钥提供程序时）和缓存密钥解析程序的功能。 虽然存储客户端库不直接依赖于此包，但是如果用户想要使用 Azure 密钥保管库存储其密钥或通过密钥保管库扩展来使用本地和云加密提供程序，则他们需要此包。

密钥保管库专为高价值主密钥设计，每个密钥保管库的限流限制的设计也考虑了这一点。 使用密钥保管库执行客户端加密时，首选模型是使用在密钥保管库中作为机密存储并在本地缓存的对称主密钥。 用户必须执行以下操作：

1. 脱机创建一个机密并将其上传到密钥保管库。
2. 使用机密的基标识符作为参数来解析机密的当前版本进行加密，并在本地缓存此信息。 使用 CachingKeyResolver 进行缓存；用户不需要实现自己的缓存逻辑。
3. 创建加密策略时，使用缓存解析程序作为输入。

有关 v11 中 Key Vault 用法的详细信息，可查看 [v11 加密代码示例](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)。

---

## <a name="best-practices"></a>最佳实践

仅在用于 .NET 的存储空间客户端库中提供加密支持。 Windows Phone 和 Windows 运行时当前不支持加密。

> [!IMPORTANT]
> 使用客户端加密时，请注意以下要点：
>
> - 读取或写入到已加密的 Blob 时，请使用完整 Blob 上传命令和范围/完整 Blob 下载命令。 避免使用协议操作（如“放置块”、“放置块列表”、“写入页”、“清除页”或“追加块”）写入到已加密的 Blob，否则可能会损坏已加密的 Blob 并使其不可读。
> - 对于表，存在类似的约束。 请注意，不要在未更新加密元数据的情况下更新已加密的属性。
> - 如果在已加密的 Blob 上设置元数据，则可能会覆盖解密所需的与加密相关的元数据，因为设置元数据不是累加性的。 这也适用于快照；避免在创建已加密的 Blob 的快照时指定元数据。 如果必须设置元数据，务必调用 **FetchAttributes** 方法首先获取当前加密元数据，并在设置元数据时避免并发写入。
> - 对于仅使用加密数据的用户，请在默认请求选项中启用 **RequireEncryption** 属性。 有关详细信息，请参阅下文。

## <a name="client-api--interface"></a>客户端 API/接口

用户可以仅提供密钥或解析程序，也可以同时提供两者。 密钥是使用密钥标识符标识的，提供包装/解包逻辑。 解析程序用于在解密过程中解析密钥。 它定义了解析方法，该方法根据给定的密钥标识符返回密钥。 由此，用户能够在多个位置中托管的多个密钥之间进行选择。

- 对于加密，始终使用该密钥，而没有密钥将导致错误。
- 对于解密：
  - 如果指定了密钥，并且其标识符与所需的密钥标识符匹配，则使用该密钥进行解密。 否则，将尝试使用解析程序。 如果此尝试没有可用的解析程序，则会引发错误。
  - 如果指定为获取密钥，则将调用密钥解析程序。 如果指定了解析程序，但该解析程序不具有密钥标识符的映射，则将引发错误。

### <a name="requireencryption-mode-v11-only"></a>RequireEncryption 模式（仅限 v11）

用户可以选择启用一个操作模式，让所有上传和下载都必须加密。 在此模式下，尝试在没有加密策略的情况下上传数据或下载在服务中未加密的数据，将导致在客户端上失败。 请求选项对象的 **RequireEncryption** 属性控制此行为。 如果应用程序要加密存储于 Azure 存储中的所有对象，则可以在服务客户端对象的默认请求选项上设置 **RequireEncryption** 属性。 例如，将 **CloudBlobClient.DefaultRequestOptions.RequireEncryption** 设置为 **true**，要求对通过该客户端对象执行的所有 blob 操作进行加密。

### <a name="blob-service-encryption"></a>Blob 服务加密

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

创建一个 **ClientSideEncryptionOptions** 对象并在创建客户端时为其设置 **SpecializedBlobClientOptions**。 你无法按 API 设置加密选项。 其他所有事项均由客户端库在内部处理。

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
BlobClientOptions options = new SpecializedBlobClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your blob client with client-side encryption enabled.
// Client-side encryption options are passed from service to container clients, and container to blob clients.
// Attempting to construct a BlockBlobClient, PageBlobClient, or AppendBlobClient from a BlobContainerClient
// with client-side encryption options present will throw, as this functionality is only supported with BlobClient.
BlobClient blob = new BlobServiceClient(connectionString, options).GetBlobContainerClient("my-container").GetBlobClient("myBlob");

// Upload the encrypted contents to the blob.
blob.Upload(stream);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadTo(outputStream);
```

应用加密选项时不需要使用 **BlobServiceClient**。 还可以将它们传递到接受 **BlobClientOptions** 对象的 **BlobContainerClient**/**BlobClient** 构造函数。

如果所需的 **BlobClient** 对象已存在，但没有客户端加密选项，则可以使用一个扩展方法通过给定的 **ClientSideEncryptionOptions** 创建该对象的副本。 此扩展方法避免了从头开始构造新的 **BlobClient** 对象的开销。

```csharp
using Azure.Storage.Blobs.Specialized;

// Your existing BlobClient instance and encryption options
BlobClient plaintextBlob;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextBlob that uses client-side encryption
BlobClient clientSideEncryptionBlob = plaintextBlob.WithClientSideEncryptionOptions(encryptionOptions);
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

创建 **BlobEncryptionPolicy** 对象并在请求选项中对其进行设置（使用 **DefaultRequestOptions** 基于每个 API 或在客户端级别设置）。 其他所有事项均由客户端库在内部处理。

```csharp
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Upload the encrypted contents to the blob.
blob.UploadFromStream(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadToStream(outputStream, null, options, null);
```

---

### <a name="queue-service-encryption"></a>队列服务加密

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

创建一个 **ClientSideEncryptionOptions** 对象并在创建客户端时为其设置 **SpecializedQueueClientOptions**。 你无法按 API 设置加密选项。 其他所有事项均由客户端库在内部处理。

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
QueueClientOptions options = new SpecializedQueueClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your queue client with client-side encryption enabled.
// Client-side encryption options are passed from service to queue clients.
QueueClient queue = new QueueServiceClient(connectionString, options).GetQueueClient("myQueue");

// Send an encrypted queue message.
queue.SendMessage("Hello, World!");

// Download queue messages, decrypting ones that are detected to be encrypted
QueueMessage[] queue.ReceiveMessages(); 
```

应用加密选项时不需要使用 **QueueServiceClient**。 还可以将它们传递到接受 **QueueClientOptions** 对象的 **QueueClient** 构造函数。

如果所需的 **QueueClient** 对象已存在，但没有客户端加密选项，则可以使用一个扩展方法通过给定的 **ClientSideEncryptionOptions** 创建该对象的副本。 此扩展方法避免了从头开始构造新的 **QueueClient** 对象的开销。

```csharp
using Azure.Storage.Queues.Specialized;

// Your existing QueueClient instance and encryption options
QueueClient plaintextQueue;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextQueue that uses client-side encryption
QueueClient clientSideEncryptionQueue = plaintextQueue.WithClientSideEncryptionOptions(encryptionOptions);
```

在某些用户的队列中，并非所有接收到的消息都可以成功解密，因此密钥或解析程序必定会引发异常。 在本例中，上面的示例的最后一行会引发异常，接收到的消息都将不可访问。 在这些情况下，可以使用子类 **QueueClientSideEncryptionOptions** 向客户端提供加密选项。 它公开事件 **DecryptionFailed**。只要至少有一个调用已添加到该事件，每当队列消息无法解密时就会触发该事件。 单独失败的消息可以通过这种方式进行处理，它们会从 **ReceiveMessages** 返回的最终的 **QueueMessage[]** 中筛选掉。

```csharp
// Create your encryption options using the sub-class.
QueueClientSideEncryptionOptions encryptionOptions = new QueueClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Add a handler to the DecryptionFailed event.
encryptionOptions.DecryptionFailed += (source, args) => {
   QueueMessage failedMessage = (QueueMessage)source;
   Exception exceptionThrown = args.Exception;
   // do something
};

// Use these options with your client objects.
QueueClient queue = new QueueClient(connectionString, queueName, new SpecializedQueueClientOptions()
{
   ClientSideEncryption = encryptionOptions
});

// Retrieve 5 messages from the queue.
// Assume 5 messages come back and one throws during decryption.
QueueMessage[] messages = queue.ReceiveMessages(maxMessages: 5).Value;
Debug.Assert(messages.Length == 4)
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

创建 **QueueEncryptionPolicy** 对象并在请求选项中对其进行设置（使用 **DefaultRequestOptions** 基于每个 API 或在客户端级别设置）。 其他所有事项均由客户端库在内部处理。

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

---

### <a name="table-service-encryption-v11-only"></a>表服务加密（仅限 v11）

除了创建加密策略和在请求选项上设置它以外，还必须在 **TableRequestOptions** 中指定 **EncryptionResolver**，或在实体上设置 [EncryptProperty] 特性。

#### <a name="using-the-resolver"></a>使用解析程序

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>使用特性

如上所述，如果实体实现了 TableEntity，则可以使用 [EncryptProperty] 特性修饰属性，而不用指定 **EncryptionResolver**。

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>加密和性能

注意，加密存储数据会导致额外的性能开销。 必须生成内容密钥和 IV，内容本身必须进行加密，并且其他元数据必须进行格式化并上传。 此开销将因所加密的数据量而有所变化。 我们建议客户在开发过程中始终测试其应用程序的性能。

## <a name="next-steps"></a>后续步骤

- [教程：在 Microsoft Azure 存储中使用 Azure 密钥保管库加密和解密 blob](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
- 下载[适用于 .NET NuGet 包的 Azure 存储客户端库](https://www.nuget.org/packages/WindowsAzure.Storage)
- 下载 Azure 密钥保管库 NuGet [核心](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/)、[客户端](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)和[扩展](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)包
- 访问 [Azure 密钥保管库文档](../../key-vault/general/overview.md)
