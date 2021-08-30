---
title: 使用数据保护 REST API 为 Blob 创建备份策略
description: 在本文中，你将了解如何使用 REST API 为 Blob 创建和管理备份策略。
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 472d6a4f-7914-454b-b8e4-062e8b556de3
ms.openlocfilehash: 96c76eb592b0fb7b94aa8da6c4f975878dc8c913
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471555"
---
# <a name="create-azure-data-protection-backup-policies-for-blobs-using-rest-api"></a>使用 REST API 为 Blob 创建 Azure 数据保护备份策略

> [!IMPORTANT]
> 在继续为 Azure Blob 创建策略和配置备份之前，请阅读[本节](blob-backup-configure-manage.md#before-you-start)。

备份策略通常控制备份的保留和计划。 由于 blob 的操作备份在本质上是连续的，因此无需计划即可执行备份。 实质上是需要策略来指定保留期。 可以重复使用备份策略来配置多个存储帐户到保管库的备份。

>[!NOTE]
>对较长持续时间进行还原可能会导致还原操作需要较长时间才能完成。 而且，还原一组数据所用的时间取决于在还原期间执行的写入和删除操作的次数。 例如，如果一个帐户有 1 百万个对象，每天增加 3,000 个对象，每天删除 1,000 个对象，将需要大约两个小时才能还原到过去 30 天的点。 对于具有此变化率的帐户，不建议在过去保留时间和还原超过90天。

策略 [REST API 文档](/rest/api/dataprotection/backup-policies/create-or-update)中概述了为 Azure 恢复服务保管库创建备份策略的步骤。 我们将参考此文档，为存储帐户中的 Blob 创建策略。

## <a name="create-a-policy"></a>创建策略

> [!IMPORTANT]
> 目前，我们不支持更新或修改现有策略。 替代方法是使用所需的详细信息创建新策略，并将其分配给相关的备份实例。

要创建 Azure 备份策略，请使用以下 PUT 操作

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

`{policyName}` 和 `{vaultName}` 在 URI 中提供。 其他信息在请求正文中提供。

## <a name="create-the-request-body"></a>创建请求正文

例如，要为 Blob 备份创建策略，需在请求正文中包含以下组成部分。

|名称  |必须  |类型  |说明  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource 属性        |

有关请求正文中的完整定义列表，请参阅[备份策略 REST API 文档](/rest/api/dataprotection/backup-policies/create-or-update)。

### <a name="example-request-body"></a>示例请求正文

以下请求正文定义 Blob 备份的备份策略。

该策略指出：

- 保留期为 30 天。
- 数据存储是“操作存储”，因为备份是本地的，不会将任何数据存储在备份保管库中。

```json
{
  "properties": {
    "datasourceTypes": [
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy",
    "policyRules": [
      {
        "name": "Default",
        "objectType": "AzureRetentionRule",
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
        ]
      }
    ]
  }
}
```

> [!IMPORTANT]
> 时间格式仅支持日期/时间。 不支持单独的时间格式。

## <a name="responses"></a>响应

备份策略创建/更新是同步操作，操作成功便会返回 OK。

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  确定       |

### <a name="example-responses"></a>示例响应

操作完成后，它将在响应正文中返回 200 (OK) 和策略内容。

```json
{
  "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups//TestBkpVaultRG/providers/Microsoft.RecoveryServices/vaults/testBkpVault/backupPolicies/TestBlobPolicy",
  "name": "TestBlobPolicy",
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
  "properties": {
    "policyRules": [
      {
        "lifecycles": [
          {
            "deleteAfter": {
              "objectType": "AbsoluteDeleteOption",
              "duration": "P30D"
            },
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
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy"
  }
}
```

## <a name="next-steps"></a>后续步骤

为存储帐户中的 Blob 启用保护。

有关 Azure 备份 REST API 的详细信息，请参阅以下文档：

- [Azure 数据保护 REST API](/rest/api/dataprotection/)
- [Azure REST API 入门](/rest/api/azure/)
