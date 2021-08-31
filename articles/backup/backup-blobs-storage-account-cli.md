---
title: 使用 Azure CLI 备份 Azure Blob
description: 了解如何使用 Azure CLI 备份 Azure Blob。
ms.topic: conceptual
ms.date: 08/06/2021
ms.openlocfilehash: e8ffd1d369df816bd1020b0b2ec2c1696e6c433e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181511"
---
# <a name="back-up-azure-blobs-in-a-storage-account-using-azure-cli"></a>使用 Azure CLI 在存储帐户中备份 Azure Blob

本文介绍如何使用 Azure CLI 备份 [Azure Blob](./blob-backup-overview.md)。

> [!IMPORTANT]
> 对于通过 CLI 进行的 Azure Blob 备份和还原的支持处于预览版阶段，在 Az 2.15.0 及更高版本中作为扩展提供。 在运行 az dataprotection 命令时会自动安装该扩展。 [详细了解](/cli/azure/azure-cli-extensions-overview)扩展。

本文将指导如何进行以下操作：

- 开始之前

- 创建备份保管库

- 创建备份策略

- 配置 Azure Blob 备份

- 运行按需备份作业

有关 Azure Blob 区域可用性、支持的方案和限制，请参阅[支持矩阵](blob-backup-support-matrix.md)。

## <a name="before-you-start"></a>开始之前

在开始之前，请参阅[先决条件](./blob-backup-configure-manage.md#before-you-start)和[支持矩阵](./blob-backup-support-matrix.md)。

## <a name="create-a-backup-vault"></a>创建备份保管库

备份保管库是 Azure 中的一个存储实体，用于存储 Azure 备份支持的各种新型工作负载（例如 Azure Database for PostgreSQL 服务器、存储帐户中的 blob 和 Azure 磁盘）的备份数据。 备份保管库便于组织备份数据，并最大限度降低管理开销。 备份保管库基于 Azure 的 Azure 资源管理器型号，提供增强的功能来帮助保护备份数据。

在创建备份保管库之前，请选择保管库数据的存储冗余。 然后继续使用该存储冗余和位置创建备份保管库。 在本文中，我们将在资源组 testBkpVaultRG 下，在 westus 区域创建备份保管库 TestBkpVault。 使用 [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) 命令创建备份保管库。 详细了解[创建备份保管库](./backup-vault-overview.md#create-a-backup-vault)。

```azurecli-interactive
az dataprotection backup-vault create -g testBkpVaultRG --vault-name TestBkpVault -l westus --type SystemAssigned --storage-settings datastore-type="VaultStore" type="LocallyRedundant"

{
  "eTag": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault",
  "identity": {
    "principalId": "2ca1d5f7-38b3-4b61-aa45-8147d7e0edbc",
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "SystemAssigned"
  },
  "location": "westus",
  "name": "TestBkpVault",
  "properties": {
    "provisioningState": "Succeeded",
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "tags": null,
  "type": "Microsoft.DataProtection/backupVaults"
}
```

> [!IMPORTANT]
> 虽然会看到保管库的备份存储冗余，但冗余并不实际应用于 blob 的操作备份。 这是因为该备份在本质上是本地的，不会有任何数据存储在备份保管库中。 此处的备份保管库是管理实体，可帮助你管理存储帐户中块 blob 的保护。

创建保管库后，让我们创建一个备份策略来保护存储帐户中的 Azure Blob。

## <a name="create-a-backup-policy"></a>创建备份策略

> [!IMPORTANT]
> 在为 Azure Blob 创建策略和配置备份之前，请阅读[本节](blob-backup-configure-manage.md#before-you-start)。

若要了解 Azure Blob 备份的备份策略的内部组件，请使用 [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) 命令来检索策略模板。 此命令返回给定数据源类型的默认策略模板。 使用此策略模板创建新策略。

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob

{
  "datasourceTypes": [
    "Microsoft.Storage/storageAccounts/blobServices"
  ],
  "name": "BlobPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

策略模板仅包含生命周期（决定删除/复制/移动备份的时间）。 由于 Blob 的操作备份在本质上是连续的，因此无需日程安排即可执行备份。

```json
"policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
```

> [!NOTE]
> 对较长持续时间进行还原可能会导致还原操作需要较长时间才能完成。 此外，还原数据集所用的时间还取决于在还原期间执行的写入和删除操作的次数。 例如，如果一个帐户有 1 百万个对象，每天增加 3,000 个对象，每天删除 1,000 个对象，将需要大约两个小时才能还原到过去 30 天的点。<br><br>对于具有此变化率的帐户，不建议保持期和还原超过过去 90 天。

一旦策略 JSON 具有所有所需值，请使用 [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create) 命令从策略对象创建新策略。

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n BlobBackup-Policy --policy policy.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
    "name": "BlobBackup-Policy",
    "properties": {
      "datasourceTypes": [
        "Microsoft.Storage/storageAccounts/blobServices"
      ],
      "objectType": "BackupPolicy",
      "policyRules": [
        {
          "isDefault": true,
          "lifecycles": [
            {
              "deleteAfter": {
                "duration": "P2D",
                "objectType": "AbsoluteDeleteOption"
              },
              "sourceDataStore": {
                "dataStoreType": "OperationalStore",
                "objectType": "DataStoreInfoBase"
              },
              "targetDataStoreCopySettings": []
            }
          ],
          "name": "Default",
          "objectType": "AzureRetentionRule"
        }
      ]
    },
    "resourceGroup": "testBkpVaultRG",
    "systemData": null,
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
  }
```

## <a name="configure-backup"></a>配置备份

创建保管库和策略后，用户需要考虑 2 个关键点来保护存储帐户中的所有 Azure Blob。

### <a name="key-entities-involved"></a>所涉关键实体

#### <a name="storage-account-that-contains-the-blobs-to-be-protected"></a>含有要保护的 Blob 的存储帐户

提取含有要保护的 Blob 的存储帐户的 Azure 资源管理器 ID。 用作存储帐户的标识符。 我们将在 blobrg 资源组下，在东南亚地区的不同订阅中使用名为“CLITestSA”的存储帐户示例。

```azurecli-interactive
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
```

#### <a name="backup-vault"></a>备份保管库

备份保管库需要存储帐户的权限才能对存储帐户中的 Blob 启用备份。 保管库的系统分配托管标识用于分配此类权限。

### <a name="assign-permissions"></a>分配权限

你需要通过 RBAC 向保管库（以保管库 MSI 表示）和相关存储帐户分配一些权限。 可以通过门户或 PowerShell 执行上述操作。 详细了解所有[相关权限](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts)。

### <a name="prepare-the-request"></a>准备请求

一旦设置了所有相关的权限，就可以分 2 步执行备份配置。 首先，我们使用相关保管库、策略、使用 [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize) 命令的存储帐户来准备相关请求。 然后，使用 [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create) 命令提交磁盘保护请求。

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureBlob  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" > backup_instance.json
```

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "name": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
        "resourceLocation": "southeastasia",
        "resourceName": "CLITestSA",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
      },
      "dataSourceSetInfo": null,
      "friendlyName": "CLITestSA",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": ""
            }
          ]
        },
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "resourceGroup": "testBkpVaultRG",
    "systemData": null,
    "type": "Microsoft.DataProtection/backupVaults/backupInstances"
  }
```

> [!IMPORTANT]
> 为 Blob 备份配置存储帐户后，一些功能会受到影响，例如更改源和删除锁。 [了解详细信息](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts)。

## <a name="next-steps"></a>后续步骤

[使用 Azure CLI 还原 Azure Blob](restore-blobs-storage-account-cli.md)