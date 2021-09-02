---
title: 使用存储在 Azure Key Vault 中的客户管理的密钥配置加密
titleSuffix: Azure Storage
description: 了解如何使用 Azure 门户、PowerShell 或 Azure CLI，通过存储在 Azure Key Vault 中的客户管理的密钥配置 Azure 存储加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 7377f0e296f571640aba35639972e66a60c6dd0f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444642"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>使用存储在 Azure Key Vault 中的客户管理的密钥配置加密

Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，数据使用 Microsoft 管理的密钥进行加密。 为了更进一步控制加密密钥，你可以管理自己的密钥。 客户管理的密钥必须存储在 Azure Key Vault 或 Key Vault 托管硬件安全模型 (HSM) 中。

本文介绍了如何使用 Azure 门户、PowerShell 或 Azure CLI，通过存储在密钥保管库中的客户管理的密钥配置加密。 若要了解如何配置使用托管 HSM 中存储的客户管理的密钥进行加密，请参阅[配置使用 Azure Key Vault 托管 HSM 中存储的客户管理的密钥进行加密](customer-managed-keys-configure-key-vault-hsm.md)。

> [!NOTE]
> Azure 密钥保管库和 Azure 密钥保管库托管 HSM 支持使用相同的 API 和管理界面进行配置。

## <a name="configure-a-key-vault"></a>配置密钥保管库

你可以使用新的或现有的密钥保管库来存储客户管理的密钥。 存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。

使用带有 Azure 存储加密的客户管理的密钥需要为密钥保管库启用软删除和清除保护。 创建新密钥保管库时，默认会启用软删除，并且无法禁用。 你可以在创建密钥保管库时或创建后启用清除保护。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要了解如何使用 Azure 门户创建密钥保管库，请参阅[快速入门：使用 Azure 门户创建密钥保管库](../../key-vault/general/quick-create-portal.md)。 创建密钥保管库时，选择“启用清除保护”，如下图所示。

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="显示如何在创建密钥保管库时启用清除保护的屏幕截图":::

若要在现有密钥保管库上启用清除保护，请执行以下步骤：

1. 在 Azure 门户中导航到密钥保管库。
1. 在“设置”下面，选择“属性”。
1. 在“清除保护”部分，选择“启用清除保护” 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 创建新密钥保管库，请安装 [Az. KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell 模块的版本 2.0.0 或更高版本。 然后调用 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) 来创建新密钥保管库。 在 Az.KeyVault 模块的版本 2.0.0 和更高版本中，当创建新密钥保管库时，默认会启用软删除。

以下示例将创建一个启用了软删除和清除保护的新密钥保管库。 请记得将括号中的占位符值替换为你自己的值。

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

若要了解如何使用 PowerShell 在现有密钥保管库上启用清除保护，请参阅[如何在 PowerShell 中使用软删除](../../key-vault/general/key-vault-recovery.md)。

接下来，向存储帐户分配系统分配的托管标识。 将使用此托管标识授予存储帐户访问 Key Vault 的权限。 有关系统分配的托管标识的详细信息，请参阅[什么是 Azure 资源托管标识？](../../active-directory/managed-identities-azure-resources/overview.md)。

若要使用 PowerShell 分配托管标识，请调用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)：

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

最后，配置密钥保管库的访问策略，使存储帐户有权访问密钥保管库。 此步骤使用前面分配给存储帐户的托管标识。

若要设置密钥保管库的访问策略，请调用 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)：

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建新的 Key Vault，请调用 [az keyvault create](/cli/azure/keyvault#az_keyvault_create)。 请记得将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

若要了解如何使用 Azure CLI 在现有密钥保管库上启用清除保护，请参阅[如何在 Azure CLI 中使用软删除](../../key-vault/general/key-vault-recovery.md)。

接下来，向存储帐户分配系统分配的托管标识。 将使用此托管标识授予存储帐户访问 Key Vault 的权限。 有关系统分配的托管标识的详细信息，请参阅[什么是 Azure 资源托管标识？](../../active-directory/managed-identities-azure-resources/overview.md)。

若要使用 Azure CLI 分配托管标识，请调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update)：

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

最后，配置密钥保管库的访问策略，使存储帐户有权访问密钥保管库。 此步骤使用前面分配给存储帐户的托管标识。

若要设置密钥保管库的访问策略，请调用 [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy)：

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>添加密钥

接下来，在密钥保管库中添加密钥。

Azure 存储加密支持大小为 2048、3072 和 4096 的 RSA 和 RSA-HSM 密钥。 有关密钥的详细信息，请参阅[关于密钥](../../key-vault/keys/about-keys.md)。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要了解如何使用 Azure 门户添加密钥，请参阅[快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索密钥](../../key-vault/keys/quick-create-portal.md)中的说明创建密钥保管库。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 添加密钥，请调用 [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 添加密钥，请调用 [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create)。 请记得将括号中的占位符值替换为你自己的值。

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>配置使用客户管理的密钥进行加密

接下来，请将 Azure 存储帐户配置为通过 Azure Key Vault 使用客户管理的密钥，然后指定要与存储帐户关联的密钥。

使用客户管理的密钥配置加密时，只要关联的密钥保管库中有新版本可用，就可以选择自动更新用于 Azure 存储加密的密钥版本。 也可显式指定在手动更新密钥版本之前用于加密的密钥版本。

> [!NOTE]
> 若要轮换密钥，请在 Azure Key Vault 中创建密钥的新版本。 Azure 存储不会处理 Azure Key Vault 中的密钥轮换，因此你需要手动轮换密钥，或创建一个函数以便按计划轮换密钥。

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>配置加密以自动更新密钥版本

Azure 存储可以自动更新客户管理的密钥（用于加密），以使用最新密钥版本。 当在 Azure Key Vault 中轮换客户管理的密钥时，Azure 存储会自动开始将最新版本的密钥用于加密。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中配置客户管理的密钥并自动更新密钥版本，请执行以下步骤：

1. 导航到存储帐户。
1. 在存储帐户的“设置”边栏选项卡上，单击“加密”。 默认情况下，密钥管理设置为“Microsoft 管理的密钥”，如下图所示。

    ![显示加密选项的门户屏幕截图](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. 选择“客户管理的密钥”选项。
1. 选择“从 Key Vault 中选择”选项。
1. 选择“选择密钥保管库和密钥”。
1. 选择包含要使用的密钥的密钥保管库。
1. 从密钥保管库中选择密钥。

   ![屏幕截图，显示如何选择密钥保管库和密钥](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. 保存所做更改。

指定密钥后，Azure 门户会指示启用密钥版本的自动更新，并显示当前用于加密的密钥版本。

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="屏幕截图，其中显示已启用自动更新密钥版本":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 配置客户管理的密钥并自动更新密钥版本，请安装 [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) 模块 2.0.0 版或更高版本。

若要自动更新客户管理密钥的密钥版本，请在使用客户管理的密钥为存储帐户配置加密时省略密钥版本。 调用 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 以更新存储帐户的加密设置（如以下示例所示），并包含 -KeyvaultEncryption 选项，以便为存储帐户启用客户管理的密钥。

请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 配置客户管理的密钥并自动更新密钥版本，请安装 [Azure CLI 2.4.0 版本](/cli/azure/release-notes-azure-cli#april-21-2020)或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

若要自动更新客户管理的密钥的密钥版本，请在使用存储帐户的客户管理的密钥配置加密时省略密钥版本。 请调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update)，以便更新存储帐户的加密设置，如以下示例所示。 包括 `--encryption-key-source` 参数并将其设置为 `Microsoft.Keyvault` 即可为帐户启用客户管理的密钥。

请记得将括号中的占位符值替换为你自己的值。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>为手动更新密钥版本配置加密

如果希望手动更新密钥版本，请在使用客户管理的密钥配置加密时显式指定该版本。 在这种情况下，当在密钥库中创建新版本时，Azure 存储将不会自动更新密钥版本。若要使用新的密钥版本，必须手动更新用于 Azure 存储加密的版本。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中配置客户管理的密钥并手动更新密钥版本，请指定密钥 URI，包括版本。 若要将某个密钥指定为 URI，请执行下列步骤：

1. 若要在 Azure 门户中查找密钥 URI，请导航到 Key Vault，然后选择“密钥”设置。 选择所需的密钥，然后单击该密钥以查看其版本。 选择一个密钥版本，查看该版本的设置。
1. 复制“密钥标识符”字段的值（提供 URI）。

    ![显示 Key Vault 密钥 URI 的屏幕截图](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. 在存储帐户的“加密密钥”设置中，选择“输入密钥 URI”选项。 
1. 将复制的 URI 粘贴到“密钥 URI”字段中。 从 URI 中省略密钥版本，以启用自动更新密钥版本。

   ![显示如何输入密钥 URI 的屏幕截图](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. 指定包含密钥保管库的订阅。
1. 保存所做更改。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要配置客户管理的密钥并手动更新密钥版本，请在为存储帐户配置加密时显式提供密钥版本。 调用 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 以更新存储帐户的加密设置（如以下示例所示），并包含 -KeyvaultEncryption 选项，以便为存储帐户启用客户管理的密钥。

请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

手动更新密钥版本时，需要更新存储帐户的加密设置以使用新版本。 首先调用 [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) 以获取最新密钥版本。 然后调用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 来更新存储帐户的加密设置，以使用该密钥的新版本，如前面示例所示。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要配置客户管理的密钥并手动更新密钥版本，请在为存储帐户配置加密时显式提供密钥版本。 请调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update)，以便更新存储帐户的加密设置，如以下示例所示。 包括 `--encryption-key-source` 参数并将其设置为 `Microsoft.Keyvault` 即可为帐户启用客户管理的密钥。

请记得将括号中的占位符值替换为你自己的值。

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

手动更新密钥版本时，需要更新存储帐户的加密设置以使用新版本。 首先，通过调用 [az keyvault show](/cli/azure/keyvault#az_keyvault_show) 查询 Key Vault URI，并通过调用 [az keyvault key list-versions](/cli/azure/keyvault/key#az_keyvault_key_list-versions) 查询密钥版本。 然后调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 来更新存储帐户的加密设置，以使用新的密钥版本，如上一示例所示。

---

## <a name="change-the-key"></a>更改密钥

你可以在任何时候更改用于 Azure 存储加密的密钥。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 Azure 门户更改密钥，请执行以下步骤：

1. 导航到你的存储帐户，并显示“加密”设置。
1. 选择密钥保管库并选择一个新密钥。
1. 保存更改。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 更改密钥，请调用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)（如[使用客户管理的密钥配置加密](#configure-encryption-with-customer-managed-keys)中所示），并提供新的密钥名称和版本。 如果新密钥位于不同的密钥保管库中，则还必须更新密钥保管库 URI。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 更改密钥，请调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update)（如[使用客户托管密钥配置加密](#configure-encryption-with-customer-managed-keys)中所示），并提供新的密钥名称和版本。 如果新密钥位于不同的密钥保管库中，则还必须更新密钥保管库 URI。

---

## <a name="revoke-customer-managed-keys"></a>撤销客户托管密钥

撤消客户管理的密钥会删除存储帐户和密钥保管库之间的关联。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要使用 Azure 门户撤消客户管理的密钥，请按照[禁用客户管理的密钥](#disable-customer-managed-keys)中所述禁用该密钥。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

可以通过删除密钥保管库访问策略来撤销客户管理的密钥。 若要使用 PowerShell 撤销客户托管密钥，请调用 [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) 命令，如下例所示。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以通过删除密钥保管库访问策略来撤销客户管理的密钥。 若要使用 Azure CLI 撤销客户托管密钥，请调用 [az keyvault delete-policy](/cli/azure/keyvault#az_keyvault_delete_policy) 命令，如下例所示。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>禁用客户托管密钥

禁用客户托管密钥时，将再次使用 Microsoft 托管密钥对存储帐户进行加密。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要在 Azure 门户中禁用客户管理的密钥，请执行以下步骤：

1. 导航到你的存储帐户，并显示“加密”设置。
1. 取消选中“使用自己的密钥”设置旁边的复选框。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 禁用客户管理的密钥，请使用 `-StorageEncryption` 选项调用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)，如下例所示。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 禁用客户托管密钥，请调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 并将 `--encryption-key-source parameter` 设置为 `Microsoft.Storage`，如下例所示。 请记得将括号中的占位符值替换为自己的值，并使用前面示例中定义的变量。

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md)
- [用于 Azure 存储加密的客户管理的密钥](customer-managed-keys-overview.md)
- [使用 Azure Key Vault 托管 HSM 中存储的客户管理的密钥配置加密](customer-managed-keys-configure-key-vault-hsm.md)
