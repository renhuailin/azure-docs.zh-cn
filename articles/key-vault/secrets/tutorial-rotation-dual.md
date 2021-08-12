---
title: 有关具有两组凭据的资源的轮换教程
description: 通过本教程了解如何自动轮换使用两组身份验证凭据的资源的机密。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: e2da033484d84ecce26a4882c082dfdee8c355c6
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113550887"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>自动轮换使用两组身份验证凭据的资源的机密

向 Azure 服务进行身份验证的最佳方法是使用[托管标识](../general/authentication.md)，但某些情况下无法做到这一点。 在此类情况下，将使用访问密钥或密码。 应经常轮换访问密钥和密码。

本教程介绍如何定期自动轮换使用两组身份验证凭据的数据库和服务的机密。 具体而言，本教程演示了如何将 Azure Key Vault 中存储的 Azure 存储帐户密钥作为机密进行轮换。 你将使用由 Azure 事件网格通知触发的函数。 

> [!NOTE]
> 如果为委托的存储帐户访问提供共享访问签名令牌，则可在 Key Vault 中自动管理存储帐户密钥。 有些服务需要具有访问密钥的存储帐户连接字符串。 对于这种情况，建议采用此解决方案。

下面是本教程中介绍的轮换解决方案： 

![显示轮换解决方案的关系图。](../media/secrets/rotation-dual/rotation-diagram.png)

在此解决方案中，Azure Key Vault 将存储帐户的单个访问密钥存储为同一机密的不同版本，在后续版本中作为主密钥和辅助密钥交替使用。 当一个访问密钥存储到最新版本的机密中时，将重新生成备用密钥并将其作为最新版本的机密添加到 Key Vault 中。 该解决方案提供了应用程序的完整轮换周期，以便刷新到重新生成的最新密钥。 

1. 在机密过期之前的 30 天，Key Vault 会向事件网格发布“即将过期”事件。
1. 事件网格会检查事件订阅，并使用 HTTP POST 调用已订阅该事件的函数应用终结点。
1. 函数应用会标识备用密钥（而不是最新密钥），并调用存储帐户来重新生成该密钥。
1. 函数应用将重新生成的新密钥添加到 Azure Key Vault 中，作为机密的新版本。

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 [免费创建一个。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure [Cloud Shell](https://shell.azure.com/)。 本教程将门户 Cloud Shell 与 PowerShell 环境结合使用
* Azure Key Vault。
* 两个 Azure 存储帐户。

如果当前没有密钥保管库和存储帐户，可使用此部署链接：

[![标记为“部署到 Azure”的链接。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. 在“资源组”下，选择“新建”。 将组命名为“vaultrotation”，然后选择“确定” 。
1. 选择“查看 + 创建”。
1. 选择“创建”  。

    ![显示如何创建资源组的屏幕截图。](../media/secrets/rotation-dual/dual-rotation-1.png)

现在，你拥有一个密钥保管库和两个存储帐户。 可运行以下命令，在 Azure CLI 或 Azure PowerShell 中验证此设置：
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

结果类似于以下输出：

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>创建和部署密钥轮换函数

接下来，你将创建一个使用系统托管标识的函数应用，以及其他必需组件。 还将为存储帐户密钥部署轮换函数。

函数应用轮换函数需要以下组件和配置：
- 一个 Azure 应用服务计划
- 一个用于管理函数应用触发器的存储帐户
- 用于访问 Key Vault 中的机密的访问策略
- 向函数应用分配的存储帐户密钥操作员服务角色，分配目的是使其可可访问存储帐户访问密钥
- 一个具有事件触发器和 HTTP 触发器的密钥轮换函数（按需轮换）
- SecretNearExpiry 事件的事件网格事件订阅

1. 选择 Azure 模板部署链接： 

   [![Azure 模板部署链接。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. 在“资源组”列表中选择“vaultrotation” 。
1. 在“存储帐户 RG”框中，输入存储帐户所在的资源组的名称。 如果要部署密钥轮换函数的资源组中已存在你的存储帐户，请保留默认值 [resourceGroup().name]。
1. 在“存储帐户名称”框中，输入包含要轮换的访问密钥的存储帐户名称。 如果你使用在[先决条件](#prerequisites)中创建的存储帐户，请保留默认值 [concat(resourceGroup().name, 'storage')]。
1. 在“Key Vault RG”框中，输入密钥保管库所在的资源组的名称。 如果要部署密钥轮换函数的资源组中已存在你的密钥保管库，请保留默认值 [resourceGroup().name]。
1. 在“密钥保管库名称”框中，输入密钥保管库的名称。 如果你使用在[先决条件](#prerequisites)中创建的存储帐户，请保留默认值 [concat(resourceGroup().name, '-kv')]。
1. 在“应用服务计划类型”框中，选择“托管计划”。 仅当密钥保管库位于防火墙之后时才需要“高级计划”。
1. 在“函数应用名称”框中，输入函数应用的名称。
1. 在“机密名称”框中，输入要在其中存储访问密钥的机密的名称。
1. 在“存储库 URL”框中，输入函数代码的 GitHub 位置。 在本教程中，你可以使用 https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git。
1. 选择“查看 + 创建”。
1. 选择“创建”。

   ![演示如何创建和部署函数的屏幕截图。](../media/secrets/rotation-dual/dual-rotation-2.png)

完成上述步骤后，你将获得一个存储帐户、一个服务器场、一个函数应用和 Application Insights。 部署完成后，你将看到以下页面：

   ![显示“部署已完成”页的屏幕截图。](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> 如果遭遇失败，可选择“重新部署”来完成组件的部署。


可以在 [Azure 示例](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell)中找到轮换函数的部署模板和代码。

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>将存储帐户访问密钥添加到 Key Vault

首先，设置访问策略，以向用户主体授予“管理机密”权限：
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

现在，可使用存储帐户访问密钥作为值来创建新的机密。 要使轮换函数可在存储帐户中重新生成密钥，还需要提供要添加到机密的存储帐户资源 ID、机密有效期和密钥 ID。

确定存储帐户资源 ID。 可在 `id` 属性中找到该值。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

列出存储帐户访问密钥，以便可获取密钥值：
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

将机密和存储帐户资源 ID 添加到过期日期设置为明天、有效期为 60 天的密钥保管库。使用检索到的 `key1Value` 和 `storageAccountResourceId` 的值运行此命令：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

上述机密会在几分钟内触发 `SecretNearExpiry` 事件。 此事件转而将触发函数来轮换过期时间设置为 60 天的机密。 在该配置中，“SecretNearExpiry”事件将每 30 天（过期前 30 天）触发一次，并且轮换函数将在 key1 与 key2 之间交替轮换。

可检索存储帐户密钥和 Key Vault 机密并对其进行比较，从而验证是否重新生成了访问密钥。

使用此命令获取机密信息：
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

请注意，`CredentialId` 已更新为备用 `keyName`，并且 `value` 已重新生成：

![显示第一个存储帐户的 a z keyvault secret show 命令输出的屏幕截图。](../media/secrets/rotation-dual/dual-rotation-4.png)

检索访问密钥以比较值：
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

请注意，密钥的 `value` 与密钥保管库中的机密相同：

![显示第一个存储帐户的 a z storage account keys list 命令输出的屏幕截图。](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>添加存储帐户以进行轮换

可重复使用同一个函数应用来为多个存储帐户轮换密钥。 

若要将存储帐户密钥添加到现有函数以进行轮换，你需要：
- 向函数应用分配的存储帐户密钥操作员服务角色，分配目的是使它可访问存储帐户访问密钥。
- SecretNearExpiry 事件的事件网格事件订阅。

1. 选择 Azure 模板部署链接： 

   [![Azure 模板部署链接。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. 在“资源组”列表中选择“vaultrotation” 。
1. 在“存储帐户 RG”框中，输入存储帐户所在的资源组的名称。 如果要部署密钥轮换函数的资源组中已存在你的存储帐户，请保留默认值 [resourceGroup().name]。
1. 在“存储帐户名称”框中，输入包含要轮换的访问密钥的存储帐户名称。
1. 在“Key Vault RG”框中，输入密钥保管库所在的资源组的名称。 如果要部署密钥轮换函数的资源组中已存在你的密钥保管库，请保留默认值 [resourceGroup().name]。
1. 在“密钥保管库名称”框中，输入密钥保管库的名称。
1. 在“函数应用名称”框中，输入函数应用的名称。
1. 在“机密名称”框中，输入要在其中存储访问密钥的机密的名称。
1. 选择“查看 + 创建”。
1. 选择“创建”  。

   ![显示如何创建其他存储帐户的屏幕截图。](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>将其他存储帐户访问密钥添加到 Key Vault

确定存储帐户资源 ID。 可在 `id` 属性中找到该值。
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

列出存储帐户访问密钥，以便可获取密钥 2 值：
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

将机密和存储帐户资源 ID 添加到过期日期设置为明天、有效期为 60 天的密钥保管库。使用检索到的 `key2Value` 和 `storageAccountResourceId` 的值运行此命令：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

使用此命令获取机密信息：
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

请注意，`CredentialId` 已更新为备用 `keyName`，并且 `value` 已重新生成：

![显示第二个存储帐户的 a z keyvault secret show 命令输出的屏幕截图。](../media/secrets/rotation-dual/dual-rotation-8.png)

检索访问密钥以比较值：
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

请注意，密钥的 `value` 与密钥保管库中的机密相同：

![显示第二个存储帐户的 a z storage account keys list 命令输出的屏幕截图。](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>两组凭据的 Key Vault 轮换函数

两组凭据和几个现成函数的轮换函数模板：

- [项目模板](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Redis 缓存](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [存储帐户](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> 上述轮换函数由社区成员（而不是 Microsoft）创建。 任何 Microsoft 支持计划或服务都不支持社区 Azure 函数，这些函数按原样提供，无任何形式的保证。

## <a name="next-steps"></a>后续步骤

- 教程：[一组凭据的机密轮换](./tutorial-rotation.md)
- 概述：[通过 Azure 事件网格监视 Key Vault](../general/event-grid-overview.md)
- 如何：[在 Azure 门户中创建你的第一个函数](../../azure-functions/functions-get-started.md)
- 如何：[Key Vault 机密发生更改时接收电子邮件](../general/event-grid-logicapps.md)
- 参考：[Azure Key Vault 的 Azure 事件网格事件架构](../../event-grid/event-schema-key-vault.md)
