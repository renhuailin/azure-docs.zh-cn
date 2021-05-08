---
title: 创建存储帐户，帐户启用基础结构加密用于双重加密数据
titleSuffix: Azure Storage
description: 如果客户要求更高级别的数据安全保证，则还可以在 Azure 存储基础结构级别启用 256 位 AES 加密。 启用基础结构加密后，存储帐户中的数据将使用两个不同的加密算法和两个不同的密钥进行两次加密。
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 612ba18ba71a22ad6c346b26008e688195c1d1e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746581"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>创建存储帐户，帐户启用基础结构加密用于双重加密数据

Azure 存储使用 256 位 AES 加密（可用的最强大分组加密法之一），在服务级别自动加密存储帐户中的所有数据，并且符合 FIPS 140-2 规范。 如果客户要求更高级别的数据安全保证，则还可以在 Azure 存储基础结构级别启用 256 位 AES 加密。 启用基础结构加密后，将使用两种不同的加密算法和两个不同的密钥&mdash;分别在服务级别和基础架构级别&mdash;对存储帐户中的数据进行两次加密。 Azure 存储数据的双重加密可以在其中一种加密算法或密钥可能被泄露的情况下提供保护。 在此方案中，附加的加密层会继续保护你的数据。

服务级别加密支持将 Microsoft 管理的密钥或客户管理的密钥与 Azure Key Vault 或 Key Vault 托管硬件安全模型 (HSM)（预览版）一起使用。 基础结构级别的加密依赖于 Microsoft 管理的密钥并始终使用单独的密钥。 有关 Azure 存储加密的密钥管理的详细信息，请参阅[关于加密密钥管理](storage-service-encryption.md#about-encryption-key-management)。

若要对数据进行双重加密，必须首先创建为基础结构加密配置的存储帐户。 本文介绍如何创建启用基础结构加密的存储帐户。

## <a name="register-to-use-infrastructure-encryption"></a>注册使用基础结构加密

若要创建启用了基础结构加密的存储帐户，首先必须使用 PowerShell 或 Azure CLI 注册使用此 Azure 功能。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

不可用

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 注册，请调用 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 命令。

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

若要使用 PowerShell 检查注册状态，请调用 [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 命令。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

注册得到批准后，必须重新注册 Azure 存储资源提供程序。 若要使用 PowerShell 重新注册资源提供程序，请调用 [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 命令。

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 注册，请调用 [az feature register](/cli/azure/feature#az-feature-register) 命令。

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

若要使用 Azure CLI 查看注册状态，请调用 [az feature](/cli/azure/feature#az-feature-show) 命令。

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

注册得到批准后，必须重新注册 Azure 存储资源提供程序。 若要使用 Azure CLI 重新注册资源提供程序，请调用 [az provider register](/cli/azure/provider#az-provider-register) 命令。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[模板](#tab/template)

空值

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>创建启用了基础结构加密的帐户

必须配置存储帐户，以便在创建帐户时使用基础结构加密。 存储帐户必须是常规用途 v2 类型。

帐户创建后，无法启用或禁用基础结构加密。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 PowerShell 创建启用了基础结构加密的存储帐户，请执行以下步骤：

1. 在 Azure 门户中，导航到“存储帐户”页。
1. 选择“添加”按钮，添加新的常规用途 v2 存储帐户。
1. 在“高级”选项卡上，找到“基础结构”加密，并选择“启用”。  
1. 选择“查看 + 创建”，完成存储帐户的创建。

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="显示如何在创建帐户时启用基础结构加密的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 创建启用了基础结构加密的存储帐户，请确保已安装 [Az.Storage PowerShell 模块](https://www.powershellgallery.com/packages/Az.Storage) 2.2.0 或更高版本。 有关详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

接下来，通过调用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 命令来创建常规用途 v2 存储帐户。 包括 `-RequireInfrastructureEncryption` 选项来启用基础结构加密。

下面的示例演示如何创建常规用途 v2 存储帐户，帐户配置用于读取访问异地冗余存储 (RA-GRS) ，并启用基础结构加密用于对数据进行两次加密。 请记得将括号中的占位符值替换为你自己的值：

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建启用了基础结构加密的存储帐户，请确保已安装 Azure CLI 2.8.0 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

接下来，通过调用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令创建常规用途 v2 存储帐户，并包括 `--require-infrastructure-encryption option` 来启用基础结构加密。

下面的示例演示如何创建常规用途 v2 存储帐户，帐户配置用于读取访问异地冗余存储 (RA-GRS) ，并启用基础结构加密用于对数据进行两次加密。 请记得将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[模板](#tab/template)

下面的 JSON 示例创建了常规用途 v2 存储帐户，帐户配置用于读取访问异地冗余存储 (RA-GRS) ，并启用基础结构加密用于对数据进行两次加密。 请记得将括号中的占位符值替换为你自己的值：

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>验证是否已启用基础结构加密

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要验证是否通过 Azure 门户为存储帐户启用了基础结构加密，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在“设置”下，选择“加密”。 

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="显示如何验证是否已为帐户启用基础结构加密的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要验证是否通过 PowerShell 为存储帐户启用了基础结构加密，请调用 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 命令。 此命令返回一组存储帐户属性及其值。 在 `Encryption` 属性内检索 `RequireInfrastructureEncryption` 字段，并验证其是否设置为 `True`。

下面的示例检索 `RequireInfrastructureEncryption` 属性的值。 请务必将尖括号中的占位符值替换为你自己的值：

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要验证是否通过 Azure CLI 为存储帐户启用了基础结构加密，请调用 [az storage account show](/cli/azure/storage/account#az-storage-account-show) 命令。 此命令返回一组存储帐户属性及其值。 在 `encryption` 属性内查找 `requireInfrastructureEncryption` 字段，并验证其是否设置为 `true`。

下面的示例检索 `requireInfrastructureEncryption` 属性的值。 请务必将尖括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[模板](#tab/template)

空值

---

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md)
- [用于 Azure 存储加密的客户管理的密钥](customer-managed-keys-overview.md)
