---
title: Azure Cosmos DB 时点还原功能的资源模型。
description: 本文介绍 Azure Cosmos DB 时点还原功能的资源模型。 它介绍了一些参数，这些参数支持可以在 SQL 和 MongoDB 帐户 Azure Cosmos DB API 中还原的连续备份和资源。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d838425583638aef5199b52df4869923c826553d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369962"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Azure Cosmos DB 时间点还原功能的资源模型 (预览版) 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> 时间点还原功能 (持续备份模式) 用于 Azure Cosmos DB 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文说明 Azure Cosmos DB 时点还原功能 (预览版) 的资源模型。 它介绍了一些参数，这些参数支持可以在 SQL 和 MongoDB 帐户 Azure Cosmos DB API 中还原的连续备份和资源。

## <a name="database-accounts-resource-model"></a>数据库帐户的资源模型

使用几个额外的属性更新数据库帐户的资源模型，以支持新的还原方案。 这些属性为 **BackupPolicy、CreateMode 和 RestoreParameters。**

### <a name="backuppolicy"></a>BackupPolicy

帐户级别备份策略中名为 `Type` "参数" 的新属性 `backuppolicy` 启用连续备份和时间点还原功能。 此模式称为 **连续备份**。 在公共预览版中，仅可在创建帐户时设置此模式。 启用后，在此帐户中创建的所有容器和数据库都将具有默认启用的连续备份和时间点还原功能。

> [!NOTE]
> 目前，时间点还原功能处于公共预览阶段，适用于 MongoDB Azure Cosmos DB API 和 SQL 帐户。 使用连续模式创建帐户后，不能将其切换为定期模式。

### <a name="createmode"></a>CreateMode

此属性指示如何创建帐户。 可能的值为 *Default* 和 *Restore*。 若要执行还原，请将此值设置为 *restore* ，并在属性中提供相应的值 `RestoreParameters` 。

### <a name="restoreparameters"></a>RestoreParameters

`RestoreParameters`资源包含还原操作的详细信息，包括帐户 ID、还原时间和需要还原的资源。

|属性名称 |说明  |
|---------|---------|
|restoreMode  | 还原模式应为 *PointInTime* |
|restoreSource   |  从中启动还原的源帐户的 instanceId。       |
|restoreTimestampInUtc  | 应将帐户还原到的时间点（UTC）。 |
|databasesToRestore   | 对象的列表 `DatabaseRestoreSource` ，用于指定应还原的数据库和容器。 如果此值为空，则还原整个帐户。   |

**DatabaseRestoreResource** -每个资源都表示一个数据库和该数据库下的所有集合。

|属性名称 |说明  |
|---------|---------|
|databaseName | 数据库的名称 |
| collectionNames| 此数据库下的容器列表 |

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
    },
}
}
```

## <a name="restorable-resources"></a>可恢复资源

提供了一组新的资源和 Api，可帮助你发现有关资源的关键信息，这些信息可以还原、可以还原的位置以及对这些资源执行关键操作的时间戳。

> [!NOTE]
> 用于枚举这些资源的所有 API 都需要以下权限：
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>可还原数据库帐户

此资源包含可还原的数据库帐户实例。 数据库帐户可以是已删除的帐户，也可以是 live 帐户。 它包含允许您查找要还原的源数据库帐户的信息。

|属性名称 |说明  |
|---------|---------|
| ID | 资源的唯一标识符。 |
| accountName | 全局数据库帐户名称。 |
| creationTime | 创建帐户时的 UTC 时间。  |
| deletionTime | 删除帐户的时间（UTC）。  如果帐户是活动的，则此值为空。 |
| apiType | Azure Cosmos DB 帐户的 API 类型。 |
| restorableLocations | 帐户存在的位置的列表。 |
| restorableLocations: locationName | 区域帐户的区域名称。 |
| restorableLocations: regionalDatabaseAccountInstanceI | 区域帐户的 GUID。 |
| restorableLocations： creationTime | 创建区域帐户时的 UTC 时间。|
| restorableLocations: deletionTime | 删除区域帐户时的 UTC 时间。 如果区域帐户是活动的，则此值为空。|

若要获取所有可恢复帐户的列表，请参阅可 [还原数据库帐户-列表](restorable-database-accounts-list.md) 或可 [还原的数据库帐户-按位置列出](restorable-database-accounts-list-by-location.md) 的文章。

### <a name="restorable-sql-database"></a>可还原的 SQL 数据库

每个资源都包含发生变化事件的信息，例如，在 SQL 数据库上发生的创建和删除。 如果意外删除了数据库，并且需要查明事件发生的时间，则此信息可能会有所帮助。

|属性名称 |说明  |
|---------|---------|
| eventTimestamp | 创建或删除数据库时的 UTC 时间。 |
| ownerId | SQL 数据库的名称。 |
| ownerResourceId | SQL 数据库的资源 ID|
| operationType | 此数据库事件的操作类型。 下面是可能的值：<br/><ul><li>创建：数据库创建事件</li><li>删除：数据库删除事件</li><li>Replace：数据库修改事件</li><li>SystemOperation：由系统触发的数据库修改事件。 此事件不是由用户启动</li></ul> |
| database |事件发生时的 SQL 数据库属性|

若要获取所有数据库突变的列表，请参阅可恢复 [的 Sql 数据库-列表一](restorable-sql-databases-list.md) 文。

### <a name="restorable-sql-container"></a>可还原 SQL 容器

每个资源都包含发生变化事件的信息，例如，在 SQL 容器上发生的创建和删除。 此信息可帮助在修改或删除容器的情况下，以及是否需要查明事件发生的时间。

|属性名称 |说明  |
|---------|---------|
| eventTimestamp    | 此容器事件发生时的 UTC 时间。|
| ownerId| SQL 容器的名称。|
| ownerResourceId   | SQL 容器的资源 ID。|
| operationType | 此容器事件的操作类型。 下面是可能的值： <br/><ul><li>创建：容器创建事件</li><li>删除：容器删除事件</li><li>Replace：容器修改事件</li><li>SystemOperation：由系统触发的容器修改事件。 此事件不是由用户启动</li></ul> |
| container | 事件发生时的 SQL 容器属性。|

若要获取同一数据库下所有容器突变的列表，请参阅可恢复的 [Sql 容器-列表一](restorable-sql-containers-list.md) 文。

### <a name="restorable-sql-resources"></a>可还原的 SQL 资源

每个资源都表示一个数据库和该数据库下的所有容器。

|属性名称 |说明  |
|---------|---------|
| databaseName  | SQL 数据库的名称。
| collectionNames   | 此数据库下的 SQL 容器列表。|

若要获取位于给定时间戳和位置的帐户上的 SQL 数据库和容器组合列表，请参阅可恢复的 [Sql 资源-列表一](restorable-sql-resources-list.md) 文。

### <a name="restorable-mongodb-database"></a>可还原的 MongoDB 数据库

每个资源都包含发生变化事件的信息，例如在 MongoDB 数据库上发生的创建和删除。 此信息可帮助防止意外删除数据库，用户需要了解该事件发生的时间。

|属性名称 |说明  |
|---------|---------|
|eventTimestamp| 此数据库事件发生时的 UTC 时间。|
| ownerId| MongoDB 数据库的名称。 |
| ownerResourceId   | MongoDB 数据库的资源 ID。 |
| operationType |   此数据库事件的操作类型。 下面是可能的值：<br/><ul><li> 创建：数据库创建事件</li><li> 删除：数据库删除事件</li><li> Replace：数据库修改事件</li><li> SystemOperation：由系统触发的数据库修改事件。 此事件不是由用户启动 </li></ul> |

若要获取所有数据库变化的列表，请参阅可恢复 [的 Mongodb 数据库-列表](restorable-mongodb-databases-list.md) 一文。

### <a name="restorable-mongodb-collection"></a>可还原的 MongoDB 集合

每个资源都包含发生变化事件的信息，例如在 MongoDB 集合上发生的创建和删除。 此信息可帮助在修改或删除集合的情况下，用户需要了解该事件发生的时间。

|属性名称 |说明  |
|---------|---------|
| eventTimestamp |此收集事件发生时的 UTC 时间。 |
| ownerId| MongoDB 集合的名称。 |
| ownerResourceId   | MongoDB 集合的资源 ID。 |
| operationType |此集合事件的操作类型。 下面是可能的值：<br/><ul><li>创建：集合创建事件</li><li>删除：收集删除事件</li><li>Replace：集合修改事件</li><li>SystemOperation：由系统触发的集合修改事件。 此事件不是由用户启动</li></ul> |

若要获取同一数据库下所有容器突变的列表，请参阅可恢复 [的 Mongodb 集合-列表](restorable-mongodb-collections-list.md) 一文。

### <a name="restorable-mongodb-resources"></a>可恢复的 MongoDB 资源

每个资源都表示一个数据库和该数据库下的所有集合。

|属性名称 |说明  |
|---------|---------|
| databaseName  |MongoDB 数据库的名称。 |
| collectionNames | 此数据库下的 MongoDB 集合列表。 |

若要获取在给定时间戳和位置帐户上存在的所有 MongoDB 数据库和集合组合的列表，请参阅可恢复的 [Mongodb 资源-列表一](restorable-mongodb-resources-list.md) 文。

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](continuous-backup-restore-portal.md)、 [PowerShell](continuous-backup-restore-powershell.md)、 [CLI](continuous-backup-restore-command-line.md)或 [Azure 资源管理器](continuous-backup-restore-template.md)配置和管理连续备份。
* [管理](continuous-backup-restore-permissions.md) 以连续备份模式还原数据所需的权限。
