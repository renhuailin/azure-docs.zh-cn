---
title: 快速入门 - 适用于 JavaScript 的 Azure Key Vault 证书客户端库（版本 4）
description: 了解如何使用 JavaScript 客户端库在 Azure 密钥保管库中创建、检索和删除证书
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: d6fa93319e9e2ecdd666a92f816df5a63a4f477f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748669"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>快速入门：适用于 JavaScript 的 Azure Key Vault 证书客户端库（版本 4）

适用于 JavaScript 的 Azure Key Vault 证书客户端库入门。 [Azure Key Vault](../general/overview.md) 是一项云服务，它为证书提供了安全的存储。 可以安全地存储密钥、密码、证书和其他机密。 可以通过 Azure 门户创建和管理 Azure Key Vault。 本快速入门介绍如何使用 JavaScript 客户端库在 Azure 密钥保管库中创建、检索和删除证书

Key Vault 客户端库资源：

[API 参考文档](/javascript/api/overview/azure/key-vault-index) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [包 (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

有关 Key Vault 和证书的详细信息，请参阅：
- [Key Vault 概述](../general/overview.md)
- [证书概述](about-certificates.md)。

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

在控制台窗口中，安装适用于 Node.js 的 Azure Key Vault [证书库](https://www.npmjs.com/package/@azure/keyvault-certificates)。

```azurecli
npm install @azure/keyvault-certificates
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

针对密钥保管库创建一个访问策略，以便为用户帐户授予证书权限

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>代码示例

以下代码示例展示了如何创建客户端，以及如何设置、检索和删除证书。 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端

本快速入门使用登录用户向 Key Vault 进行身份验证，这是本地开发的首选方法。 对于部署到 Azure 的应用程序，应将托管标识分配给应用服务或虚拟机。有关详细信息，请参阅[托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)。

在下面的示例中，Key Vault 的名称将扩展为 Key Vault URI，格式为“https://\<your-key-vault-name\>.vault.azure.net”。 此示例使用 [Azure 标识库](/javascript/api/overview/azure/identity-readme)的[“DefaultAzureCredential()”](/javascript/api/@azure/identity/defaultazurecredential)类，该类允许在具有不同选项的不同环境中使用相同代码提供标识。 有关向 Key Vault 进行身份验证的详细信息，请参阅[开发人员指南](../general/developers-guide.md#authenticate-to-key-vault-in-code)。

将以下代码添加到“main()”函数

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>保存证书

现在，应用程序已进行身份验证，可以使用 [beginCreateCertificate method](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_) 方法将证书放入 keyvault 中，这需要证书的名称和具有[证书策略属性](/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties)的证书策略[证书策略](/javascript/api/@azure/keyvault-certificates/certificatepolicy)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> 如果证书名已存在，则上面的代码将创建该证书的新版本。
### <a name="retrieve-a-certificate"></a>检索证书

现在，可以使用 [getCertificate 方法](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption)检索以前设置的值。

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>删除证书

最后，让我们使用 [beginDeleteCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) 和 [purgeDeletedCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_) 方法从密钥保管库中删除并清除证书。

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>示例代码

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>测试和验证

执行以下命令来运行应用。

```cmd
npm install
npm index.js
```

随即显示以下输出的变体：

```azurecli
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库，存储了一个证书，然后检索了该证书。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 阅读[证书概述](about-certificates.md)
- 请参阅[从应用服务应用程序访问 Key Vault 的教程](../general/tutorial-net-create-vault-azure-web-app.md)
- 请参阅[从虚拟机访问 Key Vault 的教程](../general/tutorial-net-virtual-machine.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 请参阅 [Key Vault 安全性概述](../general/security-features.md)
