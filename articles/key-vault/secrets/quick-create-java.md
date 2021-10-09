---
title: 快速入门 - 适用于 Java 的 Azure Key Vault 机密客户端库
description: 提供适用于 Java 的 Azure Key Vault 机密客户端库的快速入门。
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 7497db682cd8fac12cd93a83c3765862898455ee
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763474"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>快速入门：适用于 Java 的 Azure Key Vault 机密客户端库
适用于 Java 的 Azure Key Vault 机密客户端库入门。 请遵循以下步骤安装包并试用基本任务的示例代码。

其他资源：

* [源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)
* [API 参考文档](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [产品文档](index.yml)
* [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>先决条件
- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java 开发工具包 (JDK)](/java/azure/jdk/) 8 或更高版本
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

本快速入门假设你在 Linux 终端窗口中运行 [Azure CLI](/cli/azure/install-azure-cli) 和 [Apache Maven](https://maven.apache.org)。

## <a name="setting-up"></a>设置
本快速入门结合使用 Azure Identity 库和 Azure CLI，向 Azure 服务验证用户身份。 开发人员还可以使用 Visual Studio 或 Visual Studio Code 来验证其调用。有关详细信息，请参阅[使用 Azure Identity 客户端库对客户端进行身份验证](/java/api/overview/azure/identity-readme)。

### <a name="sign-in-to-azure"></a>登录 Azure
1. 运行 `login` 命令。

    ```azurecli-interactive
    az login
    ```

   如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。

   否则，请在浏览器中打开 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，然后输入终端中显示的授权代码。

2. 在浏览器中使用帐户凭据登录。

### <a name="create-a-new-java-console-app"></a>创建新的 Java 控制台应用
在控制台窗口中，使用 `mvn` 命令创建名为 `akv-secrets-java` 的新 Java 控制台应用。

```console
mvn archetype:generate -DgroupId=com.keyvault.secrets.quickstart
                       -DartifactId=akv-secrets-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

生成项目的输出将如下所示：

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-secrets-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

将目录更改为新创建的 `akv-secrets-java/` 文件夹。

```console
cd akv-secrets-java
```

### <a name="install-the-package"></a>安装包
在文本编辑器中打开 pom.xml 文件。 将以下依赖项元素添加到依赖项组。

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>创建资源组和 Key Vault
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>授予对 Key Vault 的访问权限
针对密钥保管库创建一个访问策略，以便为用户帐户授予机密权限。

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --secret-permissions delete get list set purge
```

#### <a name="set-environment-variables"></a>设置环境变量
此应用程序使用密钥保管库名称作为名为 `KEY_VAULT_NAME` 的环境变量。

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

## <a name="object-model"></a>对象模型
适用于 Java 的 Azure Key Vault 机密客户端库可用于管理机密。 [代码示例](#code-examples)部分介绍如何创建客户端以及设置、检索和删除密码。

整个控制台应用在[下面](#sample-code)。

## <a name="code-examples"></a>代码示例
### <a name="add-directives"></a>添加指令
将以下指令添加到代码的顶部：

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端
本快速入门使用登录用户向 Key Vault 进行身份验证，这是本地开发的首选方法。 对于部署到 Azure 的应用程序，应将托管标识分配给应用服务或虚拟机。 有关详细信息，请参阅[托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)。

在下面的示例中，密钥保管库的名称将扩展为密钥保管库 URI，格式为 `https://\<your-key-vault-name\>.vault.azure.net`。 此示例使用 ['DefaultAzureCredential()'](/java/api/com.azure.identity.defaultazurecredential) 类，该类允许在具有不同选项的不同环境中使用相同代码提供标识。 有关详细信息，请参阅[默认 Azure 凭据身份验证](/java/api/overview/azure/identity-readme)。

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>保存机密
现在，应用程序已进行身份验证，你可使用 `secretClient.setSecret` 方法将机密放入密钥保管库。 这要求提供机密名称 - 在此示例中，我们已将值“mySecret”分配给 `secretName` 变量。

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show) 命令来验证是否设置了机密：

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>检索机密
现在，可使用 `secretClient.getSecret` 方法检索之前设置的机密。

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

现可使用 `retrievedSecret.getValue()` 访问检索到的机密的值。

### <a name="delete-a-secret"></a>删除机密
最后，使用 `secretClient.beginDeleteSecret` 方法从密钥保管库中删除机密。

删除机密的操作耗时很长，你可轮询其进度或等待操作完成。

```java
SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
deletionPoller.waitForCompletion();
```

可使用 [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show) 命令来验证是否已删除机密：

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

## <a name="clean-up-resources"></a>清理资源
可以使用 Azure CLI 或 Azure PowerShell 来删除不再需要的 Key Vault 和相应的资源组。

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>示例代码
```java
package com.keyvault.secrets.quickstart;

import java.io.Console;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(keyVaultUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();

        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Please provide the value of your secret > ");
        
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");
        System.out.println("Forgetting your secret.");
        
        secretValue = "";
        System.out.println("Your secret's value is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret's value is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
        deletionPoller.waitForCompletion();

        System.out.println("done.");
    }
}
```

## <a name="next-steps"></a>后续步骤
在本快速入门中，你创建了一个密钥保管库、存储了一个机密、检索了该机密，然后将它删除了。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 参阅 [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- 如何[保护对密钥保管库的访问](../general/security-features.md)
