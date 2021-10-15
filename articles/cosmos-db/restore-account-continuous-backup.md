---
title: 还原使用连续备份模式的 Azure Cosmos DB 帐户。
description: 了解如何确定还原时间并将实时的或已删除的 Azure Cosmos DB 帐户还原。 本文介绍如何使用事件源来确定还原时间，以及如何使用 Azure 门户、PowerShell、CLI 或资源管理器模板来还原帐户。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1f8622b37055cf8585e9c43f2e822756ac06d1de
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352186"
---
# <a name="restore-an-azure-cosmos-db-account-that-uses-continuous-backup-mode"></a>还原使用连续备份模式的 Azure Cosmos DB 帐户
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB 的时间点还原功能可帮助你从容器内的意外更改中恢复，还原已删除的帐户、数据库或容器，或者还原到任何区域（其中已存在备份）。 通过连续备份模式，可还原到过去 30 天内的任何时间点。

本文介绍了如何确定还原时间并将实时的或已删除的 Azure Cosmos DB 帐户还原。 本文演示了如何使用 [Azure 门户](#restore-account-portal)、[PowerShell](#restore-account-powershell)、[CLI](#restore-account-cli) 或[资源管理器模板](#restore-arm-template)来还原帐户。

## <a name="restore-an-account-using-azure-portal"></a><a id="restore-account-portal"></a>使用 Azure 门户还原帐户

### <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>将实时帐户从意外修改中还原

可以使用 Azure 门户来还原整个实时帐户或该帐户下所选的数据库和容器。 还原数据需要完成以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)
1. 导航到 Azure Cosmos DB 帐户，打开“时间点还原”边栏选项卡。

   > [!NOTE]
   > 只有在你拥有 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 权限的情况下，才会填充 Azure 门户中的“还原”边栏选项卡。 若要了解有关如何设置此权限的详细信息，请参阅[备份和还原权限](continuous-backup-restore-permissions.md)一文。

1. 填写以下详细信息以进行还原：

   * **还原点 (UTC)** – 在过去 30 天内的时间戳。 该帐户应存在于该时间戳下。 可以指定 UTC 格式的还原点。 如果你希望还原，可将该还原点设为与第二个还原点相接近。 选择“单击此处”，获取有关[标识还原点](#event-feed)的帮助。

   * **位置** – 还原帐户的目标区域。 在给定时间戳此帐户应存在于该区域中（例如美国西部或美国东部）。 帐户只能还原到源帐户所在的区域。

   * **还原资源** – 可选择“整个帐户”，也可选择要还原的“所选数据库/容器”。 数据库和容器应存在于规定时间戳下。 根据所选的还原点和位置，将填充恢复资源，以便用户可以选择需要还原的特定数据库或容器。

   * **资源组** - 将在该资源组下创建并还原目标帐户。 此资源组必须已存在。

   * **还原目标帐户** – 目标帐户名称。 目标帐户名称需要遵循与创建新帐户时相同的准则。 此帐户将通过源帐户所在的同一区域中的还原过程来创建。
 
   :::image type="content" source="./media/restore-account-continuous-backup/restore-live-account-portal.png" alt-text="使用 Azure 门户将实时帐户从意外修改中还原。" border="true" lightbox="./media/restore-account-continuous-backup/restore-live-account-portal.png":::

1. 选择以上参数后，选择“提交”按钮，开始还原。 还原费用是一次性的费用，具体取决于数据大小和所选区域中备份存储的费用。 如要了解详细信息，请参阅[定价](continuous-backup-restore-introduction.md#continuous-backup-pricing)部分。

在还原过程中删除源帐户可能会导致还原失败。

### <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>使用事件源来标识还原时间

在 Azure 门户中填写还原点时间时，如果不知道如何确定还原点，请选择“单击此处”链接，转到“事件源”边栏选项卡。 事件源提供了创建、替换、删除数据库和源帐户容器上的事件的完整保真列表。 

例如，如果要在删除或更新某个容器之前还原到该点，请检查此事件源。 事件将按事件发生时的时间降序显示，最新事件显示在顶部。 用户可以浏览结果，并选择事件前后的时间以进一步缩小时间范围。

:::image type="content" source="./media/restore-account-continuous-backup/event-feed-portal.png" alt-text="使用事件源来标识还原时间。" border="true" lightbox="./media/restore-account-continuous-backup/event-feed-portal.png":::

> [!NOTE]
> 事件源不会显示对项资源所做的更改。 你可以随时手动指定过去 30 天内的任何时间戳（只要在该时间戳有账户存在）进行还原。

### <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>还原已删除的帐户

可使用 Azure 门户在删除帐户的 30 天内完全还原该帐户。 完成以下步骤还原已删除的帐户：

1. 登录到 [Azure 门户](https://portal.azure.com/)
1. 在全局搜索栏中搜索“Azure Cosmos DB”资源。 随后将列出所有现有帐户。
1. 接下来，选择“还原”按钮。 “还原”边栏选项卡会显示可在保留期内还原的已删除帐户的列表，保留期为自删除时间起 30 天。
1. 选择要还原的帐户。

   :::image type="content" source="./media/restore-account-continuous-backup/restore-deleted-account-portal.png" alt-text="从 Azure 门户还原已删除的帐户。" border="true" lightbox="./media/restore-account-continuous-backup/restore-deleted-account-portal.png":::

   > [!NOTE]
   > 只有在你拥有 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 权限的情况下，才会填充 Azure 门户中的“还原”边栏选项卡。 若要了解有关如何设置此权限的详细信息，请参阅[备份和还原权限](continuous-backup-restore-permissions.md)一文。

1. 选择要还原的帐户，并输入以下详细信息还原已删除的帐户：

   * **还原点 (UTC)** – 在过去 30 天内的时间戳。 该时间戳下应已存在该账户。 指定 UTC 格式的还原点。 如果你希望还原，可将该还原点设为与第二个还原点相接近。

   * **位置** – 还原帐户的目标区域。 源帐户应存在于规定时间戳下以及此区域中。 例如，美国西部或美国东部。  

   * **资源组** - 将在该资源组下创建并还原目标帐户。 此资源组必须已存在。

   * **还原目标帐户** - 目标帐户名称需要遵循与创建新帐户时相同的准则。 此帐户将通过源帐户所在的同一区域中的还原过程来创建。

### <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>跟踪还原操作的状态

启动还原操作后，选择门户右上角的“通知”钟形图标。 它将提供一个显示正在还原的帐户状态的链接。 在还原过程中，帐户状态将为“正在创建”，还原操作完成后，帐户状态将更改为“联机”。

:::image type="content" source="./media/restore-account-continuous-backup/track-restore-operation-status.png" alt-text="当操作完成时，已还原帐户的状态从“正在创建”更改为“联机”。" border="true" lightbox="./media/restore-account-continuous-backup/track-restore-operation-status.png":::

## <a name="restore-an-account-using-azure-powershell"></a><a id="restore-account-powershell"></a>使用 Azure PowerShell 还原帐户

在还原帐户之前，请安装[最新版 Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true) 或高于 6.2.0 的版本。 接下来，请使用以下命令连接到 Azure 帐户并选择所需的订阅：

1. 使用以下命令登录到 Azure：

   ```azurepowershell
   Connect-AzAccount
   ```

1. 使用以下命令选择特定订阅：

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>

### <a id="trigger-restore-ps"></a>Trigger a restore operation

The following cmdlet is an example to trigger a restore operation with the restore command by using the target account, source account, location, resource group, and timestamp:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "RestoredAccountName" `
  -SourceDatabaseAccountName "SourceDatabaseAccountName" `
  -RestoreTimestampInUtc "UTCTime" `
  -Location "AzureRegionName"

```

**示例 1：** 还原整个帐户：

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**示例 2：** 还原特定集合和数据库。 此示例还原 MyDB1 中的集合 MyCol1、MyCol2，以及整个数据库 MyDB2（包含其中的所有容器）。   

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB1" -CollectionName "MyCol1", "MyCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "MyDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "MyRG" `
  -TargetDatabaseAccountName "Pitracct" `
  -SourceDatabaseAccountName "SourceSql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>枚举 SQL API 的可还原资源

枚举 cmdlet 可帮助你发现用于在各种时间戳上还原的资源。 此外，它们还提供可还原帐户、数据库和容器资源上的密钥事件源。

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>列出可在当前订阅中还原的所有帐户

运行 `Get-AzCosmosDBRestorableDatabaseAccount` PowerShell 命令以列出可在当前订阅中还原的所有帐户。

响应包括可还原的所有数据库帐户（实时帐户和已删除帐户）以及可从中还原的区域。

```json
{
    "accountName": "SampleAccount",
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
        "location": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "location": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

就像帐户的 `CreationTime` 或 `DeletionTime` 一样，区域也有一个 `CreationTime` 或 `DeletionTime`。 通过这些时间，你可以选择正确的区域和要还原到该区域的有效时间范围。

#### <a name="list-all-the-versions-of-sql-databases-in-a-live-database-account"></a>列出实时数据库帐户中的 SQL 数据库的所有版本

通过列出数据库的所有版本，可在数据库存在的实际时间未知的情况下选择正确的数据库。

运行以下 PowerShell 命令以列出数据库的所有版本。 此命令仅适用于实时帐户。 `DatabaseAccountInstanceId` 和 `Location` 参数是从 `Get-AzCosmosDBRestorableDatabaseAccount` cmdlet 的响应中获取的 `name` 和 `location` 属性。 `DatabaseAccountInstanceId` 属性引用要还原的源数据库帐户的 `instanceId` 属性：

```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -Location "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>列出实时数据库帐户中数据库的 SQL 容器的所有版本

使用以下命令以列出 SQL 容器的所有版本。 此命令仅适用于实时帐户。 `DatabaseRId` 参数是要还原的数据库的 `ResourceId`。 它是在 `Get-AzCosmosdbSqlRestorableDatabase` cmdlet 的响应中找到的 `ownerResourceid` 属性的值。 响应中还包括对此数据库内的所有容器执行的操作的列表。

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"

```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>查找可在任意给定时间戳还原的数据库或容器

使用以下命令以获取可在任意给定时间戳还原的数据库或容器的列表。 此命令仅适用于实时帐户。

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

### <a name="enumerate-restorable-resources-in-api-for-mongodb"></a><a id="enumerate-mongodb-api"></a>枚举 API for MongoDB 的可还原资源

下述枚举命令可帮助你发现用于在各种时间戳上还原的资源。 此外，它们还提供可还原帐户、数据库和容器资源上的密钥事件源。 这些命令仅适用于实时帐户，与 SQL API 命令类似，但命令名称中使用 `MongoDB` 而不是 `sql`。

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>列出实时数据库帐户中的 MongoDB 数据库的所有版本

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US"

```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>列出实时数据库帐户中数据库的 MongoDB 集合的所有版本

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRId "AoQ13r==" `
  -Location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>列出可在给定时间戳和区域还原的 MongoDB 数据库帐户的所有资源

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -Location "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-an-account-using-azure-cli"></a><a id="restore-account-cli"></a>使用 Azure CLI 还原帐户

还原帐户之前，请通过以下步骤安装 Azure CLI：

1. 安装最新版本的 Azure CLI

   * 安装最新版 [Azure CLI](/cli/azure/install-azure-cli) 或高于 2.26.0 的版本
   * 如果已经安装了 CLI，请运行 `az upgrade` 命令以更新到最新版本。 此命令仅适用于高于 2.11 的 CLI 版本。 如果有较早的版本，请使用上面的链接安装最新版本。

1. 登录并选择你的订阅。

   * 使用 `az login` 来命令登录 Azure 账户。
   * 使用 `az account set -s <subscriptionguid>` 命令选择所需订阅。

### <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore-cli"></a>使用 CLI 触发还原操作

触发还原的最简单方法是发出带有目标帐户或源帐户名称、位置、资源组名称、时间戳 (UTC) 以及数据库和容器名称（二者可选）的还原命令。 以下是触发还原操作的一些示例：

1. 通过从现有帐户还原来创建新的 Azure Cosmos DB 帐户。

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. 通过从现有数据库帐户仅还原选定的数据库和容器来创建新的 Azure Cosmos DB 帐户。

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=Collection1 Collection2 \
    --databases-to-restore name=MyDB2 collections=Collection3 Collection4

   ```

### <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>枚举 SQL API 的可还原资源

下述枚举命令可帮助你发现用于在各种时间戳上还原的资源。 此外，它们还提供可还原帐户、数据库和容器资源上的密钥事件源。

#### <a name="list-all-the-accounts-that-can-be-restored-in-the-current-subscription"></a>列出可在当前订阅中还原的所有帐户

运行以下 CLI 命令可列出可在当前订阅中还原的所有帐户

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "Pitracct"
```

响应包括可还原的所有数据库帐户（实时帐户和已删除帐户）以及可从中还原的区域：

```json
{
    "accountName": "Pitracct",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

就像帐户的 `CreationTime` 或 `DeletionTime` 一样，区域也有一个 `CreationTime` 或 `DeletionTime`。 通过这些时间，你可以选择正确的区域和要还原到该区域的有效时间范围。

#### <a name="list-all-the-versions-of-databases-in-a-live-database-account"></a>列出实时数据库帐户中数据库的所有版本

通过列出数据库的所有版本，可在数据库存在的实际时间未知的情况下选择正确的数据库。

运行以下 CLI 命令以列出数据库的所有版本。 此命令仅适用于实时帐户。 `instance-id` 和 `location` 参数是从 `az cosmosdb restorable-database-account list` 命令的响应中获取的 `name` 和 `location` 属性。 instanceId 属性也是要还原的源数据库帐户的属性：

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

此命令输出现在会显示创建和删除数据库的时间。

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

#### <a name="list-all-the-versions-of-sql-containers-of-a-database-in-a-live-database-account"></a>列出实时数据库帐户中数据库的 SQL 容器的所有版本

使用以下命令以列出 SQL 容器的所有版本。 此命令仅适用于实时帐户。 `database-rid` 参数是要还原的数据库的 `ResourceId`。 它是在 `az cosmosdb sql restorable-database list` 命令的响应中找到的 `ownerResourceid` 属性的值。

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

此命令输出包括对此数据库内的所有容器执行的操作的列表：

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

#### <a name="find-databases-or-containers-that-can-be-restored-at-any-given-timestamp"></a>查找可在任意给定时间戳还原的数据库或容器

使用以下命令以获取可在任意给定时间戳还原的数据库或容器的列表。 此命令仅适用于实时帐户。

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

### <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>枚举 MongoDB API 帐户的可还原资源

下述枚举命令可帮助你发现用于在各种时间戳上还原的资源。 此外，它们还提供可还原帐户、数据库和容器资源上的密钥事件源。 这些命令仅适用于实时帐户。

#### <a name="list-all-the-versions-of-mongodb-databases-in-a-live-database-account"></a>列出实时数据库帐户中的 MongoDB 数据库的所有版本

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

#### <a name="list-all-the-versions-of-mongodb-collections-of-a-database-in-a-live-database-account"></a>列出实时数据库帐户中数据库的 MongoDB 集合的所有版本

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

#### <a name="list-all-the-resources-of-a-mongodb-database-account-that-are-available-to-restore-at-a-given-timestamp-and-region"></a>列出可在给定时间戳和区域还原的 MongoDB 数据库帐户的所有资源

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore-arm-template"></a>使用资源管理器模板进行还原

还可以使用资源管理器模板还原帐户。 定义模板时，请包含以下参数：

* 将 `createMode` 参数设置为 Restore
* 定义 `restoreParameters`，请注意，`restoreSource` 值是提取自源账户的 `az cosmosdb restorable-database-account list` 命令的输出。 帐户名称的 Instance ID 属性用于执行还原。
* 将 `restoreMode` 参数设置为 PointInTime 并配置 `restoreTimestampInUtc` 值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

接下来，使用 Azure PowerShell 或 CLI 部署模板。 下面的示例演示如何使用 CLI 命令部署模板：  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli) 或 [Azure 资源管理器](provision-account-continuous-backup.md#provision-arm-template)预配连续备份。
* [如何将帐户从定期备份迁移到连续备份](migrate-continuous-backup.md)。
* [连续备份模式资源模型](continuous-backup-restore-resource-model.md)。
* [管理](continuous-backup-restore-permissions.md)以连续备份模式还原数据所需的权限。
