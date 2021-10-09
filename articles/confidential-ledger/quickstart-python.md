---
title: 快速入门 - Microsoft Azure 机密账本 Python 客户端库
description: 了解如何使用适用于 Python 的 Microsoft Azure 机密账本客户端库
author: msmbaldwin
ms.author: mbaldwin
ms.date: 04/27/2021
ms.service: confidential-ledger
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 1fd69155a6131565b81ba9684e5c2c077a5420d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671519"
---
# <a name="quickstart-microsoft-azure-confidential-ledger-client-library-for-python"></a>快速入门：适用于 Python 的 Microsoft Azure 机密账本客户端库

开始使用适用于 Python 的 Microsoft Azure 机密账本客户端库。 请遵循以下步骤安装包并试用基本任务的示例代码。

Microsoft Azure 机密账本是用于管理敏感数据记录的高度安全的新服务。 根据授权区块链模型，机密账本提供独特的数据完整性优势，例如保持不变（使账本只能追加）和防篡改（以确保所有记录保持完整）。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[API 参考文档](/python/api/overview/azure/keyvault-secrets-readme) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/confidentialledger) | [包（Python 包索引）管理库](https://pypi.org/project/azure-mgmt-confidentialledger/)| [包（Python 包索引）客户端库](https://pypi.org/project/azure-confidentialledger/)

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Python 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="set-up"></a>设置

本快速入门结合使用 Azure 标识库和 Azure CLI 或 Azure PowerShell，向 Azure 服务验证用户身份。 开发人员还可以使用 Visual Studio 或 Visual Studio Code 来验证其调用。 有关详细信息，请参阅[使用 Azure 标识客户端库对客户端进行身份验证](/python/api/overview/azure/identity-readme)。

### <a name="sign-in-to-azure"></a>登录到 Azure

[!INCLUDE [Sign in to Azure](../../includes/confidential-ledger-sign-in-azure.md)]

### <a name="install-the-packages"></a>安装包

在终端或命令提示符中，创建合适的项目文件夹，然后创建并激活 Python 虚拟环境，如[使用 Python 虚拟环境](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)中所述。

安装 Azure Active Directory 标识客户端库：

```terminal
pip install azure-identity
```

安装机密账本控制平面客户端库。

```terminal
pip install azure.mgmt.confidentialledger
```

安装机密账本数据平面客户端库。

```terminal
pip install azure.confidentialledger 
```

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](../../includes/confidential-ledger-rg-create.md)]

### <a name="register-the-microsoftconfidentialledger-resource-provider"></a>注册 microsoft.ConfidentialLedger 资源提供程序

[!INCLUDE [Register the microsoft.ConfidentialLedger resource provider](../../includes/confidential-ledger-register-rp.md)]

## <a name="create-your-python-app"></a>创建 python 应用

### <a name="initialization"></a>初始化

现在，我们可以开始编写 python 应用程序。  首先，我们将导入所需的包。

```python
# Import the Azure authentication library

from azure.identity import DefaultAzureCredential

## Import the control plane sdk

from azure.mgmt.confidentialledger import ConfidentialLedger as ConfidentialLedgerAPI
from azure.mgmt.confidentialledger.models import ConfidentialLedger

# import the data plane sdk

from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient
```

接下来，我们将使用 [DefaultAzureCredential 类](/python/api/azure-identity/azure.identity.defaultazurecredential) 对应用进行身份验证。

```python
credential = DefaultAzureCredential()
```

我们将通过设置要在应用程序中使用的某些变量来完成设置：资源组 (myResourceGroup)、要创建的账本名称以及数据平面客户端库使用的两个 url。

  > [!Important]
  > 每个账本必须具有全局唯一名称。 在以下示例中，将 \<your-unique-keyvault-name\> 替换为你的账本的名称。

```python
resource_group = "myResourceGroup"
ledger_name = "<your-unique-ledger-name>"
subscription_id = "<azure-subscription-id>"

identity_url = "https://identity.confidential-ledger.core.azure.com"
ledger_url = "https://" + ledger_name + ".confidential-ledger.azure.com"
```

### <a name="use-the-control-plane-client-library"></a>使用控制平面客户端库

控制平面客户端库 (azure.mgmt.confidentialledger) 允许对账本进行操作（如创建、修改和删除），列出与订阅关联的账本，并获取特定账本的详细信息。

在我们的代码中，我们将首先通过传递 ConfidentialLedgerAPI 凭据变量和 Azure 订阅 ID（两者均在上面设置）来创建控制平面客户端。  

```python
confidential_ledger_mgmt = ConfidentialLedgerAPI(
    credential, subscription_id
)
```

现在可以使用 `begin_create` 创建账本。 `begin_create` 函数需要三个参数：资源组、账本名称和“properties”对象。  

创建具有以下键和值的 `properties` 字典，并将其分配给变量。

```python
properties = {
    "location": "eastus",
    "tags": {},
    "properties": {
        "ledgerType": "Public",
        "aadBasedSecurityPrincipals": [],
    },
}

ledger_properties = ConfidentialLedger(**properties)
```

现在，将资源组、账本名称和 properties 对象传递给 `begin_create`。

```python
confidential_ledger_mgmt.ledger.begin_create(resource_group, ledger_name, ledger_properties)
```

若要验证账本是否成功创建，请使用 `get` 函数查看其详细信息。

```python
myledger = confidential_ledger_mgmt.ledger.get(resource_group, ledger_name)

print("Here are the details of your newly created ledger:")
print (f"- Name: {myledger.name}")
print (f"- Location: {myledger.location}")
print (f"- ID: {myledger.id}")

```

### <a name="use-the-data-plane-client-library"></a>使用数据平面客户端库

现在，我们有一个账本，我们将使用数据平面客户端库 (azure.confidentialledger) 与其进行交互。 

首先，我们将生成并保存机密账本证书。  

```python
identity_client = ConfidentialLedgerIdentityServiceClient(identity_url)
network_identity = identity_client.get_ledger_identity(
     ledger_id=ledger_name
)

ledger_tls_cert_file_name = "networkcert.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)
```

现在，我们可以结合使用网络证书和账本 URL 以及凭据来创建机密账本客户端。

```python
ledger_client = ConfidentialLedgerClient(
     endpoint=ledger_url, 
     credential=credential,
     ledger_certificate_path=ledger_tls_cert_file_name
)
```

我们已准备好写入账本。  我们将使用 `append_to_ledger` 函数执行此操作。

```python
append_result = ledger_client.append_to_ledger(entry_contents="Hello world!")
print(append_result.transaction_id)
```

print 函数将返回写入账本的事务 ID，该 ID 可用于检索写入账本的消息。

```python
entry = ledger_client.get_ledger_entry(transaction_id=append_result.transaction_id)
print(entry.contents)
```

print 函数将返回“Hello world!”，因为这是账本中对应于事务 ID 的消息。

## <a name="full-sample-code"></a>完整示例代码

```python
from azure.identity import DefaultAzureCredential

## Import control plane sdk

from azure.mgmt.confidentialledger import ConfidentialLedger as ConfidentialLedgerAPI
from azure.mgmt.confidentialledger.models import ConfidentialLedger

# import data plane sdk

from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

# Set variables

rg = "myResourceGroup"
ledger_name = "<unique-ledger-name>"
subscription_id = "<azure-subscription-id>"

identity_url = "https://identity.confidential-ledger.core.azure.com"
ledger_url = "https://" + ledger_name + ".eastus.cloudapp.azure.com"

# Authentication

# Need to do az login to get default credential to work

credential = DefaultAzureCredential()

# Control plane (azure.mgmt.confidentialledger)
# 
# initialize endpoint with credential and subscription

confidential_ledger_mgmt = ConfidentialLedgerAPI(
    credential, "<subscription-id>"
)

# Create properties dictionary for begin_create call 

properties = {
    "location": "eastus",
    "tags": {},
    "properties": {
        "ledgerType": "Public",
        "aadBasedSecurityPrincipals": [],
    },
}

ledger_properties = ConfidentialLedger(**properties)

# Create a ledger

foo = confidential_ledger_mgmt.ledger.begin_create(rg, ledger_name, ledger_properties)

# Get the details of the ledger you just created

print(f"{rg} / {ledger_name}")
 
print("Here are the details of your newly created ledger:")
myledger = confidential_ledger_mgmt.ledger.get(rg, ledger_name)

print (f"- Name: {myledger.name}")
print (f"- Location: {myledger.location}")
print (f"- ID: {myledger.id}")

# Data plane (azure.confidentialledger)
#
# Create a CL client

identity_client = ConfidentialLedgerIdentityServiceClient(identity_url)
network_identity = identity_client.get_ledger_identity(
     ledger_id=ledger_name
)

ledger_tls_cert_file_name = "networkcert.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)


ledger_client = ConfidentialLedgerClient(
     endpoint=ledger_url, 
     credential=credential,
     ledger_certificate_path=ledger_tls_cert_file_name
)

# Write to the ledger
append_result = ledger_client.append_to_ledger(entry_contents="Hello world!")
print(append_result.transaction_id)

# Read from the ledger
entry = ledger_client.get_ledger_entry(transaction_id=append_result.transaction_id)
print(entry.contents)
```

## <a name="clean-up-resources"></a>清理资源

其他 Microsoft Azure 机密账本文章可以基于本快速入门。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。

否则，当完成本文中创建的资源后，请使用 Azure CLI [az group delete](/cli/azure/group?#az_group_delete) 命令删除资源组及其包含的所有资源：

```azurecli
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>后续步骤

- [Microsoft Azure 机密账本概述](overview.md)
