---
title: 使用 Azure CLI 在 Azure Cosmos DB 中配置连续备份和时间点还原。
description: 了解如何使用 Azure CLI 为帐户预配连续备份和还原数据。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377323"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>使用 Azure CLI 配置和管理连续备份和时间点还原（预览版）
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB 的时间点还原功能（连续备份模式）目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 的时间点还原功能（预览版）可帮助你从容器内的意外更改中恢复，还原已删除的帐户、数据库或容器，或者还原到任何区域（需存在备份）。 通过连续备份模式，可还原到过去 30 天内的任何时间点。

本文介绍如何使用 Azure CLI 为帐户预配连续备份和还原数据。

## <a name="install-azure-cli"></a><a id="install"></a>安装 Azure CLI

1. 安装最新版本的 Azure CLI

   * 安装最新版本的 [Azure CLI](/cli/azure/install-azure-cli) 或高于2.17.1 的版本。
   * 如果已经安装了 CLI，请运行 `az upgrade` 命令以更新到最新版本。 此命令仅适用于高于 2.11 的 CLI 版本。 如果有较早的版本，请使用上面的链接安装最新版本。

1. 安装 `cosmosdb-preview` CLI 扩展。

   * 时间点还原命令在 `cosmosdb-preview` 扩展下可用。
   * 可以通过运行以下命令来安装此扩展：`az extension add --name cosmosdb-preview`
   * 可以通过运行以下命令来卸载此扩展：`az extension remove --name cosmosdb-preview`

1. 登录并选择你的订阅。

   * 使用 `az login` 来命令登录 Azure 账户。
   * 使用 `az account set -s <subscriptionguid>` 命令选择所需订阅。

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>为 SQL API 帐户预配连续备份

若要为 SQL API 帐户预配连续备份，应将额外的参数 `--backup-policy-type Continuous` 与常规预配命令一起传递。 以下命令是一个名为 `pitracct2` 的单个区域写入帐户的示例，其连续备份策略在“美国西部”区域中的“myrg”资源组下创建 ：

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>为 Azure Cosmos DB API for MongoDB 帐户预配连续备份

以下命令显示一个名为 `pitracct3` 的单个区域写入帐户的示例，其连续备份策略在“美国西部”区域中的“myrg”资源组下创建 ：

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>使用 CLI 触发还原操作

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
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>枚举 SQL API 的可还原资源

下述枚举命令可帮助你发现用于在各种时间戳上还原的资源。 此外，它们还提供可还原帐户、数据库和容器资源上的密钥事件源。

列出可在当前订阅中还原的所有帐户

运行以下 CLI 命令可列出可在当前订阅中还原的所有帐户

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

响应包括可还原的所有数据库帐户（实时帐户和已删除帐户）以及可从中还原的区域：

```json
{
    "accountName": "pitrbb",
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

**列出实时数据库帐户中数据库的所有版本**

通过列出数据库的所有版本，可在数据库存在的实际时间未知的情况下选择正确的数据库。

运行以下 CLI 命令以列出数据库的所有版本。 此命令仅适用于实时帐户。 `instanceId` 和 `location` 参数是从 `az cosmosdb restorable-database-account list` 命令的响应中获取的 `name` 和 `location` 属性。 instanceId 属性也是要还原的源数据库帐户的属性：

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

**列出实时数据库帐户中数据库的 SQL 容器的所有版本**

使用以下命令以列出 SQL 容器的所有版本。 此命令仅适用于实时帐户。 `databaseRid` 参数是要还原的数据库的 `ResourceId`。 它是在 `az cosmosdb sql restorable-database list` 命令的响应中找到的 `ownerResourceid` 属性的值。

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

查找可在任意给定时间戳还原的数据库或容器

使用以下命令以获取可在任意给定时间戳还原的数据库或容器的列表。 此命令仅适用于实时帐户。

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

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

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>枚举 MongoDB API 帐户的可还原资源

下述枚举命令可帮助你发现用于在各种时间戳上还原的资源。 此外，它们还提供可还原帐户、数据库和容器资源上的密钥事件源。 与使用 SQL API 一样，可以使用 `az cosmosdb` 命令，但要用 `mongodb` 作为参数而不是 `sql`。 这些命令仅适用于实时帐户。

列出实时数据库帐户中的 MongoDB 数据库的所有版本

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

列出实时数据库帐户中数据库的 MongoDB 集合的所有版本

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

列出可在给定时间戳和区域还原的 MongoDB 数据库帐户的所有资源

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](continuous-backup-restore-portal.md)、[PowerShell](continuous-backup-restore-powershell.md) 或 [Azure 资源管理器](continuous-backup-restore-template.md)配置和管理连续备份。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
* [管理](continuous-backup-restore-permissions.md)以连续备份模式还原数据所需的权限。
