---
title: 使用 REST API 列出 Azure Cosmos DB 中的可还原 SQL API 数据库
description: 显示在可恢复帐户下的所有 Azure Cosmos DB SQL 数据库上完成的所有突变的事件源。 这在数据库被意外删除以获取删除时间的情况下非常有用。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: d3d72cff5fcfeed17d60e2f856be4adc1a983819
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527296"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>使用 REST API 列出 Azure Cosmos DB 中的可还原 SQL API 数据库

显示在可恢复帐户下的所有 Azure Cosmos DB SQL 数据库上完成的所有突变的事件源。 这在数据库被意外删除以获取删除时间的情况下非常有用。 此 API 需要 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 权限

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI 参数

| 名称 | 在 | 必需 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |字符串| 可还原数据库帐户的 instanceId GUID。 |
| **location** | path | True | 字符串| Azure Cosmos DB 区域中，单词与每个单词之间的空格均为大写字母。 |
| **subscriptionId** | path | True | 字符串| 目标订阅的 ID。 |
| **api 版本** | query | True | 字符串 | 要用于此操作的 API 版本。 |

## <a name="responses"></a>响应

| 名称 | 类型 | 说明 |
| --- | --- | --- |
| 200 正常 | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| 操作已成功完成。 |
| 其他状态代码 | [DefaultErrorResponse](#defaulterrorresponse)| 描述操作失败原因的错误响应。 |

## <a name="examples"></a>示例

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**示例请求**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**示例响应**

状态代码：200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>定义

|定义 |说明 | |--- ||--- | | [数据库](#database) |Azure Cosmos DB SQL 数据库资源对象 | | [DefaultErrorResponse](#defaulterrorresponse) |来自服务的错误响应。 | | [ErrorResponse](#errorresponse) |错误响应。 | | [OperationType](#operationtype) |指示事件的操作类型的枚举。 | | [资源](#resource) |Azure Cosmos DB SQL 数据库事件的资源 | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) |Azure Cosmos DB SQL 数据库事件 | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) |Azure Cosmos DB SQL 数据库事件的属性 | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) |列表操作响应，其中包含 SQL 数据库事件及其属性。 |

### <a name="database"></a><a id="database"></a>数据

Azure Cosmos DB SQL 数据库资源对象

| **名称**  | **类型**  | **说明**| |--- ||--- |---| |_colls | string |系统生成的属性，指定集合资源的可寻址路径。 | |_etag | string |系统生成的属性，表示乐观并发控制所需的资源 etag。 | |_rid | string |系统生成的属性。 唯一标识符。 | |_self | string |系统生成的属性，用于指定数据库资源的可寻址路径。 | |_ts | |系统生成的属性，表示资源的上次更新时间戳。 | |_users | string |系统生成的属性，指定用户资源的可寻址路径。 | |ID | 字符串 |Azure Cosmos DB SQL database 的名称 |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

来自服务的错误响应。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| 错误 | [ErrorResponse](#errorresponse)| 错误响应。 |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

错误响应。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| code |字符串| 错误代码。 |
| message |字符串| 指示操作失败的原因的错误消息。 |

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

指示事件的操作类型的枚举。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| 创建 |字符串|数据库创建事件|
| 删除 |字符串|数据库删除事件|
| Replace |字符串|数据库修改事件|
| SystemOperation |字符串|系统触发的数据库修改事件。 此事件不是由用户启动|

### <a name="resource"></a><a id="resource"></a>资源

Azure Cosmos DB SQL 数据库事件的资源

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| _rid |字符串| 系统生成的属性。 唯一标识符。 |
| database |[数据库](#database)| Azure Cosmos DB SQL 数据库资源对象 |
| eventTimestamp |字符串| 此数据库事件发生的时间。 |
| operationType |[OperationType](#operationtype)| 此数据库事件的操作类型。 |
| ownerId |字符串| SQL 数据库的名称。 |
| ownerResourceId |字符串| SQL 数据库的资源 ID。 |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Azure Cosmos DB SQL 数据库事件

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| ID |字符串| Azure 资源管理器资源的唯一资源标识符。 |
| name |字符串| Azure 资源管理器资源的名称。 |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| SQL 数据库事件的属性。 |
| 类型 |字符串| Azure 资源的类型。 |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

Azure Cosmos DB SQL 数据库事件的属性

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| resource |[资源](#resource)| Azure Cosmos DB SQL 数据库事件的资源 |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

列表操作响应，其中包含 SQL 数据库事件及其属性。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| value |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| SQL 数据库事件及其属性的列表。 |

## <a name="next-steps"></a>后续步骤

* 使用 REST API 列出 Azure Cosmos DB SQL API 中可[还原的容器](restorable-sql-containers-list.md)。
* 连续备份模式的[资源模型](continuous-backup-restore-resource-model.md)。