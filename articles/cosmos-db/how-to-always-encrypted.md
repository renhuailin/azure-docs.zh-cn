---
title: 为 Azure Cosmos DB 使用具有 Always Encrypted 功能的客户端加密
description: 了解如何将具有 Always Encrypted 功能的客户端加密用于 Azure Cosmos DB
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/25/2021
ms.author: thweiss
author: ThomasWeiss
ms.openlocfilehash: 9135e35e74eda1387dc6dee5ba0875c4152439b0
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356376"
---
# <a name="use-client-side-encryption-with-always-encrypted-for-azure-cosmos-db-preview"></a>将具有 Always Encrypted 功能的客户端加密用于 Azure Cosmos DB （预览版）
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Always Encrypted 功能旨在保护 Azure Cosmos DB 中存储的敏感数据，如信用卡号或身份证号（例如美国社会安全号码）。 Always Encrypted 允许客户端对客户端应用程序内的敏感数据进行加密，并且永远不向数据库透漏加密密钥。

Always Encrypted 将客户端加密功能引入 Azure Cosmos DB。 在以下情况下，可能需要加密客户端数据：

- 保护具有特定机密性特性的敏感数据：Always Encrypted 支持客户端在其应用程序中对敏感数据进行加密，并且永远不会向 Azure Cosmos DB 服务公开纯文本数据或加密密钥。
- 为每个属性实现访问控制：由于加密是通过你拥有和管理的 Azure Key Vault 的密钥来控制的，因此可以应用访问策略来控制每个客户端可以访问哪些敏感属性。

> [!IMPORTANT]
> 适用于 Azure Cosmos DB 的 Always Encrypted 目前为预览版。 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

若要开始使用适用于 Azure Cosmos DB 的 Always Encrypted 预览版，可以：

- 使用 [Azure Cosmos DB 本地模拟器](local-emulator.md)的 2.11.13.0 或更高版本。
- 通过填写[此表单](https://ncv.microsoft.com/poTcF52I6N)，请求在 Azure Cosmos DB 帐户上启用预览版。

> [!TIP]
> 对于适用于 Azure Cosmos DB 的 Always Encrypted 预览版，你是否有任何反馈要分享？ 请发送电子邮件至 [azurecosmosdbcse@service.microsoft.com](mailto:azurecosmosdbcse@service.microsoft.com)。

## <a name="concepts"></a>概念

适用于 Azure Cosmos DB 的 Always Encrypted 引入了一些新的概念，这些新概念与客户端加密的配置有关。

### <a name="encryption-keys"></a>加密密钥

#### <a name="data-encryption-keys"></a>数据加密密钥

使用 Always Encrypted 时，数据会通过应提前创建的数据加密密钥 (DEK) 进行加密。 这些 DEK 存储在 Azure Cosmos DB 服务中，并在数据库级别上定义，因此可以在多个容器之间共享 DEK。 使用 Azure Cosmos DB SDK 在客户端创建 DEK。

方法：

- 为要加密的每个属性创建一个 DEK，或
- 使用相同的 DEK 加密多个属性。

#### <a name="customer-managed-keys"></a>客户管理的密钥

DEK 在 Azure Cosmos DB 中存储前，将由客户管理的密钥 (CMK) 进行包装。 通过控制 DEK 的包装和解包，CMK 可以有效地控制对使用相应 DEK 加密的数据的访问。 CMK 存储设计为可扩展/插件模型，默认实现要求它们存储在 Azure Key Vault。

:::image type="content" source="./media/how-to-always-encrypted/encryption-keys.png" alt-text="加密密钥" border="true":::

### <a name="encryption-policy"></a>加密策略

与[索引策略](index-policy.md)类似，加密策略是描述应如何加密 JSON 属性的容器级规范。 创建容器时必须提供此策略，并且该策略是不可变的。 在当前版本中，无法更新加密策略。

对于要加密的每个属性，加密策略定义：

- 属性的路径，格式为 `/property`。 目前仅支持顶层路径，不支持嵌套路径（如 `/path/to/property`）。
- 加密和解密属性时使用的 [DEK](#data-encryption-keys) 的 ID。
- 加密类型。 可以是随机的，也可以是确定性的。
- 加密属性时使用的加密算法。 指定算法可以替代创建密钥时定义的算法（如果二者兼容）。

> [!NOTE]
> 无法加密以下属性：
> - ID
> - 容器的分区键

#### <a name="randomized-vs-deterministic-encryption"></a>随机加密与确定性加密

Azure Cosmos DB 服务永远不会看到使用 Always Encrypted 加密的属性的纯文本。 但是，它仍支持对加密数据执行某些查询功能，具体取决于用于属性的加密类型。 Always Encrypted 支持以下两种类型的加密：

- 确定性加密：对任何给定的纯文本值和加密配置始终生成相同的加密值。 通过确定性加密，查询可以对加密属性执行相等筛选。 但是，它可能会允许攻击者通过了解加密属性中的模式来推测有关加密值的信息。 当存在小部分可能的加密值时（如 True/False 或北部/南部/东部/西部区域），则尤其如此。

- 随机加密：使用较不可预测的方式来加密数据。 随机加密更加安全，但会妨碍查询对加密属性进行筛选。

请参阅[生成初始化向量 (IV)](/sql/relational-databases/security/encryption/always-encrypted-cryptography#step-1-generating-the-initialization-vector-iv)，详细了解 Always Encrypted 中的确定性加密和随机加密。

## <a name="setup-azure-key-vault"></a>设置 Azure Key Vault

开始使用 Always Encrypted 的第一步是在 Azure Key Vault 中创建 CMK：

1. 创建新的 Azure Key Vault 实例或浏览到现有实例。
1. 在“密钥”部分中创建新密钥。
1. 创建密钥后，浏览到它的当前版本，并复制它的完整密钥标识符：<br>`https://<my-key-vault>.vault.azure.net/keys/<key>/<version>`. 如果在密钥标识符的末尾省略密钥版本，将使用最新版本的密钥。

接下来，需要配置 Azure Cosmos DB SDK 访问 Azure Key Vault 实例的方式。 此身份验证通过 Azure Active Directory (AD) 标识完成。 虽然可以使用任何类型的标识，但你很可能会使用 Azure AD 应用程序的标识或[托管标识](../active-directory/managed-identities-azure-resources/overview.md)作为客户端代码与 Azure Key Vault 实例之间的代理。 使用以下步骤将 Azure AD 应用程序用作代理：

1. 按照[本快速入门](../active-directory/develop/quickstart-register-app.md)中所述，创建一个新应用程序并添加客户端密码。

1. 返回到 Azure Key Vault 实例，浏览到“访问策略”部分，然后添加新策略：

   1. 在“密钥权限”中，选择“获取”、“列表”、“解包密钥”、“包装密钥”、“验证”和“签名”      。
   1. 在“选择主体”中，搜索之前创建的 AAD 应用程序。

### <a name="protect-your-cmk-from-accidental-deletion"></a>保护 CMK 不被意外删除

若要确保在意外删除 CMK 后不会失去对加密数据的访问权限，建议在 Azure Key Vault 实例上设置两个属性：“软件删除”和“清除保护” 。

如果创建新的 Azure Key Vault 实例，请在创建过程中启用以下属性：

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="为新的 Azure 密钥保管库实例启用“软删除”和“清除保护”":::

如果使用的是现有 Azure Key Vault 实例，则可以通过查看 Azure 门户中的“属性”部分来验证是否已启用这些属性。 如果未启用任一属性，请参阅以下文章中的“启用软删除”和“启用清除保护”部分：

- [如何在 PowerShell 中使用软删除](../key-vault/general/key-vault-recovery.md)
- [如何在 Azure CLI 中使用软删除](../key-vault/general/key-vault-recovery.md)

## <a name="initialize-the-sdk"></a>初始化 SDK

> [!NOTE]
> 当前支持适用于 Azure Cosmos DB 的 Always Encrypted：
> - 在 .NET 中使用 [Microsoft.Azure.Cosmos.Encryption 包](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Encryption)。
> - 在 Java 中使用 [azure.cosmos.encryption 包](https://mvnrepository.com/artifact/com.azure/azure-cosmos-encryption)。

若要使用 Always Encrypted，必须将 `EncryptionKeyStoreProvider` 的实例附加到 Azure Cosmos DB SDK 实例。 此对象用于与托管 CMK 的密钥存储进行交互。 适用于 Azure Key Vault 的默认密钥存储提供程序名为 `AzureKeyVaultKeyStoreProvider`。

以下代码片段显示如何将 Azure AD 应用程序标识与客户端密码结合使用。 可以找到创建不同类型的 `TokenCredential` 类的示例：

- [在 .NET 中](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [在 Java 中](/java/api/overview/azure/identity-readme#credential-classes)

# <a name="net"></a>[.NET](#tab/dotnet)

> [!NOTE]
> 在 .NET 中，需要其他的 [Microsoft.Data.Encryption.AzureKeyVaultProvider 包](https://www.nuget.org/packages/Microsoft.Data.Encryption.AzureKeyVaultProvider)来访问 `AzureKeyVaultKeyStoreProvider` 类。

```csharp
var tokenCredential = new ClientSecretCredential(
    "<aad-app-tenant-id>", "<aad-app-client-id>", "<aad-app-secret>");
var keyStoreProvider = new AzureKeyVaultKeyStoreProvider(tokenCredential);
var client = new CosmosClient("<connection-string>")
    .WithEncryption(keyStoreProvider);
```

# <a name="java"></a>[Java](#tab/java)

```java
TokenCredential tokenCredential = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<aad-app-tenant-id>")
    .clientId("<aad-app-client-id>")
    .clientSecret("<aad-app-secret>")
    .build();
AzureKeyVaultKeyStoreProvider encryptionKeyStoreProvider =
    new AzureKeyVaultKeyStoreProvider(tokenCredential);
CosmosAsyncClient client = new CosmosClientBuilder()
    .endpoint("<endpoint>")
    .key("<primary-key>")
    .buildAsyncClient();
EncryptionAsyncCosmosClient encryptionClient =
    EncryptionAsyncCosmosClient.buildEncryptionAsyncClient(client, encryptionKeyStoreProvider);
```
---

## <a name="create-a-data-encryption-key"></a>创建数据加密密钥

必须先在父数据库中创建[数据加密密钥](#data-encryption-keys)，然后才能在容器中加密数据。 这一操作是通过调用 `CreateClientEncryptionKeyAsync` 方法和传递以下项完成的：

- 一个字符串标识符，用于唯一标识数据库中的密钥。
- 要与密钥一起使用的加密算法。 当前仅支持一个算法。
- 存储在 Azure Key Vault 中的 [CMK](#customer-managed-keys) 的密钥标识符。 此参数在通用的 `EncryptionKeyWrapMetadata` 对象中传递，其中 `name` 可以是任何你想要的易记名称，而 `value` 必须是密钥标识符。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var database = client.GetDatabase("my-database");
await database.CreateClientEncryptionKeyAsync(
    "my-key",
    DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256,
    new EncryptionKeyWrapMetadata(
        keyStoreProvider.ProviderName,
        "akvKey",
        "https://<my-key-vault>.vault.azure.net/keys/<key>/<version>"));
```

# <a name="java"></a>[Java](#tab/java)

```java
EncryptionCosmosAsyncDatabase database =
    client.getEncryptedCosmosAsyncDatabase("my-database");
database.createClientEncryptionKey(
    "my-key",
    CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256,
    new EncryptionKeyWrapMetadata(
        "akvKey",
        "https://<my-key-vault>.vault.azure.net/keys/<key>/<version>"));
```
---

## <a name="create-a-container-with-encryption-policy"></a>创建具有加密策略的容器

创建容器时指定容器级加密策略。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var path1 = new ClientEncryptionIncludedPath
{
    Path = "/property1",
    ClientEncryptionKeyId = "my-key",
    EncryptionType = EncryptionType.Deterministic.ToString(),
    EncryptionAlgorithm = DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256.ToString()
};
var path2 = new ClientEncryptionIncludedPath
{
    Path = "/property2",
    ClientEncryptionKeyId = "my-key",
    EncryptionType = EncryptionType.Randomized.ToString(),
    EncryptionAlgorithm = DataEncryptionKeyAlgorithm.AEAD_AES_256_CBC_HMAC_SHA256.ToString()
};
await database.DefineContainer("my-container", "/partition-key")
    .WithClientEncryptionPolicy()
    .WithIncludedPath(path1)
    .WithIncludedPath(path2)
    .Attach()
    .CreateAsync();
```

# <a name="java"></a>[Java](#tab/java)

```java
ClientEncryptionIncludedPath path1 = new ClientEncryptionIncludedPath();
path1.clientEncryptionKeyId = "my-key":
path1.path = "/property1";
path1.encryptionType = CosmosEncryptionType.DETERMINISTIC;
path1.encryptionAlgorithm = CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256;

ClientEncryptionIncludedPath path2 = new ClientEncryptionIncludedPath();
path2.clientEncryptionKeyId = "my-key":
path2.path = "/property2";
path2.encryptionType = CosmosEncryptionType.RANDOMIZED;
path2.encryptionAlgorithm = CosmosEncryptionAlgorithm.AEAES_256_CBC_HMAC_SHA_256;

List<ClientEncryptionIncludedPath> paths = new ArrayList<>();
paths.add(path1);
paths.add(path2);

CosmosContainerProperties containerProperties =
    new CosmosContainerProperties("my-container", "/id");
containerProperties.setClientEncryptionPolicy(new ClientEncryptionPolicy(paths));
database.createEncryptionContainerAsync(containerProperties);
```
---

## <a name="read-and-write-encrypted-data"></a>读取和写入加密数据

### <a name="how-data-gets-encrypted"></a>如何加密数据

每当将文档写入 Azure Cosmos DB 时，SDK 都会查找加密策略，以确定需要加密的属性以及如何加密。 加密的结果是一个 base 64 字符串。

复杂类型的加密：

- 如果要加密的属性是 JSON 数组，数组的每个条目都会加密。

- 如果要加密的属性是 JSON 对象，只会加密对象的叶值。 中间子属性名称以纯文本形式保留。

### <a name="read-encrypted-items"></a>读取加密项

在发出点读取（按其 ID 和分区键提取单个项目）、查询或读取更改源时，无需显式操作即可对加密属性进行解密。 这是因为：

- SDK 会查找加密策略，以确定需要解密哪些属性。
- 加密的结果会嵌入值的原始 JSON 类型。

请注意，加密属性的解析及其后续解密仅基于请求返回的结果。 例如，如果 `property1` 已加密但投影到 `property2` (`SELECT property1 AS property2 FROM c`) 中，则 SDK 接收时不会将其标识为加密属性。

### <a name="filter-queries-on-encrypted-properties"></a>针对加密属性的筛选查询

编写对加密属性进行筛选的查询时，必须使用 `AddParameterAsync` 方法传递查询参数的值。 此方法采用以下参数：

- 查询参数的名称。
- 要在查询中使用的值。
- 加密属性的路径（如加密策略中定义的）。

> [!IMPORTANT]
> 加密属性只能在相等筛选器 (`WHERE c.property = @Value`) 中使用。 任何其他使用情况都将返回不可预知的和错误的查询结果。 这一约束将在 SDK 的下一版本中得到更好的执行。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
var queryDefinition = container.CreateQueryDefinition(
    "SELECT * FROM c where c.property1 = @Property1");
await queryDefinition.AddParameterAsync(
    "@Property1",
    1234,
    "/property1");
```

# <a name="java"></a>[Java](#tab/java)

```java
EncryptionSqlQuerySpec encryptionSqlQuerySpec = new EncryptionSqlQuerySpec(
    new SqlQuerySpec("SELECT * FROM c where c.property1 = @Property1"), container);
encryptionSqlQuerySpec.addEncryptionParameterAsync(
    new SqlParameter("@Property1", 1234), "/property1")
```
---

### <a name="reading-documents-when-only-a-subset-of-properties-can-be-decrypted"></a>当只能解密一部分属性时阅读文档

如果客户端无权访问用于加密属性的所有 CMK，则在数据读回后，只能对部分属性进行解密。 例如，如果 `property1` 用 key1 加密，而 `property2` 用 key2 加密，则仅具有 key1 访问权限的客户端应用程序仍可以读取数据，但不能读取 `property2`。 在这种情况下，必须通过 SQL 查询读取数据，并将客户端无法解密的属性投影出去：`SELECT c.property1, c.property3 FROM c`。

## <a name="cmk-rotation"></a>CMK 轮换

如果怀疑当前 CMK 已泄露，你可能需要“轮换”你的 CMK（即使用新的 CMK 而不是当前的 CMK）。 定期轮换 CMK 也是一种常见的安全做法。 若要执行此轮换，只需提供应该用于包装特定 DEK 的新 CMK 的密钥标识符。 请注意，此操作不会影响数据的加密，但会影响 DEK 的保护。 在轮换完成之前，不应撤销对以前的 CMK 的访问权限。

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
await database.RewrapClientEncryptionKeyAsync(
    "my-key",
    new EncryptionKeyWrapMetadata(
        keyStoreProvider.ProviderName,
        "akvKey",
        " https://<my-key-vault>.vault.azure.net/keys/<new-key>/<version>"));
```

# <a name="java"></a>[Java](#tab/java)

```java
database. rewrapClientEncryptionKey(
    "my-key",
    new EncryptionKeyWrapMetadata(
        "akvKey", " https://<my-key-vault>.vault.azure.net/keys/<new-key>/<version>"));
```
---

## <a name="next-steps"></a>后续步骤

- 大概了解如何[安全访问 Cosmos DB 中的数据](secure-access-to-data.md)。
- 详细了解[客户管理的密钥](how-to-setup-cmk.md)。