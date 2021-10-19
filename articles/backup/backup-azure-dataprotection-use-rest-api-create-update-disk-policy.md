---
title: 使用数据保护 REST API 为磁盘创建备份策略
description: 在本文中，你将了解如何使用 REST API 为磁盘创建和管理备份策略。
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: ecc107c0-311c-42d0-a094-654d7ee30443
ms.openlocfilehash: 0df0b23d921076959718e79d5674aa8d561639e3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620808"
---
# <a name="create-azure-data-protection-backup-policies-for-disks-using-rest-api"></a>使用 REST API 为磁盘创建 Azure 数据保护备份策略

备份策略控制备份的保留和计划。 Azure 磁盘备份每天提供多次备份。

可以重复使用备份策略为多个 Azure 磁盘配置到保管库的备份，或者[使用 REST API 为 Azure 恢复服务保管库创建备份策略](/rest/api/dataprotection/backup-policies/create-or-update)。

若要创建用于备份磁盘的策略，请执行以下操作：

## <a name="create-a-policy"></a>创建策略

>[!IMPORTANT]
>目前不支持更新或修改现有策略。 替代方法是使用所需的详细信息创建新策略，并将其分配给相关的备份实例。

若要创建 Azure 备份策略，请使用以下 PUT 操作：

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

`{policyName}` 和 `{vaultName}` 在 URI 中提供。 其他信息在请求正文中提供。

## <a name="create-the-request-body"></a>创建请求正文

例如，若要为磁盘备份创建策略，请求正文中需包含以下组件：

|名称  |必须  |类型  |说明  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource 属性        |

有关请求正文中的完整定义列表，请参阅[备份策略 REST API 文档](/rest/api/dataprotection/backup-policies/create-or-update)。

### <a name="example-request-body"></a>示例请求正文

该策略指出：

- 计划每 4 小时运行触发器 (PT4H)。 然后，系统会按大约 4 小时的间隔执行备份，以便备份在一天中均匀分布。
- 可以选择每 4、6、8 或 12 小时的触发器间隔。 若要将备份计划为每天一次，请使用 P1D。 备份在规定的时间每天触发一次。
- 数据存储是“操作存储”，因为备份是本地的，不会将任何数据存储在备份保管库中。 在操作存储中，每个备份实例将存储 7 天 (P7D)。

```json
{
"properties": {
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
}
```

>[!IMPORTANT]
>时间格式仅支持日期/时间。 不支持仅时间格式。 每日备份的时间表示备份开始时间，而不是备份完成时间。

完成备份操作所需时间取决于各种因素，包括磁盘大小和连续备份的更改率。 但是，Azure 磁盘备份是使用[增量快照](../virtual-machines/disks-incremental-snapshots.md)的无代理备份，不会影响生产应用程序的性能。

若要了解有关策略创建的详细信息，请参阅 [Azure 磁盘备份策略](backup-managed-disks.md#create-backup-policy)文档。

## <a name="responses"></a>响应

备份策略创建/更新是同步操作，操作成功便会返回 OK。

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  确定       |

### <a name="example-responses"></a>示例响应

操作完成后，它将在响应正文中返回 200 (OK) 和策略内容。

```json
{
    "id": "/subscriptions/73307177-bb00-4801-bd11-894b2f2d5162/resourceGroups/RG-BV/providers/Microsoft.DataProtection/backupVaults/BV-JPE-GRS/backupPolicies/DiskBackupPolicy-03",
    "name": "DiskBackupPolicy-03",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Incremental",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-07-01T19:00:00+00:00/P1D"
                        ],
                      },
                    "taggingCriteria": [
                        {
                            "tagInfo": {
                                "tagName": "Default",
                                "id": "Default_"
                            },
                            "taggingPriority": 99,
                            "isDefault": true
                        }
                    ],
                    "objectType": "ScheduleBasedTriggerContext"
                },
                "dataStore": {
                    "dataStoreType": "OperationalStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupDaily",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P7D"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "OperationalStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    }
                ],
                "isDefault": true,
                "name": "Default",
                "objectType": "AzureRetentionRule"
            }
        ],
        "datasourceTypes": [
            "Microsoft.Compute/disks"
        ],
        "objectType": "BackupPolicy"
    }
}
```

## <a name="next-steps"></a>后续步骤

[为 Azure 磁盘启用保护](backup-azure-dataprotection-use-rest-api-backup-disks.md)

有关 Azure 备份 REST API 的详细信息，请参阅以下文章：

- [Azure 数据保护 REST API](/rest/api/dataprotection/)
- [Azure REST API 入门](/rest/api/azure/)
