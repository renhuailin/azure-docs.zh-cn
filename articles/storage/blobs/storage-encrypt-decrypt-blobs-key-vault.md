---
title: 教程 - 使用 Azure 密钥保管库加密和解密 blob
titleSuffix: Azure Storage
description: 了解如何通过客户端加密使用 Azure 密钥保管库来加密和解密 blob。
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 78356745ee013b011f23a4bf42f903cf89bedd4b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603973"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>教程 - 使用 Azure 密钥保管库加密和解密 blob

本教程介绍如何结合使用客户端存储加密与 Azure 密钥保管库。 它会逐步演示如何使用这些技术在控制台应用程序中加密和解密 Blob。

**估计完成时间：** 20 分钟

有关 Azure 密钥保管库的概述信息，请参阅[什么是 Azure 密钥保管库？](../../key-vault/general/overview.md)。

有关 Azure 存储的客户端加密概述信息，请参阅 [Microsoft Azure 存储的客户端加密和 Azure 密钥保管库](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="prerequisites"></a>先决条件

要完成本教程，必须满足下列要求：

- Azure 存储帐户
- Visual Studio 2013 或更高版本
- Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>客户端加密概述

有关 Azure 存储的客户端加密概述，请参阅 [Microsoft Azure 存储的客户端加密和 Azure 密钥保管库](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

下面是客户端加密的工作原理的简要说明：

1. Azure 存储客户端 SDK 生成内容加密密钥 (CEK)，这是一次性使用对称密钥。
2. 使用此 CEK 对客户数据进行加密。
3. 然后，使用密钥加密密钥 (KEK) 对此 CEK 进行包装（加密）。 KEK 由密钥标识符标识，可以是非对称密钥对或对称密钥，还可以在本地托管或存储在 Azure 密钥保管库中。 存储空间客户端本身永远无法访问 KEK。 它只能调用密钥保管库提供的密钥包装算法。 客户可以根据需要选择使用自定义提供程序进行密钥包装/解包。
4. 然后，将已加密的数据上传到 Azure 存储服务。

## <a name="set-up-your-azure-key-vault"></a>设置 Azure 密钥保管库

若要继续学习本教程，需要执行教程[快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密](../../key-vault/secrets/quick-create-net.md)中所述的以下步骤：

- 创建密钥保管库。
- 将密钥或密码添加到密钥保管库。
- 将应用程序注册到 Azure Active Directory。
- 授权应用程序使用密钥或密码。

记下将应用程序注册到 Azure Active Directory 时生成的 ClientID 和 ClientSecret。

在密钥保管库中创建这两个密钥。 我们在本教程的其余部分假定使用了以下名称：ContosoKeyVault 和 TestRSAKey1。

## <a name="create-a-console-application-with-packages-and-appsettings"></a>使用程序包和 AppSettings 创建控制台应用程序

在 Visual Studio 中创建新的控制台应用程序。

在 Package Manager Console 中添加必要的 Nuget 包。

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

将 AppSettings 添加到 App.Config。

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

添加以下 `using` 指令并确保将对 System.Configuration 的引用添加到项目中。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```

---

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>添加方法以便为控制台应用程序获取令牌

以下方法由密钥保管库类使用，这些类需要进行身份验证才能访问密钥保管库。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

---

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>在程序中访问 Azure 存储和密钥保管库

在 Main() 方法中，添加以下代码。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

---

> [!NOTE]
> 密钥保管库对象模型
>
> 务必了解，实际上有两个 Key Vault 对象模型：一个基于 REST API（KeyVault 命名空间），另一个是客户端加密的扩展。
>
> 密钥保管库客户端与 REST API 进行交互，并了解密钥保管库中包含的两种模型的 JSON Web 密钥和密码。
>
> 密钥保管库扩展似乎是专为 Azure 存储中的客户端加密而创建的类。 根据密钥解析程序的概念，它们包含密钥 (IKey) 和类的接口。 需要了解两种 IKey 实现：RSAKey 和 SymmetricKey。 现在它们碰巧与 Key Vault 中包含的内容保持一致，但此时它们是独立的类（因此，Key Vault 客户端检索到的密钥与秘密检索未实现 IKey）。
>
>

## <a name="encrypt-blob-and-upload"></a>加密 Blob 和上传

添加以下代码以加密 Blob 并将其上传到 Azure 存储帐户。 使用的 **ResolveKeyAsync** 方法会返回 IKey。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

---

> [!NOTE]
> 如果查看 BlobEncryptionPolicy 构造函数，会看到它可以接受密钥和/或解析程序。 请注意，现在无法将解析程序用于加密，因为它当前不支持默认密钥。

## <a name="decrypt-blob-and-download"></a>解密 Blob 并下载

当使用解析程序类有意义时，实际上就是解密。 用于加密的密钥的 ID 与其元数据中的 Blob 相关联，因此没有理由检索该密钥，请记住密钥与 Blob 之间的关联关系。 只需确保该密钥保留在密钥保管库中。

RSA 密钥的私钥则保留在密钥保管库中，因此，为了进行解密，来自包含 CEK 的 Blob 元数据的加密密钥会发送到密钥保管库进行解密。

添加以下代码以解密刚刚上传的 Blob。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

---

> [!NOTE]
> 可以通过几个其他类型的解析程序来简化密钥管理，其中包括：AggregateKeyResolver 和 CachingKeyResolver。

## <a name="use-key-vault-secrets"></a>使用密钥保管库密码

将密码用于客户端加密的方式是通过 SymmetricKey 类，因为密码实际上是一种对称密钥。 但是，如上所述，密钥保管库中的密码不会完全映射到 SymmetricKey。 这里要注意几个问题：

- SymmetricKey 中的密钥必须是固定长度：128、192、256、384 或 512 位。
- SymmetricKey 中的密钥应采用 Base64 编码。
- 用作 SymmetricKey 的密钥保管库密钥需要在密钥保管库中具有“application/octet-stream”内容类型。

以下是使用 PowerShell 在密钥保管库中创建可用作 SymmetricKey 的密钥的示例。
请注意，硬编码值 $key 仅用于演示目的。 请在自己的代码中生成此密钥。

```powershell
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

在控制台应用程序中，可以使用与之前相同的调用将此密钥作为 SymmetricKey 进行检索。

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

我们目前正在努力创建反映 Azure 存储客户端库 12.x 版的代码片段。 有关详细信息，请参阅[宣布推出 Azure 存储 v12 客户端库](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)。

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

---

## <a name="next-steps"></a>后续步骤

要深入了解如何将 Microsoft Azure 存储与 C# 配合使用，请参阅[用于 .NET 的 Microsoft Azure 存储客户端库](/previous-versions/azure/dn261237(v=azure.100))。

有关 Blob REST API 的详细信息，请参阅 [Blob Service REST API](/rest/api/storageservices/Blob-Service-REST-API)（Blob 服务 REST API）。

有关 Microsoft Azure 存储的最新信息，请转到 [Microsoft Azure 存储团队博客](/archive/blogs/windowsazurestorage/)。
