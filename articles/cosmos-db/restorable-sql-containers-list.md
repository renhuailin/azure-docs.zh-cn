---
title: 使用 REST API 列出 Azure Cosmos DB 中的可还原 SQL API 容器
description: 显示在特定数据库下的所有 Azure Cosmos DB SQL 容器上完成的所有突变的事件源。 这有助于防止容器被意外删除。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 90018d3e1b793575830ba34756ad685927612006
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527339"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>使用 REST API 列出 Azure Cosmos DB 中的可还原 SQL API 容器

显示在特定数据库下的所有 Azure Cosmos DB SQL 容器上完成的所有突变的事件源。 这有助于防止容器被意外删除。 此 API 需要 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 权限

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

具有可选参数：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>URI 参数

| 名称 | 在 | 必需 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |字符串| 可还原数据库帐户的 instanceId GUID。 |
| **location** | path | True | 字符串| Azure Cosmos DB 区域中，单词与每个单词之间的空格均为大写字母。 |
| **subscriptionId** | path | True | 字符串| 目标订阅的 ID。 |
| **api 版本** | query | True | 字符串 | 要用于此操作的 API 版本。 |
| **restorableSqlDatabaseRid** | query | |字符串| SQL 数据库的资源 ID。 |

## <a name="responses"></a>响应

| 名称 | 类型 | 说明 |
| --- | --- | --- |
| 200 正常 | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| 操作已成功完成。 |
| 其他状态代码 | [DefaultErrorResponse](#defaulterrorresponse)| 描述操作失败原因的错误响应。 |

## <a name="examples"></a>示例

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**示例请求**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**示例响应**

状态代码：200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>定义

|定义 |说明 | |--- ||--- | | [容器](#container) |Azure Cosmos DB SQL 容器资源对象 | | [DefaultErrorResponse](#defaulterrorresponse) |来自服务的错误响应。 | | [ErrorResponse](#errorresponse) |错误响应。 | | [OperationType](#operationtype) |指示事件的操作类型的枚举。 | | [资源](#resource) |Azure Cosmos DB SQL 容器事件的资源 | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) |Azure Cosmos DB SQL 容器事件 | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) |Azure Cosmos DB SQL 容器事件的属性 | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) |列表操作响应，其中包含 SQL 容器事件及其属性。 |

### <a name="container"></a><a id="container"></a>容器

Azure Cosmos DB SQL 容器资源对象

| **名称**  | **类型**  | **说明**| |--- ||--- |---| |_etag | string |系统生成的属性，表示乐观并发控制所需的资源 etag。 | |_rid | string |系统生成的属性。 唯一标识符。 | |_self | string |系统生成的属性，用于指定容器资源的可寻址路径。 | |_ts | |系统生成的属性，表示资源的上次更新时间戳。 | |ID | 字符串 |Azure Cosmos DB SQL 容器的名称 |

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
| 创建 |字符串|容器创建事件|
| 删除 |字符串|容器删除事件|
| Replace |字符串|容器修改事件|
| SystemOperation |字符串|系统触发的容器修改事件。 此事件不是由用户启动|

### <a name="resource"></a><a id="resource"></a>资源

Azure Cosmos DB SQL 容器事件的资源

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| _rid |字符串| 系统生成的属性。 唯一标识符。 |
| container |[容器](#container)| Azure Cosmos DB SQL 容器资源对象 |
| eventTimestamp |字符串| 此容器事件发生的时间。 |
| operationType |[OperationType](#operationtype)| 此容器事件的操作类型。 |
| ownerId |字符串| SQL 容器的名称。 |
| ownerResourceId |字符串| SQL 容器的资源 ID。 |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Azure Cosmos DB SQL 容器事件

| **名称** | **类型** | **说明** |
| --- | --- | ---
| ID |字符串| Azure 资源管理器资源的唯一资源标识符。 |
| name |字符串| Azure 资源管理器资源的名称。 |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| SQL 容器事件的属性。 |
| 类型 |字符串| Azure 资源的类型。 |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

Azure Cosmos DB SQL 容器事件的属性

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| resource |[资源](#resource)| Azure Cosmos DB SQL 容器事件的资源 |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

列表操作响应，其中包含 SQL 容器事件及其属性。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| value |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| SQL 容器事件及其属性的列表。 |

## <a name="next-steps"></a>后续步骤

* 使用 REST API 列出 MongoDB Azure Cosmos DB API 中可恢复的[数据库](restorable-mongodb-databases-list.md)。
* 连续备份模式的[资源模型](continuous-backup-restore-resource-model.md)。