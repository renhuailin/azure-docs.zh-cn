---
title: 为表和队列创建一个支持客户管理密钥的帐户
titleSuffix: Azure Storage
description: 了解如何创建支持为表和队列配置客户管理密钥的存储帐户。 使用 Azure CLI 或 Azure 资源管理器模板来创建一个存储帐户，该帐户依赖于 Azure 存储加密的帐户加密密钥。 然后，可以为帐户配置客户管理的密钥。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4c86811ee72d2713fced6320a17d1ccde1866d99
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769942"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>为表和队列创建一个支持客户管理密钥的帐户

Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，队列存储和表存储使用的密钥的作用域为服务，由 Microsoft 管理。 用户还可以选择使用客户管理的密钥来加密队列或表数据。 若要将客户管理的密钥与队列和表一起使用，必须首先创建一个存储帐户，该帐户使用的加密密钥的作用域是帐户，而不是服务。 在创建了使用帐户加密密钥作为队列和表数据的帐户后，可以为该存储帐户配置客户管理的密钥。

本文介绍了如何创建一个存储帐户，该帐户依赖于该帐户的作用域。 首次创建帐户时，Microsoft 使用帐户密钥来对帐户中的数据进行加密，而 Microsoft 管理密钥。 接下来，用户可以为帐户配置客户管理的密钥，以利用这些权益，包括提供用户自己的密钥、更新密钥版本、轮换密钥和吊销访问控制。

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>创建使用帐户加密密钥的帐户

用户必须配置新的存储帐户，以便在创建存储帐户时为队列和表使用帐户加密密钥。 帐户创建后，便无法更改加密密钥的作用域。

存储帐户必须是常规用途 v2 类型。 可以通过使用 Azure CLI 或 Azure 资源管理器模板，来创建存储帐户并将其配置为依赖帐户加密密钥。

> [!NOTE]
> 只有在创建存储帐户时，才可以选择配置队列和表存储，以通过帐户加密密钥来加密数据。 Blob 存储和 Azure 文件始终使用帐户加密密钥来加密数据。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 创建依赖于帐户加密密钥的存储帐户，请确保已安装 Azure PowerShell 模块版本 3.4.0 或更高版本。 有关详细信息，请参阅“[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)”。

接下来，通过调用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 命令并使用适当的参数来创建常规用途 v2 存储帐户：

- 包括 `-EncryptionKeyTypeForQueue` 选项，并将其值设置为 `Account`，以使用帐户加密密钥加密队列存储中的数据。
- 包括 `-EncryptionKeyTypeForTable` 选项，并将其值设置为 `Account`，以使用帐户加密密钥加密表存储中的数据。

下面的示例演示如何创建一个常规用途 v2 存储帐户，该帐户配置为读取访问地域冗余存储 (GRS)，并使用帐户加密密钥来加密队列和表存储的数据。 请记得将括号中的占位符值替换为你自己的值：

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建依赖于账户加密密钥的存储帐户，请确保已安装 Azure CLI 2.0.80 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

接下来，通过调用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令并使用适当的参数来创建常规用途 v2 存储帐户：

- 包括 `--encryption-key-type-for-queue` 选项，并将其值设置为 `Account`，以使用帐户加密密钥加密队列存储中的数据。
- 包括 `--encryption-key-type-for-table` 选项，并将其值设置为 `Account`，以使用帐户加密密钥加密表存储中的数据。

下面的示例演示如何创建一个常规用途 v2 存储帐户，该帐户配置为读取访问地域冗余存储 (GRS)，并使用帐户加密密钥来加密队列和表存储的数据。 请记得将括号中的占位符值替换为你自己的值：

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[模板](#tab/template)

下面的 JSON 示例创建了一个常规用途 v2 存储帐户，该帐户配置为读取访问地域冗余存储 (GRS)，并使用帐户加密密钥来加密队列和表存储的数据。 请务必将尖括号中的占位符值替换为自己的值：

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
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

依赖于帐户加密密钥的帐户创建后，便可以配置客户管理的密钥，这些密钥存储在 Azure 密钥保管库或密钥保管库托管的硬件安全模型 (HSM) （预览版）中。 若要了解如何配置密钥保管库中客户管理的密钥，请参阅“[使用 Azure Key Vault 中存储的客户管理的密钥配置加密](customer-managed-keys-configure-key-vault.md)”。 若要了解如何存储托管 HSM 中客户管理的密钥，请参阅“[使用 Azure 密钥保管库托管 HSM（预览版）中存储的客户管理的密钥配置加密](customer-managed-keys-configure-key-vault-hsm.md)”。

## <a name="verify-the-account-encryption-key"></a>验证帐户加密密钥

若要验证存储帐户中的服务是否正在使用帐户加密密钥，请调用 Azure CLI [az storage account](/cli/azure/storage/account#az_storage_account_show) 命令。 此命令可返回一组存储帐户属性及其值。 在加密属性中查找每个服务的 `keyType` 字段，并验证其是否设置为 `Account`。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要验证存储帐户中的服务是否正在使用帐户加密密钥，请调用 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 命令。 此命令可返回一组存储帐户属性及其值。 在 `Encryption` 属性中查找每个服务的 `KeyType` 字段，并验证其是否设置为 `Account`。

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要验证存储帐户中的服务是否正在使用帐户加密密钥，请调用 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 命令。 此命令可返回一组存储帐户属性及其值。 在加密属性中查找每个服务的 `keyType` 字段，并验证其是否设置为 `Account`。

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[模板](#tab/template)

空值

---

## <a name="pricing-and-billing"></a>定价和计费

如果创建的存储帐户使用范围为该帐户的加密密钥，则在对表存储容量和事务计费方面，该帐户与使用默认的服务范围密钥的帐户存在不同。 有关详细信息，请参阅 [Azure 表存储定价](https://azure.microsoft.com/pricing/details/storage/tables/)。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md)
- [用于 Azure 存储加密的客户管理的密钥](customer-managed-keys-overview.md)
- [什么是 Azure Key Vault？](../../key-vault/general/overview.md)