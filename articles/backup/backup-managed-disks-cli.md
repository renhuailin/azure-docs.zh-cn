---
title: 使用 Azure CLI 备份 Azure 托管磁盘
description: 了解如何使用 Azure CLI 备份 Azure 托管磁盘。
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 42bbc54a545cdddf049163b3040d9701f4304a52
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112381205"
---
# <a name="back-up-azure-managed-disks-using-azure-cli"></a>使用 Azure CLI 备份 Azure 托管磁盘

本文介绍如何使用 Azure CLI 备份 [Azure 托管磁盘](../virtual-machines/managed-disks-overview.md)。

> [!IMPORTANT]
> 通过 CLI 实现的 Azure 托管磁盘备份和还原的支持以预览版提供，并在 Az 2.15.0 版本及更高版本中作为扩展提供。 运行 az dataprotection 命令时会自动安装该扩展。 [详细了解](/cli/azure/azure-cli-extensions-overview)扩展。

本文将指导如何进行以下操作：

- 创建备份保管库

- 创建备份策略

- 配置 Azure 磁盘备份

- 运行按需备份作业

有关 Azure 磁盘备份区域可用性、支持的方案和限制的详细信息，请参阅[支持矩阵](disk-backup-support-matrix.md)。

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

创建保管库后，让我们创建备份策略来保护 Azure 磁盘。

## <a name="create-a-backup-policy"></a>创建备份策略

若要了解 Azure 磁盘备份的备份策略的内部组件，请使用 [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) 命令来检索策略模板。 此命令返回给定数据源类型的默认策略模板。 使用此策略模板创建新策略。

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk

{
  "datasourceTypes": [
    "Microsoft.Compute/disks"
  ],
  "name": "DiskPolicy",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Incremental",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "OperationalStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupHourly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        },
        "taggingCriteria": [
          {
            "isDefault": true,
            "tagInfo": {
              "id": "Default_",
              "tagName": "Default"
            },
            "taggingPriority": 99
          }
        ]
      }
    },
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
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

策略模板由触发器（决定备份的触发因素）和生命周期（决定删除/复制/移动备份的时间）。 在 Azure 磁盘备份中，触发器的默认值为每 4 小时 (PT4H) 定时触发，并将每个备份保留 7 天。

定时触发：

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        }

```

默认保留生命周期：

```json
"lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ]
```

Azure 磁盘备份每天提供多次备份。 如果需要更频繁地进行备份，请选择“每小时”备份频率，让你能够以每 4、6、8 或 12 小时的间隔进行备份。 根据所选的“时间”间隔安排备份。

例如，如果选择“每 4 小时”备份一次，则会按 4 小时的间隔执行备份，以便备份在一天中均匀分布。 如果每天备份一次即可满足需求，则选择“每日”备份频率。 可在每日备份频率中指定每日备份时间。

>[!IMPORTANT]
>每日备份时间表示备份开始时间，而不是备份完成时间。

完成备份操作所需时间取决于各种因素，包括磁盘大小和连续备份的更改率。 但是，Azure 磁盘备份是使用[增量快照](../virtual-machines/disks-incremental-snapshots.md)的无代理备份，不会影响生产应用程序的性能。

   >[!NOTE]
   >尽管所选保管库可能具有全局冗余设置，但当前 Azure 磁盘备份仅支持快照数据存储。 所有备份都存储在订阅的资源组中，不会复制到备份保管库中。

若要了解有关策略创建的详细信息，请参阅 [Azure 磁盘备份策略](backup-managed-disks.md#create-backup-policy)文档。

将模板下载为 JSON 文件后，可对其进行编辑，以根据需要进行计划和保留。 然后使用生成的 JSON 创建新策略。 如果要编辑每小时频率或保持期，请使用 [az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_trigger_set) 和/或 [az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_retention_rule_set) 命令。 一旦策略 JSON 具有所有所需值，请使用 [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create) 命令从策略对象创建新策略。

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n mypolicy --policy policy.json

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy",
"name": "mypolicy",
"properties": {
"datasourceTypes": [
"Microsoft.Compute/disks"
],
"objectType": "BackupPolicy",
"policyRules": [
{
"backupParameters": {
"backupType": "Incremental",
"objectType": "AzureBackupParams"
},
"dataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"name": "BackupHourly",
"objectType": "AzureBackupRule",
"trigger": {
"objectType": "ScheduleBasedTriggerContext",
"schedule": {
"repeatingTimeIntervals": [
"R/2020-04-05T13:00:00+00:00/PT4H"
]
},
"taggingCriteria": [
{
"criteria": null,
"isDefault": true,
"tagInfo": {
"eTag": null,
"id": "Default_",
"tagName": "Default"
},
"taggingPriority": 99
}
]
}
},
{
"isDefault": true,
"lifecycles": [
{
"deleteAfter": {
"duration": "P7D",
"objectType": "AbsoluteDeleteOption"
},
"sourceDataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"targetDataStoreCopySettings": null
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

创建保管库和策略后，需要考虑 3 个关键点来保护 Azure 磁盘。

### <a name="key-entities-involved"></a>所涉关键实体

#### <a name="disk-to-be-protected"></a>要保护的磁盘

提取要保护的磁盘的 ARM ID 和位置。 用作磁盘的标识符。 我们以其他订阅的资源组“diskrg”下名为“CLITestDisk”的磁盘为例。

```azurecli-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
```

#### <a name="snapshot-resource-group"></a>快照资源组

磁盘快照存储在订阅内的资源组中。 作为指导原则，建议创建专用资源组作为 Azure 备份服务使用的快照数据存储。 专用资源组可以限制资源组的访问权限，确保备份数据管理的安全性和便捷性。 请注意要放置磁盘快照的资源组 ARM ID

```azurecli-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>备份保管库

备份保管库需要磁盘和快照资源组的权限，以便能够触发快照并管理其生命周期。 保管库的系统分配托管标识用于分配此类权限。 使用 [az dataprotection backup-vault update](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_update) 命令启用恢复服务保管库的系统分配托管标识。

```azurecli-interactive
az dataprotection backup-vault update -g testBkpVaultRG --vault-name TestBkpVault --type SystemAssigned
```

### <a name="assign-permissions"></a>分配权限

你需要通过 RBAC 向保管库（保管库 MSI 表示）、相关磁盘和/或磁盘 RG 分配少量权限。 可以通过 Azure 门户或 CLI 执行上述操作。 [配置备份](backup-managed-disks.md#configure-backup)的第 1、2、3 点详细介绍了所有相关权限。

### <a name="prepare-the-request"></a>准备请求

一旦设置了所有相关的权限，就可以分 2 步执行备份配置。 首先，使用 [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize) 命令通过使用相关保管库、策略、磁盘和快照资源组准备相关请求。 initialize 命令将返回 JSON 文件，然后必须更新快照资源组值。 然后，使用 [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create) 命令提交磁盘保护请求。

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureDisk  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk" > backup_instance.json
```

打开 JSON 文件，在 ```data_store_parameters_list``` 部分下的 ``` resource_group_id ``` 中编辑“快照资源组 ID”。

```json
{
  "backup_instance_name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "data_source_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "data_source_set_info": null,
    "object_type": "BackupInstance",
    "policy_info": {
      "policy_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policy_parameters": {
        "data_store_parameters_list": [
          {
            "data_store_type": "OperationalStore",
            "object_type": "AzureOperationalStoreParameters",
            "resource_group_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
          }
        ]
      }
    }
  }
}
```

> [!NOTE]
> 备份实例名称由客户端生成，因此这将是唯一值。 它基于数据源名称和唯一 GUID。 列出备份实例后，可以检查备份实例的名称和相关数据源名称。

使用编辑的 JSON 文件创建 Azure 托管磁盘的备份实例。

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json


{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "currentProtectionState": "ProtectionConfigured",
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resourceLocation": "southeastasia",
      "resourceName": "CLITestDisk",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
    },
    "dataSourceSetInfo": null,
    "friendlyName": "CLITestDisk",
    "objectType": "BackupInstance",
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/sarath-rg"
          }
        ]
      },
      "policyVersion": null
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

创建备份实例后，如果不想等待策略计划，可以继续触发按需备份。

## <a name="run-an-on-demand-backup"></a>运行按需备份

使用 [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true) 命令列出保管库中的所有备份实例，然后使用 [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true) 命令获取相关实例。 或者，对于大规模场景，可以使用 [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) 命令列出保管库和订阅中的备份实例。

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.Compute/disks",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
        "resourceLocation": "westus",
        "resourceName": "CLITestDisk",
        "resourceType": "Microsoft.Compute/disks",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": null,
      "datasourceAuthCredentials": null,
      "friendlyName": "CLITestDisk",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
            }
          ]
        },
        "policyVersion": null
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "TestBkpVault",
    "zones": null
  }
]


```

可在触发备份时指定保留规则。 若要查看策略中的保留规则，请浏览策略 JSON 中的保留规则。 以下示例显示名为“默认”的规则，我们将使用该规则进行按需备份。

```JSON
{
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
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
```

使用 [az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup) 命令触发按需备份。

```azurecli-interactive
az dataprotection backup-instance adhoc-backup --name "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166" --rule-name "Default" --resource-group "000pikumar" --vault-name "PratikPrivatePreviewVault1"
```

## <a name="tracking-jobs"></a>跟踪作业

使用 [az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) 命令跟踪所有作业。 你可以列出所有作业并提取特定作业详细信息。

还可以使用 Az.ResourceGraph 跟踪所有备份保管库中的所有作业。 使用 [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) 命令获取可以跨任何备份保管库的相关作业。

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --status Completed
```

## <a name="next-steps"></a>后续步骤

[使用 Azure CLI 还原 Azure 托管磁盘](restore-managed-disks-cli.md)
