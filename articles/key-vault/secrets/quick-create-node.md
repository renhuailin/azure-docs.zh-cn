---
title: 快速入门 - 适用于 JavaScript 的 Azure Key Vault 机密客户端库（版本 4）
description: 了解如何使用 JavaScript 客户端库在 Azure 密钥保管库中创建、检索和删除机密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 53adc99c236371dcbd39e59edae1fecc394091de
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760016"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>快速入门：适用于 JavaScript 的 Azure Key Vault 机密客户端库（版本 4）

适用于 JavaScript 的 Azure Key Vault 机密客户端库入门。 [Azure Key Vault](../general/overview.md) 是一项云服务，它为机密提供了安全的存储。 可以安全地存储密钥、密码、证书和其他机密。 可以通过 Azure 门户创建和管理 Azure Key Vault。 本快速入门介绍如何使用 JavaScript 客户端库在 Azure 密钥保管库中创建、检索和删除机密

Key Vault 客户端库资源：

[API 参考文档](/javascript/api/overview/azure/key-vault-index) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [包 (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

有关 Key Vault 和机密的详细信息，请参阅：
- [Key Vault 概述](../general/overview.md)
- [机密概述](about-secrets.md)。

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

```terminal
mkdir key-vault-node-app
```

1. 切换到新创建的 key-vault-node-app 目录，然后运行 init 命令以初始化节点项目：

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>安装 Key Vault 包

在控制台窗口中，安装适用于 Node.js 的 Azure Key Vault [机密库](https://www.npmjs.com/package/@azure/keyvault-secrets)。

```terminal
npm install @azure/keyvault-secrets
```

安装 [azure.identity](https://www.npmjs.com/package/@azure/identity) 包以对 Key Vault 进行身份验证

```terminal
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

针对密钥保管库创建一个访问策略，以便为用户帐户授予机密权限

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>代码示例

以下代码示例演示如何创建客户端以及设置、检索和删除机密。 

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
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端

本快速入门使用登录用户向 Key Vault 进行身份验证，这是本地开发的首选方法。 对于部署到 Azure 的应用程序，应将托管标识分配给应用服务或虚拟机。有关详细信息，请参阅[托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)。

在下面的示例中，Key Vault 的名称将扩展为 Key Vault URI，格式为“https://\<your-key-vault-name\>.vault.azure.net”。 此示例使用 [Azure 标识库](/javascript/api/overview/azure/identity-readme)中的[“DefaultAzureCredential()”](/javascript/api/@azure/identity/defaultazurecredential)类。利用该类，我们可以在具有不同选项的不同环境中使用相同的代码来提供标识。 有关向 Key Vault 进行身份验证的详细信息，请参阅[开发人员指南](../general/developers-guide.md#authenticate-to-key-vault-in-code)。

将以下代码添加到“main()”函数

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>保存机密

应用程序通过身份验证后，你可以使用 [setSecret 方法](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_)将机密放入密钥保管库。此操作需要使用机密的名称，本示例中使用“mySecret”。  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>检索机密

现在，你可以使用 [getSecret 方法](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_)检索以前设置的值。

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

机密现已保存为 `retrievedSecret.value`。

### <a name="delete-a-secret"></a>删除机密

最后，让我们使用 [beginDeleteSecret](/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) 和 [purgeDeletedSecret](/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_) 方法从密钥保管库中删除并清除机密。

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
```

## <a name="sample-code"></a>示例代码

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
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>测试和验证

1. 执行以下命令来运行应用。

    ```terminal
    npm install
    node index.js
    ```

1. 出现提示时，输入一个密码值。 例如，mySecretPassword。

    随即显示以下输出的变体：

    ```azurecli
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.  
    Purging your secret from <your-unique-keyvault-name> ... done.   
    ```


## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库、存储了一个机密，然后检索了该机密。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 阅读 [Azure Key Vault 机密概述](about-secrets.md)
- 如何[保护对密钥保管库的访问](../general/security-features.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 请参阅 [Key Vault 安全性概述](../general/security-features.md)
