---
title: 快速入门 – Azure Key Vault Python 客户端库 – 管理密钥
description: 了解如何使用 Python 客户端库在 Azure Key Vault 中创建、检索和删除密钥
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 6375d522089f7ff4d77761f941a8b754cfa528bc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748680"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>快速入门：适用于 Python 的 Azure Key Vault 客户端密钥库

适用于 Python 的 Azure Key Vault 客户端库入门。 请遵循以下步骤安装包并试用基本任务的示例代码。 通过使用 Key Vault 存储加密密钥，可以避免在代码中存储此类密钥，从而提高应用程序的安全性。

[API 参考文档](/python/api/overview/azure/keyvault-keys-readme) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [包（Python 包索引）](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python 2.7+ 或 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

本快速入门假设你在 Linux 终端窗口中运行 [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="set-up-your-local-environment"></a>设置本地环境

本快速入门结合使用 Azure Identity 库和 Azure CLI，向 Azure 服务验证用户身份。 开发人员还可以使用 Visual Studio 或 Visual Studio Code 来验证其调用。有关详细信息，请参阅[使用 Azure Identity 客户端库对客户端进行身份验证](/python/api/overview/azure/identity-readme)。

### <a name="sign-in-to-azure"></a>登录 Azure

1. 运行 `login` 命令。

    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。

    否则，请在 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 处打开浏览器页，然后输入终端中显示的授权代码。

2. 在浏览器中使用帐户凭据登录。

### <a name="install-the-packages"></a>安装包

1. 在终端或命令提示符中，创建合适的项目文件夹，然后创建并激活 Python 虚拟环境，如[使用 Python 虚拟环境](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)中所述。

1. 安装 Azure Active Directory 标识库：

    ```terminal
    pip install azure.identity
    ```


1. 安装 Key Vault 密钥客户端库：

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>创建资源组和 Key Vault

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>授予对 Key Vault 的访问权限

针对密钥保管库创建一个访问策略，以便为用户帐户授予机密权限。

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>设置环境变量

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

## <a name="create-the-sample-code"></a>创建示例代码

使用适用于 Python 的 Azure Key Vault 密钥客户端库，你可以管理加密密钥。 以下代码示例演示如何创建客户端、设置密钥、检索密钥和删除密钥。

创建包含此代码的名为 kv_keys.py 的文件。

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>运行代码

确保上一部分中的代码位于名为 kv_keys.py 的文件中。 然后，使用以下命令运行代码：

```terminal
python kv_keys.py
```

- 如果遇到权限错误，请确保已运行 [`az keyvault set-policy` 命令](#grant-access-to-your-key-vault)。
- 重新运行具有相同密钥名称的代码可能会产生错误：“(冲突)密钥 \<name\> 当前处于已删除但可恢复的状态。” 使用其他密钥名称。

## <a name="code-details"></a>代码详细信息

### <a name="authenticate-and-create-a-client"></a>进行身份验证并创建客户端

本快速入门使用登录用户向 Key Vault 进行身份验证，这是本地开发的首选方法。 对于部署到 Azure 的应用程序，应将托管标识分配给应用服务或虚拟机。有关详细信息，请参阅[托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)。

在下面的示例中，密钥保管库的名称扩展为密钥保管库 URI，格式为 `https://\<your-key-vault-name\>.vault.azure.net`。 此示例使用 ['DefaultAzureCredential()' ](/python/api/azure-identity/azure.identity.defaultazurecredential)类，该类允许在具有不同选项的不同环境中使用相同代码提供标识。 有关详细信息，请参阅[默认 Azure 凭据身份验证](/python/api/overview/azure/identity-readme)。 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>保存密钥

获取密钥保管库的客户端对象后，可以使用 [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-) 方法存储密钥： 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

还可以使用 [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) 或 [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-)。

调用 `create` 方法会生成对密钥保管库的 Azure REST API 的调用。

在处理请求时，Azure 使用你提供给客户端的凭据对象，对调用方的标识（服务主体）进行身份验证。

## <a name="retrieve-a-key"></a>检索密钥

若要读取 Key Vault 中的密钥，请使用 [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-) 方法：

```python
retrieved_key = client.get_key(keyName)
 ```

还可以使用 Azure CLI 命令 [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) 来验证是否设置了密钥。

### <a name="delete-a-key"></a>删除密钥

若要删除密钥，请使用 [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-) 方法：

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

`begin_delete_key` 方法是异步方法，将返回一个轮询器对象。 调用轮询器的 `result` 方法等待其完成。

可以使用 Azure CLI 命令 [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) 来验证是否已删除密钥。

删除密钥后，该密钥会在一段时间内保持已删除但可恢复状态。 如果再次运行该代码，请使用其他密钥名称。

## <a name="clean-up-resources"></a>清理资源

如果还想进行[证书](../certificates/quick-create-python.md)和[机密](../secrets/quick-create-python.md)相关试验，可以重复使用在本文中创建的 Key Vault。

否则，当完成本文中创建的资源后，请使用以下命令删除资源组及其包含的所有资源：

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>后续步骤

- [Azure 密钥保管库概述](../general/overview.md)
- [保护对密钥保管库的访问](../general/security-features.md)
- [Azure Key Vault 开发人员指南](../general/developers-guide.md)
- [Key Vault 安全性概述](../general/security-features.md)
- [使用 Key Vault 进行身份验证](../general/authentication.md)
