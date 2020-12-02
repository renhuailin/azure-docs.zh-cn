---
title: 快速入门 - 适用于 JavaScript 的 Azure Key Vault 客户端库 (v4)
description: 了解如何使用 JavaScript 客户端库在 Azure 密钥保管库中创建、检索和删除机密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 73a12edb4576fe26fbb8e0918566aa8e292e35fc
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184308"
---
# <a name="quickstart-azure-key-vault-client-library-for-javascript-v4"></a>快速入门：适用于 JavaScript 的 Azure Key Vault 客户端库 (v4)

适用于 JavaScript 的 Azure Key Vault 机密客户端库入门。 请遵循以下步骤安装包并试用基本任务的示例代码。

[API 参考文档](/javascript/api/overview/azure/key-vault-index) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [包 (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 适用于操作系统的当前 [Node.js](https://nodejs.org)。
- [Azure CLI](/cli/azure/install-azure-cli)

本快速入门假设你在 Linux 终端窗口中运行 [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="setting-up"></a>设置
本快速入门结合使用 Azure Identity 库和 Azure CLI，向 Azure 服务验证用户身份。 开发人员还可以使用 Visual Studio 或 Visual Studio Code 来验证其调用。有关详细信息，请参阅[使用 Azure Identity 客户端库对客户端进行身份验证](/javascript/api/overview/azure/identity-readme)。

### <a name="sign-in-to-azure"></a>登录 Azure

1. 运行 `login` 命令。

    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。

    否则，请在 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 处打开浏览器页，然后输入终端中显示的授权代码。

2. 在浏览器中使用帐户凭据登录。

### <a name="install-the-package"></a>安装包

在控制台窗口中，安装适用于 Node.js 的 Azure Key Vault 机密库。

```console
npm install @azure/keyvault-secrets
```

本快速入门还需要安装 azure.identity 包：

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>创建资源组和 Key Vault

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>授予对 Key Vault 的访问权限

针对密钥保管库创建一个访问策略，以便为用户帐户授予机密权限。

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

#### <a name="set-environment-variables"></a>设置环境变量

此应用程序使用 Key Vault 名称作为名为 `KEY_VAULT_NAME` 的环境变量。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS 或 Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>对象模型

适用于 JavaScript 的 Azure Key Vault 机密客户端库可用于管理机密。 以下代码示例演示如何创建客户端以及设置、检索和删除机密。

## <a name="code-examples"></a>代码示例

### <a name="add-directives"></a>添加指令

将以下指令添加到代码的顶部：

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端

本快速入门使用登录用户向 Key Vault 进行身份验证，这是本地开发的首选方法。 对于部署到 Azure 的应用程序，应将托管标识分配给应用服务或虚拟机。有关详细信息，请参阅[托管标识概述](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

在下面的示例中，Key Vault 的名称将扩展为 Key Vault URI，格式为“https://\<your-key-vault-name\>.vault.azure.net”。 此示例使用 ['DefaultAzureCredential()' ](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential)类，该类允许在具有不同选项的不同环境中使用相同代码提供标识。 有关详细信息，请参阅[默认 Azure 凭据身份验证](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)。 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>保存机密

应用程序通过身份验证后，你可以使用 [client.setSecret 方法](/javascript/api/@azure/keyvault-secrets/secretclient?#setsecret-string--string--setsecretoptions-)将机密放入密钥保管库。此操作需要使用机密的名称，本示例中使用“mySecret”。  

```javascript
await client.setSecret(secretName, secretValue);
```

可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show) 命令来验证是否设置了机密：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>检索机密

现在，可以使用 [client.getSecret 方法](/javascript/api/@azure/keyvault-secrets/secretclient?#getsecret-string--getsecretoptions-)检索以前设置的值。

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

机密现已保存为 `retrievedSecret.value`。

### <a name="delete-a-secret"></a>删除机密

最后，使用 [client.beginDeleteSecret 方法](/javascript/api/@azure/keyvault-secrets/secretclient?#begindeletesecret-string--begindeletesecretoptions-)从密钥保管库中删除机密。

```javascript
await client.beginDeleteSecret(secretName)
```

可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show) 命令来验证是否已删除机密：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>清理资源

可以使用 Azure CLI 或 Azure PowerShell 来删除不再需要的 Key Vault 和相应的资源组。

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>代码示例

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库、存储了一个机密，然后检索了该机密。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 如何[保护对密钥保管库的访问](../general/secure-your-key-vault.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 查看 [Azure Key Vault 最佳做法](../general/best-practices.md)
