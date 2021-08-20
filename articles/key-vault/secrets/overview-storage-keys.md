---
title: 使用 Azure Key Vault 和 Azure CLI 管理存储帐户密钥
description: 存储帐户密钥在 Azure Key Vault 与 Azure 存储帐户基于密钥的访问方式之间提供无缝集成。
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5a73b98f31fb7c2f8979f977138ca7f836fe5a34
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114221797"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>使用 Key Vault 和 Azure CLI 管理存储帐户密钥
> [!IMPORTANT]
> 我们建议使用 Azure 存储与 Azure Active Directory (Azure AD) 的集成，这是 Microsoft 推出的基于云的标识和访问管理服务。 Azure AD 集成适用于 [Azure Blob 和队列](../../storage/common/storage-auth-aad.md)，提供对 Azure 存储的基于 OAuth2 令牌的访问（类似于 Azure Key Vault）。 Azure AD 允许使用应用程序标识或用户标识（而不是存储帐户凭据）对客户端应用程序进行身份验证。 在 Azure 上运行时，可以使用 [Azure AD 托管标识](../../active-directory/managed-identities-azure-resources/index.yml)。 托管标识消除了客户端身份验证的需要，并可以在应用程序中存储凭据，或者将凭据与应用程序一同存储。 仅在无法实施 Azure AD 身份验证的情况下使用以下解决方案。

Azure 存储帐户使用由帐户名和密钥构成的凭据。 密钥是自动生成的，充当密码而不是加密密钥。 Key Vault 通过在存储帐户中定期重新生成存储帐户密钥来管理存储帐户密钥，并提供共享访问签名令牌，以便对存储帐户中的资源进行委托访问。

可以使用 Key Vault 托管的存储帐户密钥功能列出（同步） Azure 存储帐户中的密钥，并定期重新生成（轮换）密钥。 可以管理存储帐户和经典存储帐户的密钥。

使用托管的存储帐户密钥功能时，请注意以下要点：

- 响应调用方时永远不会返回密钥值。
- 只有 Key Vault 能够管理存储帐户密钥。 不要自行管理密钥，并避免干扰 Key Vault 进程。
- 只有单个 Key Vault 对象能够管理存储帐户密钥。 不要允许从多个对象进行密钥管理。
- 只使用 Key Vault 重新生成密钥。 不要手动重新生成存储帐户密钥。

## <a name="service-principal-application-id"></a>服务主体应用程序 ID

Azure AD 租户为每个已注册的应用程序提供[服务主体](../../active-directory/develop/developer-glossary.md#service-principal-object)。 此服务主体充当应用程序 ID，该 ID 在授权设置过程中用于通过 Azure RBAC 访问其他 Azure 资源。

Key Vault 是已在所有 Azure AD 租户中预先注册的 Microsoft 应用程序。 Key Vault 注册到每个 Azure 云中的同一个应用程序 ID 下。

| 租户 | 云 | 应用程序 ID |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 公共 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 其他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>先决条件

若要完成本指南，必须先执行以下操作：

- [安装 Azure CLI](/cli/azure/install-azure-cli)。
- [创建密钥保管库](quick-create-cli.md)
- [创建 Azure 存储帐户](../../storage/common/storage-account-create.md?tabs=azure-cli)。 存储帐户名必须仅使用小写字母和数字。 名称的长度必须为 3 到 24 个字符。
      
## <a name="manage-storage-account-keys"></a>管理存储帐户密钥

### <a name="connect-to-your-azure-account"></a>连接到 Azure 帐户

使用 [az login](/powershell/module/az.accounts/connect-azaccount) 命令对 Azure CLI 会话进行身份验证。

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>向 Key Vault 授予对你的存储帐户的访问权限

使用 Azure CLI [az role assignment create](/cli/azure/role/assignment) 命令授予 Key Vault 访问你的存储帐户的权限。 为该命令提供以下参数值：

- `--role`：传递“存储帐户密钥操作员服务角色”Azure 角色。 此角色将访问范围限制为你的存储帐户。 对于经典存储帐户，请改为传递“经典存储帐户密钥操作员服务角色”。
- `--assignee`：传递值“https://vault.azure.net”，它是 Azure 公有云中 Key Vault 的 url。 （对于 Azure 政府云，改用“--assignee-object-id”，具体请参阅[服务主体应用程序 ID](#service-principal-application-id)。）
- `--scope`：传递格式为 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` 的存储帐户资源 ID。 若要查找订阅 ID，请使用 Azure CLI [az account list](/cli/azure/account?#az_account_list) 命令；若要查找存储帐户名称和存储帐户资源组，请使用 Azure CLI [az storage account list](/cli/azure/storage/account?#az_storage_account_list) 命令。

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>向托管存储帐户授予用户帐户权限

使用 Azure CLI [az keyvault-set-policy](/cli/azure/keyvault?#az_keyvault_set_policy) cmdlet 更新 Key Vault 访问策略，并向用户帐户授予存储帐户权限。

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

请注意，Azure 门户中存储帐户的“访问策略”页不会显示存储帐户的权限。
### <a name="create-a-key-vault-managed-storage-account"></a>创建 Key Vault 托管存储帐户

 使用 Azure CLI [az keyvault storage](/cli/azure/keyvault/storage?#az_keyvault_storage_add) 命令创建 Key Vault 托管的存储帐户。 将重新生成周期设置为 90 天。 当需要轮换时，KeyVault 将重新生成非活动状态的密钥，然后将新创建的密钥设置为活动密钥。 在任何时间都只有一个密钥，即活动密钥，用于颁发 SAS 令牌。 为该命令提供以下参数值：

- `--vault-name`：传递 Key Vault 的名称。 若要查找 Key Vault 的名称，请使用 Azure CLI [az keyvault list](/cli/azure/keyvault?#az_keyvault_list) 命令。
- `-n`：传递你的存储帐户的名称。 若要查找存储帐户的名称，请使用 Azure CLI [az storage account list](/cli/azure/storage/account?#az_storage_account_list) 命令。
- `--resource-id`：传递格式为 `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` 的存储帐户资源 ID。 若要查找订阅 ID，请使用 Azure CLI [az account list](/cli/azure/account?#az_account_list) 命令；若要查找存储帐户名称和存储帐户资源组，请使用 Azure CLI [az storage account list](/cli/azure/storage/account?#az_storage_account_list) 命令。
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>共享访问签名令牌

也可以要求 Key Vault 生成共享访问签名令牌。 共享访问签名对存储帐户中的资源提供委托访问。 可以授予客户端访问存储帐户中的资源的权限，而无需共享帐户密钥。 使用共享访问签名可以安全共享存储资源，而不会透露帐户密钥。

本部分所述的命令将完成以下操作：

- 设置帐户共享访问签名定义 `<YourSASDefinitionName>`。 该定义是在 Key Vault `<YourKeyVaultName>` 中的 Key Vault 托管存储帐户 `<YourStorageAccountName>` 上设置的。
- 为 Blob、文件、表和队列服务创建帐户共享访问签名令牌。 为“服务”、“容器”和“对象”资源类型创建令牌。 创建的令牌拥有所有权限、通过 HTTPS 访问并指定了开始和结束日期。
- 在保管库中设置 Key Vault 托管的存储共享访问签名定义。 该定义包含创建的共享访问签名令牌的模板 URI。 该定义使用共享访问签名类型 `account`，有效期为 N 天。
- 验证共享访问签名是否已作为机密保存在 Key Vault 中。

### <a name="create-a-shared-access-signature-token"></a>创建共享访问签名令牌

使用 Azure CLI [az storage account generate-sas](/cli/azure/storage/account?#az_storage_account_generate_sas) 命令创建共享访问签名定义。 此操作需要 `storage` 和 `setsas` 权限。


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
操作成功运行后，复制输出。

```console
"se=2020-01-01&sp=***"
```

此输出将在下一步骤中传递给 `--template-uri` 参数。

### <a name="generate-a-shared-access-signature-definition"></a>生成共享访问签名定义

使用 Azure CLI [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_create) 命令并将上一步骤的输出传递给 `--template-uri` 参数，以创建共享访问签名定义。  可将所选的名称提供给 `-n` 参数。

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>验证共享访问签名定义

可以使用 Azure CLI [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) 命令验证共享访问签名定义是否已存储在密钥保管库中。

现可使用 [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) 命令和 `id` 属性来查看该机密的内容。

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>后续步骤

- 详细了解[密钥、机密和证书](/rest/api/keyvault/)。
- 查看 [Azure Key Vault 团队博客](/archive/blogs/kv/)中的文章。
- 参阅 [az keyvault storage](/cli/azure/keyvault/storage) 参考文档。
