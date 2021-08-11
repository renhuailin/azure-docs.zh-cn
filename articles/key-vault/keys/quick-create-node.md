---
title: 快速入门 - 适用于 JavaScript 的 Azure Key Vault 密钥客户端库（版本 4）
description: 了解如何使用 JavaScript 客户端库在 Azure 密钥保管库中创建、检索和删除密钥
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: a3d63a65edaca68bbb60bf7b8901e341da218377
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733531"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>快速入门：适用于 JavaScript 的 Azure Key Vault 密钥客户端库（版本 4）

适用于 JavaScript 的 Azure Key Vault 密钥客户端库入门。 [Azure Key Vault](../general/overview.md) 是一项云服务，它为加密密钥提供了安全的存储。 可以安全地存储密钥、密码、证书和其他机密。 可以通过 Azure 门户创建和管理 Azure Key Vault。 本快速入门介绍如何使用 JavaScript 密钥客户端库在 Azure 密钥保管库中创建、检索和删除密钥

Key Vault 客户端库资源：

[API 参考文档](/javascript/api/overview/azure/key-vault-index) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [包 (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

有关 Key Vault 和密钥的详细信息，请参阅：
- [Key Vault 概述](../general/overview.md)
- [密钥概述](about-keys.md)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 适用于操作系统的当前 [Node.js](https://nodejs.org)。
- [Azure CLI](/cli/azure/install-azure-cli)
- Key Vault - 可以使用 [Azure 门户](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md) 或 [Azure PowerShell](../general/quick-create-powershell.md) 进行创建

本快速入门假设你运行 [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sign-in-to-azure"></a>登录 Azure

1. 运行 `login` 命令。

    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。

    否则，请在 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 处打开浏览器页，然后输入终端中显示的授权代码。

2. 在浏览器中使用帐户凭据登录。

## <a name="create-new-nodejs-application"></a>创建新的 Node.js 应用程序

接下来，创建可部署到云的 Node.js 应用程序。 

1. 在命令外壳中，创建一个名为 `key-vault-node-app` 的文件夹：

```azurecli
mkdir key-vault-node-app
```

1. 切换到新创建的 key-vault-node-app 目录，然后运行 init 命令以初始化节点项目：

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>安装 Key Vault 包

在控制台窗口中，安装适用于 Node.js 的 Azure Key Vault [密钥库](https://www.npmjs.com/package/@azure/keyvault-keys)。

```azurecli
npm install @azure/keyvault-keys
```

安装 [azure.identity](https://www.npmjs.com/package/@azure/identity) 包以对 Key Vault 进行身份验证

```azurecli
npm install @azure/identity
```

## <a name="set-environment-variables"></a>设置环境变量

此应用程序使用 Key Vault 名称作为名为 `KEY_VAULT_NAME` 的环境变量。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS 或 Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>授予对 Key Vault 的访问权限

针对密钥保管库创建一个访问策略，以便为用户帐户授予密钥权限

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>代码示例

以下代码示例展示了如何创建客户端，以及如何设置、检索和删除密钥。 

### <a name="set-up-the-app-framework"></a>设置应用框架

1. 创建新的文本文件并将其另存为“index.js”

1. 添加所需调用以加载 Azure 和 Node.js 模块

1. 为程序创建结构，包括基本的异常处理

```javascript
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
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>添加指令

将以下指令添加到代码的顶部：

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端

本快速入门使用登录用户向 Key Vault 进行身份验证，这是本地开发的首选方法。 对于部署到 Azure 的应用程序，应将托管标识分配给应用服务或虚拟机。有关详细信息，请参阅[托管标识概述](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

在下面的示例中，Key Vault 的名称将扩展为 Key Vault URI，格式为“https://\<your-key-vault-name\>.vault.azure.net”。 此示例使用 [Azure 标识库](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)的[“DefaultAzureCredential()”](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential)类，该类允许在具有不同选项的不同环境中使用相同代码提供标识。 有关向 Key Vault 进行身份验证的详细信息，请参阅[开发人员指南](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)。

将以下代码添加到“main()”函数

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>保存密钥

现在，应用程序已进行身份验证，可以使用 [createKey 方法](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_)将密钥放入 keyvault 中，此方法的参数接受密钥名和[密钥类型](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype)

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>检索密钥

现在，可以使用 [getKey 方法](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_)检索以前设置的值。

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>删除密钥

最后，让我们使用 [beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) 和 [purgeDeletedKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_) 方法从密钥保管库中删除并清除密钥。

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>示例代码

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

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
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log(`Purging your key from ${keyVaultName} ...`);
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>测试和验证

执行以下命令来运行应用。

```azurecli
npm install
npm index.js
```

随即显示以下输出的变体：

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库，存储了一个密钥，然后检索了该密钥。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 阅读 [Azure Key Vault 密钥概述](about-keys.md)
- 如何[保护对密钥保管库的访问](../general/security-features.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 请参阅 [Key Vault 安全性概述](../general/security-features.md)
