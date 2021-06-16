---
title: 使用 Azure PowerShell 在 Azure Cosmos DB 中配置连续备份和时间点还原。
description: 了解如何使用 Azure PowerShell 为帐户预配连续备份和还原数据。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8f3a99e7dca81e669438498f78447eddfdddd583
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110662116"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>使用 Azure PowerShell 配置和管理连续备份和时间点还原（预览版）
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB 的时间点还原功能（连续备份模式）目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 的时间点还原功能（预览版）可帮助你从容器内的意外更改中恢复，还原已删除的帐户、数据库或容器，或者还原到任何区域（需存在备份）。 通过连续备份模式，可还原到过去 30 天内的任何时间点。

本文介绍如何使用 Azure PowerShell 为帐户预配连续备份和还原数据。

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>安装 Azure PowerShell

1. 从 Azure PowerShell 运行以下命令以安装 `Az.CosmosDB` 预览版模块，其中包含与时间点还原相关的命令：

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. 安装模块后，使用以下命令登录到 Azure

   ```azurepowershell
   Connect-AzAccount
   ```

1. 使用以下命令选择特定订阅：

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>为 SQL API 帐户预配连续备份

要为帐户预配连续备份，请添加参数 `-BackupPolicyType Continuous` 以及常规预配命令。

以下 cmdlet 是单个区域写入帐户 `pitracct2` 的示例，其连续备份策略在“美国西部”区域中的“myrg”资源组下创建：

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>为 MongoDB API 帐户预配连续备份

以下 cmdlet 是在“美国西部”区域的“myrg”资源组下创建的连续备份帐户“pitracct2”的示例：

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>触发还原操作

以下 cmdlet 是通过使用目标帐户、源帐户、位置、资源组和时间戳，使用还原命令触发还原操作的示例：

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**示例 1：** 还原整个帐户：

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**示例 2：** 还原特定集合和数据库。 此示例还原 myDB1 中的集合 myCol1、myCol2 以及其下包含所有容器的整个数据库 myDB2。

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>枚举 SQL API 的可还原资源

枚举 cmdlet 可帮助你发现用于在各种时间戳上还原的资源。 此外，它们还提供可还原帐户、数据库和容器资源上的密钥事件源。

**列出可在当前订阅中还原的所有帐户**

运行 `Get-AzCosmosDBRestorableDatabaseAccount` PowerShell 命令以列出可在当前订阅中还原的所有帐户。

响应包括可还原的所有数据库帐户（实时帐户和已删除帐户）以及可从中还原的区域。

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

就像帐户的 `CreationTime` 或 `DeletionTime` 一样，区域也有一个 `CreationTime` 或 `DeletionTime`。 通过这些时间，你可以选择正确的区域和要还原到该区域的有效时间范围。

**列出实时数据库帐户中的 SQL 数据库的所有版本**

通过列出数据库的所有版本，可在数据库存在的实际时间未知的情况下选择正确的数据库。

运行以下 PowerShell 命令以列出数据库的所有版本。 此命令仅适用于实时帐户。 `DatabaseAccountInstanceId` 和 `LocationName` 参数是从 `Get-AzCosmosDBRestorableDatabaseAccount` cmdlet 的响应中获取的 `name` 和 `location` 属性。 `DatabaseAccountInstanceId` 属性引用要还原的源数据库帐户的 `instanceId` 属性：


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**列出实时数据库帐户中数据库的 SQL 容器的所有版本。**

使用以下命令以列出 SQL 容器的所有版本。 此命令仅适用于实时帐户。 `DatabaseRid` 参数是要还原的数据库的 `ResourceId`。 它是在 `Get-AzCosmosdbSqlRestorableDatabase` cmdlet 的响应中找到的 `ownerResourceid` 属性的值。 响应中还包括对此数据库内的所有容器执行的操作的列表。

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**查找可在任意给定时间戳还原的数据库或容器**

使用以下命令以获取可在任意给定时间戳还原的数据库或容器的列表。 此命令仅适用于实时帐户。

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>枚举 MongoDB 的可还原资源

下述枚举命令可帮助你发现用于在各种时间戳上还原的资源。 此外，它们还提供可还原帐户、数据库和容器资源上的密钥事件源。 这些命令仅适用于实时帐户，与 SQL API 命令类似，但命令名称中使用 `MongoDB` 而不是 `sql`。

**列出实时数据库帐户中的 MongoDB 数据库的所有版本**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**列出实时数据库帐户中数据库的 mongodb 集合的所有版本**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

列出可在给定时间戳和区域还原的 MongoDB 数据库帐户的所有资源

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>后续步骤

* 使用 [Azure CLI](continuous-backup-restore-command-line.md)、[资源管理器](continuous-backup-restore-template.md)或 [Azure 门户](continuous-backup-restore-portal.md)配置和管理连续备份。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
* [管理](continuous-backup-restore-permissions.md)以连续备份模式还原数据所需的权限。
