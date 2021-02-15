---
title: 使用 Azure PowerShell 在 Azure Cosmos DB 中配置连续备份和时间点还原。
description: 了解如何使用 Azure PowerShell 为帐户预配连续备份和还原数据。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 5261075a82eaefd91cbedd2dd2fe08cb1e0a20b4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381828"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>配置和管理连续备份和时间点还原 (预览) -使用 Azure PowerShell
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> 时间点还原功能 (持续备份模式) 用于 Azure Cosmos DB 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 的时间点还原功能 (预览版) 可帮助你从容器内的意外更改中恢复，还原已删除的帐户、数据库或容器，或还原到任何备份存在) 的区域 (。 连续备份模式允许您在过去30天内还原到任何时间点。

本文介绍如何使用 Azure PowerShell 设置包含连续备份和还原数据的帐户。

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>安装 Azure PowerShell

1. 在 Azure PowerShell 中运行以下命令以安装 `Az.CosmosDB` 预览模块，其中包含与时间点还原相关的命令：

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. 安装模块后，使用登录到 Azure

   ```azurepowershell
   Connect-AzAccount
   ```

1. 使用以下命令选择特定订阅：

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>使用连续备份预配 SQL API 帐户

若要设置具有连续备份的帐户，请添加参数 `-BackupPolicyType Continuous` 以及常规设置命令。

以下 cmdlet 是一个区域写入帐户示例， `pitracct2` 其中包含在 " *myrg* " 资源组下的 "*美国西部*" 区域中创建的连续备份策略：

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>预配包含连续备份的 MongoDB API 帐户

以下 cmdlet 是在 *美国西部* 区域的 *myrg* 资源组下创建的连续备份帐户 *pitracct2* 的示例：

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

下面的 cmdlet 是一个示例，使用 restore 命令通过目标帐户、源帐户、位置、资源组和时间戳来触发还原操作：

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**示例1：** 还原整个帐户：

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**示例2：** 还原特定的集合和数据库。 此示例将 myCol1、myCol2 和 myDB1 之间的集合，其中包含所有容器。

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

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>枚举 SQL API 的可恢复资源

枚举 cmdlet 可帮助你发现可用于在各种时间戳上还原的资源。 此外，它们还提供可恢复帐户、数据库和容器资源上的密钥事件的源。

**列出可在当前订阅中还原的所有帐户**

运行 `Get-AzCosmosDBRestorableDatabaseAccount` PowerShell 命令以列出可在当前订阅中还原的所有帐户。

响应包括所有数据库帐户 (可以还原的 "实时" 和 "已删除") 以及可以还原的区域。

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

与帐户的 `CreationTime` 或类似 `DeletionTime` ，该区域也有一个 `CreationTime` 或 `DeletionTime` 。 利用这些时间，你可以选择正确的区域和要还原到该区域的有效时间范围。

**列出实时数据库帐户中的所有 SQL 数据库版本**

列出数据库的所有版本可在数据库存在的实际时间未知的情况下选择适当的数据库。

运行以下 PowerShell 命令以列出数据库的所有版本。 此命令仅适用于 live 帐户。 `DatabaseAccountInstanceId`和 `LocationName` 参数是从 cmdlet 的响应中获取的和 `name` `location` 属性 `Get-AzCosmosDBRestorableDatabaseAccount` 。 该 `DatabaseAccountInstanceId` 属性引用要 `instanceId` 还原的源数据库帐户的属性：


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**列出实时数据库帐户中数据库的所有 SQL 容器版本。**

使用以下命令可列出 SQL 容器的所有版本。 此命令仅适用于 live 帐户。 `DatabaseRid`参数是 `ResourceId` 要还原的数据库的。 它是 `ownerResourceid` 在 cmdlet 响应中找到的属性的值 `Get-AzCosmosdbSqlRestorableDatabase` 。 响应还包括对此数据库中的所有容器执行的操作的列表。

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**查找可在任意给定时间戳还原的数据库或容器**

使用以下命令可获取可在任意给定时间戳还原的数据库或容器的列表。 此命令仅适用于 live 帐户。

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>枚举 MongoDB 的可恢复资源

下面描述的枚举命令可帮助你发现可在各种时间戳上还原的资源。 此外，它们还提供可恢复帐户、数据库和容器资源上的密钥事件的源。 这些命令仅适用于 live 帐户，并与 SQL API 命令类似，但使用 `MongoDB` 命令名称而不是 `sql` 。

**列出实时数据库帐户中 MongoDB 数据库的所有版本**

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

**列出可在给定时间戳和区域还原的 mongodb 数据库帐户的所有资源**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>后续步骤

* 使用 [Azure CLI](continuous-backup-restore-command-line.md)、 [资源管理器](continuous-backup-restore-template.md)或 [Azure 门户](continuous-backup-restore-portal.md)配置和管理连续备份。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
* [管理](continuous-backup-restore-permissions.md) 以连续备份模式还原数据所需的权限。
