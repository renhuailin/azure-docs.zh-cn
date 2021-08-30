---
title: 数据加密 - Azure CLI - Azure Database for MySQL
description: 了解如何使用 Azure CLI 为 Azure Database for MySQL 设置和管理数据加密。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2661e73b5ef1a820d28d71b93fd96db034e8daf6
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675326"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>使用 Azure CLI 为 Azure Database for MySQL 进行数据加密

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

了解如何使用 Azure CLI 为 Azure Database for MySQL 设置和管理数据加密。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI 的先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* 创建密钥保管库和用于客户托管密钥的密钥。 同时启用密钥保管库上的清除保护和软删除。

  ```azurecli-interactive
  az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
  ```

* 在创建的 Azure Key Vault 中，创建将用于 Azure Database for MySQL 的数据加密的密钥。

  ```azurecli-interactive
  az keyvault key create --name <key_name> -p software --vault-name <vault_name>
  ```

* 要使用现有的密钥保管库，现有密钥保管库必须具有以下属性以用作客户管理的密钥：

  * [软删除](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [清除保护](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * 保留天数设置为 90 天
  ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* 该密钥必须具有以下属性才能用作客户管理的密钥：
  * 无过期日期
  * 未禁用
  * 执行 **get**、**wrap**、**unwrap** 操作
  * recoverylevel 属性设置为 **Recoverable**（这要求启用软删除，并将保留期设置为 90 天）
  * 已启用清除保护

可以使用以下命令来验证密钥的上述属性：

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```
* Azure Database for MySQL - 单一服务器应位于常规用途或内存优化定价层和常规用途存储 v2 上。 在继续操作之前，请参阅[使用客户管理的密钥进行数据加密](concepts-data-encryption-mysql.md#limitations)的限制。

## <a name="set-the-right-permissions-for-key-operations"></a>为密钥操作设置正确的权限

1. 可通过两种方式获取 Azure Database for MySQL 的托管标识。

   ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>使用托管标识创建新的 Azure Database for MySQL 服务器。

   ```azurecli-interactive
   az mysql server create --name -g <resource_group> --location <locations> --storage-size size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> -geo-redundant-backup <Enabled/Disabled>  --assign-identity
   ```

   ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>更新现有 Azure Database for MySQL 服务器以获取托管标识。

   ```azurecli-interactive
   az mysql server update --name  <server name>  -g <resource_group> --assign-identity
   ```

2. 为“主体”（MySQL 服务器的名称）选择“密钥权限”（“Get”、“Wrap”、“Unwrap”）。    

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 设置数据加密

1. 使用 Azure Key Vault 中创建的密钥对 Azure Database for MySQL 启用数据加密。

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    密钥 URL：`https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>对还原或副本服务器使用数据加密

在使用客户存储在 Key Vault 中的托管密钥对 Azure Database for MySQL 进行加密后，还将对服务器的任何新创建的副本进行加密。 可通过本地或异地还原操作，或通过副本（本地/跨区域）操作来创建这个新副本。 因此，对于加密的 MySQL 服务器，可以使用以下步骤来创建加密的还原服务器。

### <a name="creating-a-restoredreplica-server"></a>创建还原/副本服务器

* [创建还原服务器](howto-restore-server-cli.md) 
* [创建只读副本服务器](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>服务器还原后，请重新验证还原服务器的数据加密

*   为副本服务器分配标识
```azurecli-interactive
az mysql server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   获取必须用于还原/副本服务器的现有密钥

```azurecli-interactive
az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   为还原/副本服务器的新标识设置策略
  
```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* 用加密密钥重新验证还原/副本服务器

```azurecli-interactive
az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>用于 Azure Database for MySQL 的密钥的附加功能

### <a name="get-the-key-used"></a>获取使用的密钥

```azurecli-interactive
az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
```

密钥 URL：`https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>列出使用的密钥

```azurecli-interactive
az mysql server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>删除正在使用的密钥

```azurecli-interactive
az mysql server key delete -g <resource_group> --kid <key url>
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>使用 Azure 资源管理器模板启用数据加密

除了 Azure 门户之外，还可以在 Azure Database for MySQL 服务器上，使用 Azure 资源管理器模板为新服务器和现有服务器启用数据加密。

### <a name="for-a-new-server"></a>对于新服务器

使用预先创建的 Azure 资源管理器模板之一来预配启用了数据加密的服务器：[包含数据加密的示例](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

此 Azure 资源管理器模板创建 Azure Database for MySQL 服务器，并使用作为参数传递的“KeyVault”和“Key”在服务器上启用数据加密。 

### <a name="for-an-existing-server"></a>对于现有服务器

此外，还可以使用 Azure 资源管理器模板在现有 Azure Database for MySQL 服务器上启用数据加密。

* 在属性对象中的 `Uri` 属性下，传递先前复制的 Azure Key Vault 密钥的资源 ID。

* 使用“2020-01-01-preview”作为 API 版本。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>后续步骤

* [验证 Azure Database for MySQL 的数据加密](howto-data-encryption-validation.md)
* [对 Azure Database for MySQL 中的数据加密进行故障排除](howto-data-encryption-troubleshoot.md)
* [使用客户管理的密钥进行加密的概念](concepts-data-encryption-mysql.md)。

