---
title: 使用 Azure Key Vault 托管 HSM 中存储的客户管理的密钥配置加密
titleSuffix: Azure Storage
description: 了解如何通过使用 Azure CLI 来使用 Azure Key Vault 管理的 HSM 中存储的客户管理的密钥配置 Azure 存储加密。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 09f62865c80c05fd0860fa39b18d99c583cf3e56
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461616"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm"></a>使用 Azure Key Vault 托管 HSM 中存储的客户管理的密钥配置加密

Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，数据使用 Microsoft 管理的密钥进行加密。 为了更进一步控制加密密钥，你可以管理自己的密钥。 客户管理的密钥必须存储在 Azure Key Vault 或 Key Vault 托管硬件安全模型 (HSM) 中。 Azure Key Vault 管理的 HSM 是经过 FIPS 140-2 第 3 级验证的 HSM。

本文介绍如何通过使用 Azure CLI 来使用存储在托管的 HSM 中的客户管理的密钥配置加密。 若要了解如何使用密钥保管库中存储的客户管理的密钥来配置加密，请参阅[使用 Azure Key Vault 中存储的客户管理的密钥配置加密](customer-managed-keys-configure-key-vault.md)。

> [!NOTE]
> Azure 密钥保管库和 Azure 密钥保管库托管 HSM 支持使用相同的 API 和管理界面进行配置。

## <a name="assign-an-identity-to-the-storage-account"></a>将标识分配到存储帐户

首先，将系统分配的托管标识分配到存储帐户。 将使用此托管标识授予存储帐户访问托管的 HSM 的权限。 有关系统分配的托管标识的详细信息，请参阅[什么是 Azure 资源托管标识？](../../active-directory/managed-identities-azure-resources/overview.md)。

若要使用 Azure CLI 分配托管标识，请调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update)。 请记得将括号中的占位符值替换为你自己的值：

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>将角色分配到存储帐户以便访问托管的 HSM

接下来，将“托管的 HSM 加密服务加密用户”角色分配到存储帐户的托管标识，以便该存储帐户具有托管的 HSM 的访问权限。 Microsoft 建议将角色分配的范围限定为单个密钥级别，以便向托管标识授予尽可能少的特权。

若要为存储帐户创建角色分配，请调用 [az key vault role assignment create](/cli/azure/role/assignment#az_role_assignment_create)。 请记得将括号中的占位符值替换为你自己的值。
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption User" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>使用托管的 HSM 中的密钥配置加密

最后，使用客户管理的密钥来将 Azure 存储加密配置为使用托管的 HSM 中存储的密钥。 支持的密钥类型包括大小为 2048、3072 和 4096 的 RSA-HSM 密钥。 若要了解如何在托管 HSM 中创建密钥，请参阅[创建 HSM 密钥](../../key-vault/managed-hsm/key-management.md#create-an-hsm-key)。

安装 Azure CLI 2.12.0 或更高版本，以便将加密配置为使用托管的 HSM 中的客户管理的密钥。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

若要自动更新客户管理的密钥的密钥版本，请在使用存储帐户的客户管理的密钥配置加密时省略密钥版本。 请调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update)，以便更新存储帐户的加密设置，如以下示例所示。 包括 `--encryption-key-source parameter` 并将它设置为 `Microsoft.Keyvault`，以便为该帐户启用客户管理的密钥。 请记得将括号中的占位符值替换为你自己的值。

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

若要手动更新某个客户管理的密钥的版本，请在为存储帐户配置加密时包括该密钥版本：

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

手动更新密钥版本时，需要更新存储帐户的加密设置以使用新版本。 首先，通过调用 [az keyvault show](/cli/azure/keyvault#az_keyvault_show) 查询 Key Vault URI，并通过调用 [az keyvault key list-versions](/cli/azure/keyvault/key#az_keyvault_key_list_versions) 查询密钥版本。 然后调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 来更新存储帐户的加密设置，以使用新的密钥版本，如上一示例所示。

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](storage-service-encryption.md)
- [用于 Azure 存储加密的客户管理的密钥](customer-managed-keys-overview.md)
