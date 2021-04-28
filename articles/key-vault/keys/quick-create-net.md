---
title: 快速入门 - 适用于 .NET 的 Azure Key Vault 密钥客户端库（版本 4）
description: 了解如何使用 .NET 客户端库（版本 4）在 Azure 密钥保管库中创建、检索和删除密钥
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: 5c1e4d64ba3359a07dddbbf89774e31815935230
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818415"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>快速入门：适用于 .NET 的 Azure Key Vault 密钥客户端库 (SDK v4)

适用于 .NET 的 Azure Key Vault 密钥客户端库入门。 [Azure Key Vault](../general/overview.md) 是一项云服务，它为加密密钥提供了安全的存储。 可以安全地存储加密密钥、密码、证书和其他机密。 可以通过 Azure 门户创建和管理 Azure Key Vault。 本快速入门介绍如何使用 .NET 密钥客户端库在 Azure 密钥保管库中创建、检索和删除密钥

Key Vault 密钥客户端库资源：

[API 参考文档](/dotnet/api/azure.security.keyvault.keys) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [包 (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

有关 Key Vault 和密钥的详细信息，请参阅：
- [Key Vault 概述](../general/overview.md)
- [密钥概述](about-keys.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK 或更高版本](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault - 可以使用 [Azure 门户](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md) 或 [Azure PowerShell](../general/quick-create-powershell.md) 进行创建。

## <a name="setup"></a>设置

本快速入门使用 Azure 标识库向 Azure 服务进行用户身份验证。 开发人员还可以使用 Visual Studio 或 Visual Studio Code 来验证其调用。有关详细信息，请参阅[使用 Azure Identity 客户端库对客户端进行身份验证](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)。

### <a name="sign-in-to-azure"></a>登录 Azure

1. 运行 `login` 命令。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli-interactive
    az login
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
    ---

    如果 Azure CLI 或 Azure PowerShell 可以打开默认浏览器，它将这样做并加载 Azure 登录页。

    否则，请在 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 处打开浏览器页，然后输入终端中显示的授权代码。

2. 在浏览器中使用帐户凭据登录。

#### <a name="grant-access-to-your-key-vault"></a>授予对 Key Vault 的访问权限

针对密钥保管库创建一个访问策略，以便为用户帐户授予密钥权限

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <your-key-vault-name> -UserPrincipalName user@domain.com -PermissionsToSecrets delete,get,list,set,purge
```
---

### <a name="create-new-net-console-app"></a>创建新的 .NET 控制台应用

1. 在命令外壳中，运行以下命令以创建名为 `key-vault-console-app` 的项目：

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. 切换到新创建的 key-vault-console-app 目录，然后运行以下命令来生成项目：

    ```dotnetcli
    dotnet build
    ```

    生成输出不应包含警告或错误。
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>安装包

在命令外壳中，安装适用于 .NET 的 Azure Key Vault 密钥客户端库：

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
```

对于本快速入门，还需要安装适用于 Azure Identity 的 Azure SDK 客户端库：

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>设置环境变量

此应用程序使用 Key Vault 名称作为名为 `KEY_VAULT_NAME` 的环境变量。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```azurepowershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS 或 Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>对象模型

适用于 .NET 的 Azure Key Vault 密钥客户端库可用于管理密钥。 [代码示例](#code-examples)部分介绍了如何创建客户端，以及如何设置、检索和删除密钥。

## <a name="code-examples"></a>代码示例

### <a name="add-directives"></a>添加指令

在 Program.cs 的顶部添加以下指令：

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端

本快速入门使用登录用户向 Key Vault 进行身份验证，这是本地开发的首选方法。 对于部署到 Azure 的应用程序，应将托管标识分配给应用服务或虚拟机。有关详细信息，请参阅[托管标识概述](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

在下面的示例中，Key Vault 的名称将扩展为 Key Vault URI，格式为“https://\<your-key-vault-name\>.vault.azure.net”。 此示例使用 [Azure 标识库](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)的[“DefaultAzureCredential()”](/dotnet/api/azure.identity.defaultazurecredential)类，该类允许在具有不同选项的不同环境中使用相同代码提供标识。 有关向 Key Vault 进行身份验证的详细信息，请参阅[开发人员指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)。

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>保存密钥

对于此任务，请使用 [CreateKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync) 方法。 该方法的参数接受密钥名和[密钥类型](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype)。

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> 如果密钥名已存在，则上面的代码将创建该密钥的新版本。

### <a name="retrieve-a-key"></a>检索密钥

现在，可以使用 [GetKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync) 方法检索以前创建的密钥。

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>删除密钥

最后，让我们使用 [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) 和 [PurgeDeletedKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync) 方法从密钥保管库中删除并清除密钥。

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>示例代码

通过完成以下步骤，将 .NET Core 控制台应用修改为与 Key Vault 交互：

- 将 Program.cs 中的代码替换为以下代码：

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>测试和验证

1.  执行以下命令以生成项目

    ```dotnetcli
    dotnet build
    ```

1. 执行以下命令来运行应用。

    ```dotnetcli
    dotnet run
    ```

1. 出现提示时，输入一个密码值。 例如，mySecretPassword。

    随即显示以下输出的变体：

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库，存储了一个密钥，然后检索了该密钥。 

若要详细了解 Key Vault 以及如何将其与应用集成，请参阅以下文章：

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 阅读[密钥概述](about-keys.md)
- 请参阅[从应用服务应用程序访问 Key Vault 的教程](../general/tutorial-net-create-vault-azure-web-app.md)
- 请参阅[从虚拟机访问 Key Vault 的教程](../general/tutorial-net-virtual-machine.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 请参阅 [Key Vault 安全性概述](../general/security-features.md)
