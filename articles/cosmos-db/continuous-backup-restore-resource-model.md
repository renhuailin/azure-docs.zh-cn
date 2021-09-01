---
title: Azure Cosmos DB 时间点还原功能的资源模型。
description: 本文介绍了 Azure Cosmos DB 时间点还原功能的资源模型。 它介绍了用于支持连续备份的参数，以及可以在用于 SQL 和 MongoDB 的 Azure Cosmos DB API 帐户中还原的资源。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e4fffd12b72b41c45b2718e96c34a03e28eeca29
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733177"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature"></a>Azure Cosmos DB 时间点还原功能的资源模型
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

本文介绍了 Azure Cosmos DB 时间点还原功能的资源模型。 它介绍了用于支持连续备份的参数，以及可以在用于 SQL 和 MongoDB 的 Azure Cosmos DB API 帐户中还原的资源。

## <a name="database-accounts-resource-model"></a>数据库帐户的资源模型

数据库帐户的资源模型已更新，现有一些额外的属性来支持新的还原方案。 这些属性是 BackupPolicy、CreateMode 和 RestoreParameters。

### <a name="backuppolicy"></a>BackupPolicy

帐户级别备份策略中 `backuppolicy` 参数下一个名为 `Type` 的新属性，可启用连续备份和时间点还原功能。 此模式称为“连续备份”。 可以在创建帐户时或在[将帐户从定期模式迁移到连续模式](migrate-continuous-backup.md)时设置此模式。 启用连续模式后，在此帐户中创建的所有容器和数据库都将默认启用连续备份和时间点还原功能。

> [!NOTE]
> 时间点还原功能现适用于 Azure Cosmos DB API for MongoDB 和 SQL 帐户。 使用连续模式创建帐户后，无法将其切换为定期模式。

### <a name="createmode"></a>CreateMode

此属性指示帐户是如何创建的。 可能的值为 Default 和 Restore 。 若要执行还原，请将此值设置为“Restore”，并在 `RestoreParameters` 属性中提供适当的值。

### <a name="restoreparameters"></a>RestoreParameters

`RestoreParameters` 资源包含还原操作的详细信息，包括帐户 ID、还原时间和需要还原的资源。

|属性名称 |说明  |
|---------|---------|
|restoreMode  | 还原模式应为 PointInTime |
|restoreSource   |  将从中启动还原的源帐户的 instanceId。       |
|restoreTimestampInUtc  | 应将帐户还原到的时间点 (UTC)。 |
|databasesToRestore   | `DatabaseRestoreResource` 对象的列表，用于指定应还原的数据库和容器。 每个资源都表示单个数据库以及该数据库下的所有集合，有关更多信息，请参阅[可还原 SQL 资源](#restorable-sql-resources)部分。 如果此值为空，则还原整个帐户。   |

### <a name="sample-resource"></a>示例资源

以下 JSON 是启用了连续备份的示例数据库帐户资源：

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    }
}
```

## <a name="restorable-resources"></a>可还原的资源

提供了一组新的资源和 API，可帮助你发现资源的关键信息、可还原的内容、可从中还原的位置，以及对这些资源执行关键操作的时间戳。

> [!NOTE]
> 用于枚举这些资源的 API 都需要以下权限：
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>可还原的数据库帐户

此资源包含可还原的数据库帐户实例。 数据库帐户可以是已删除的帐户，也可以是实时帐户。 你可使用它包含的信息找到想要还原的源数据库帐户。

|属性名称 |说明  |
|---------|---------|
| ID | 资源的唯一标识符。 |
| accountName | 全局数据库帐户名称。 |
| creationTime | 创建或迁移帐户的时间 (UTC)。  |
| deletionTime | 删除帐户的时间 (UTC)。  如果帐户是实时的，则此值为空。 |
| apiType | Azure Cosmos DB 帐户的 API 类型。 |
| restorableLocations | 帐户所在的位置列表。 |
| restorableLocations: locationName | 区域帐户的区域名称。 |
| restorableLocations: regionalDatabaseAccountInstanceId | 区域帐户的 GUID。 |
| restorableLocations: creationTime | 创建或迁移区域帐户的时间 (UTC)。|
| restorableLocations: deletionTime | 删除区域帐户的时间 (UTC)。 如果区域帐户是实时的，则此值为空。|

若要获取所有可还原帐户的列表，请查看[可还原的数据库帐户 - 列表](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-database-accounts/list)或[可还原的数据库帐户 - 按位置列出](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-database-accounts/list-by-location)文章。

### <a name="restorable-sql-database"></a>可还原的 SQL 数据库

每个资源都包含突变事件的相关信息，例如在 SQL 数据库上发生的创建和删除事件。 如果意外删除了数据库，并且需要查明事件发生的时间，则此信息可能会有所帮助。

|属性名称 |说明  |
|---------|---------|
| eventTimestamp | 创建或删除数据库的时间 (UTC)。 |
| ownerId | SQL 数据库的名称。 |
| ownerResourceId | SQL 数据库的资源 ID|
| operationType | 此数据库事件的操作类型。 下面是可能的值：<br/><ul><li>Create：数据库创建事件</li><li>Delete：数据库删除事件</li><li>Replace：数据库修改事件</li><li>SystemOperation：系统触发的数据库修改事件。 此事件并非由用户发起</li></ul> |
| database |事件发生时 SQL 数据库的属性|

若要获取所有数据库突变的列表，请查看[可还原的 SQL 数据库 - 列表](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-databases/list)一文。

### <a name="restorable-sql-container"></a>可还原的 SQL 容器

每个资源都包含突变事件的相关信息，例如在 SQL 容器上发生的创建和删除事件。 如果修改或删除了容器，并且需要查明事件发生的时间，则此信息可能会有所帮助。

|属性名称 |说明  |
|---------|---------|
| eventTimestamp    | 此容器事件发生的时间 (UTC)。|
| ownerId| SQL 容器的名称。|
| ownerResourceId   | SQL 容器的资源 ID。|
| operationType | 此容器事件的操作类型。 下面是可能的值： <br/><ul><li>Create：容器创建事件</li><li>Delete：容器删除事件</li><li>Replace：容器修改事件</li><li>SystemOperation：系统触发的容器修改事件。 此事件并非由用户发起</li></ul> |
| container | 事件发生时 SQL 容器的属性。|

若要获取同一数据库下所有容器突变的列表，请查看[可还原的 SQL 容器 - 列表](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-containers/list)一文。

### <a name="restorable-sql-resources"></a>可还原的 SQL 资源

每个资源都表示单一数据库及其包含的所有容器。

|属性名称 |说明  |
|---------|---------|
| databaseName  | SQL 数据库的名称。
| collectionNames   | 此数据库下的 SQL 容器列表。|

若要获取在给定时间戳和位置的帐户上存在的 SQL 数据库和容器组合的列表，请查看[可还原的 SQL 资源 - 列表](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-sql-resources/list)一文。

### <a name="restorable-mongodb-database"></a>可还原的 MongoDB 数据库

每个资源都包含突变事件的相关信息，例如在 MongoDB 数据库上发生的创建和删除事件。 如果意外删除了数据库，并且用户需要查明事件发生的时间，则此信息可能会有所帮助。

|属性名称 |说明  |
|---------|---------|
|eventTimestamp| 此数据库事件发生的时间 (UTC)。|
| ownerId| MongoDB 数据库的名称。 |
| ownerResourceId   | MongoDB 数据库的资源 ID。 |
| operationType |   此数据库事件的操作类型。 下面是可能的值：<br/><ul><li> Create：数据库创建事件</li><li> Delete：数据库删除事件</li><li> Replace：数据库修改事件</li><li> SystemOperation：系统触发的数据库修改事件。 此事件并非由用户发起 </li></ul> |

若要获取所有数据库突变的列表，请查看[可还原的 Mongodb 数据库 - 列表](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-databases/list)一文。

### <a name="restorable-mongodb-collection"></a>可还原的 MongoDB 集合

每个资源都包含突变事件的相关信息，例如在 MongoDB 集合上发生的创建和删除事件。 如果修改或删除了集合，并且用户需要查明事件发生的时间，则此信息可能会有所帮助。

|属性名称 |说明  |
|---------|---------|
| eventTimestamp |此集合事件发生的时间 (UTC)。 |
| ownerId| MongoDB 集合的名称。 |
| ownerResourceId   | MongoDB 集合的资源 ID。 |
| operationType |此集合事件的操作类型。 下面是可能的值：<br/><ul><li>Create：集合创建事件</li><li>Delete：集合删除事件</li><li>Replace：集合修改事件</li><li>SystemOperation：系统触发的集合修改事件。 此事件并非由用户发起</li></ul> |

若要获取同一数据库下所有容器突变的列表，请查看[可还原的 Mongodb 集合 - 列表](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-collections/list)一文。

### <a name="restorable-mongodb-resources"></a>可还原的 MongoDB 资源

每个资源都表示单一数据库及其包含的所有集合。

|属性名称 |说明  |
|---------|---------|
| databaseName  |MongoDB 数据库的名称。 |
| collectionNames | 此数据库下的 MongoDB 集合列表。 |

若要获取在给定时间戳和位置的帐户上存在的所有 MongoDB 数据库和集合组合的列表，请查看[可还原的 Mongodb 资源 - 列表](/rest/api/cosmos-db-resource-provider/2021-04-01-preview/restorable-mongodb-resources/list)一文。

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli) 或 [Azure 资源管理器](provision-account-continuous-backup.md#provision-arm-template)预配连续备份。
* 使用 [Azure 门户](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli) 或 [Azure 资源管理器](restore-account-continuous-backup.md#restore-arm-template)还原帐户。
* [将帐户从定期备份迁移到连续备份](migrate-continuous-backup.md)。
* [管理](continuous-backup-restore-permissions.md)以连续备份模式还原数据所需的权限。
