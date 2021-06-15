---
title: REST API：在多个数据库之间同步
description: 使用 REST API 示例脚本在多个数据库之间同步。
services: sql-database
ms.service: sql-database
ms.subservice: sql-data-sync
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: MaraSteiu
ms.author: masteiu
ms.reviewer: mathoma
ms.date: 03/12/2019
ms.openlocfilehash: b0e8b12c7a58f3376c4e05819dbe60c9436ecef8
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110693353"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>使用 REST API 在多个数据库之间同步数据 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

此 REST API 示例将 SQL 数据同步配置为在多个数据库之间同步数据。

有关 SQL 数据同步的概述，请参阅[使用 Azure 中的 SQL 数据同步跨多个云和本地数据库同步数据](../sql-data-sync-data-sql-server-sql-database.md)。

> [!IMPORTANT]
> 目前，SQL 数据同步不支持 Azure SQL 托管实例。

## <a name="create-sync-group"></a>创建同步组

请使用[创建或更新](/rest/api/sql/syncgroups/createorupdate)模板来创建同步组。
 
在创建同步组时，请不要传入同步架构（表\列），且不要传入 masterSyncMemberName，因为此时同步组还没有表\列信息。

创建同步组的示例请求： 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

创建同步组的示例响应：

状态代码：200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

状态代码：201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>创建同步成员

请使用[创建或更新](/rest/api/sql/syncmembers/createorupdate)模板来创建同步成员。

创建同步成员的示例请求：

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
创建同步成员的示例响应：

状态代码：200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

状态代码：201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>刷新架构

在成功创建了同步组后，请使用以下模板来刷新架构。

请使用[刷新中心架构](/rest/api/sql/syncgroups/refreshhubschema)模板来刷新中心数据库的架构。 

刷新中心数据库架构的示例请求： 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

刷新中心数据库架构的示例响应： 

状态代码：200

状态代码：202

使用[列出中心架构](/rest/api/sql/syncgroups/listhubschemas)模板来列出中心数据库架构。 

使用[刷新成员架构](/rest/api/sql/syncmembers/refreshmemberschema)模板来刷新成员数据库架构。 

使用[列出成员架构](/rest/api/sql/syncmembers/listmemberschemas)模板来列出成员数据库架构。 

请仅在成功刷新架构后才转到下一步。 

## <a name="update-sync-group"></a>更新同步组 

使用[创建或更新](/rest/api/sql/syncgroups/createorupdate)模板来更新同步组。

通过指定同步架构来更新同步组。 请包含架构和 masterSyncMemberName，后者是保留你要使用的架构的名称。 

更新同步组的示例请求： 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

更新同步组的示例响应： 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>更新同步成员

请使用[创建或更新](/rest/api/sql/syncmembers/createorupdate)模板来更新同步成员。

更新同步成员的示例请求： 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

更新同步成员的示例响应： 

状态代码：200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

状态代码：201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>触发同步

请使用[触发同步](/rest/api/sql/syncgroups/triggersync)模板来触发同步操作。

触发同步操作的示例请求： 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

触发同步操作的示例响应： 

状态代码：200

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../powershell-script-content-guide.md)中找到更多 SQL 数据库 PowerShell 脚本示例。

有关 SQL 数据同步的详细信息，请参阅：

- 概述 - [使用 Azure 中的 SQL 数据同步跨多个云和本地数据库同步数据](../sql-data-sync-data-sql-server-sql-database.md)
- 设置数据同步
    - 使用 Azure 门户 - [教程：设置 SQL 数据同步，以在 Azure SQL 数据库和 SQL Server 之间同步数据](../sql-data-sync-sql-server-configure.md)
    - 使用 PowerShell - [使用 PowerShell 在 Azure SQL 数据库中的数据库和 SQL Server 之间同步数据](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent - [Azure 中 SQL 数据同步的 Data Sync Agent](../sql-data-sync-agent-overview.md)
- 最佳做法 - [Azure 中 SQL 数据同步的最佳做法](../sql-data-sync-best-practices.md)
- 监视 - [使用 Azure Monitor 日志监视 SQL 数据同步](../monitor-tune-overview.md)
- 故障排除 - [排查 Azure 中的 SQL 数据同步问题](../sql-data-sync-troubleshoot.md)
- 更新同步架构
    - 使用 Transact-SQL - [在 Azure 中的 SQL 数据同步中自动复制架构更改](../sql-data-sync-update-sync-schema.md)
    - 使用 PowerShell - [使用 PowerShell 更新现有同步组中的同步架构](update-sync-schema-in-sync-group.md)

有关 SQL 数据库的详细信息，请参阅：

- [SQL 数据库概述](../sql-database-paas-overview.md)
- [数据库生命周期管理](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))