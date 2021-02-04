---
title: 使用 REST API 列出适用于 MongoDB Azure Cosmos DB API 中的可还原数据库
description: 显示在可恢复帐户下的所有 Azure Cosmos DB MongoDB 数据库上完成的所有突变的事件源。 这在数据库被意外删除以获取删除时间的情况下非常有用。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 5ebb0a23822074f61a16bf1d7652dba589399542
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537504"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>使用 REST API 列出适用于 MongoDB Azure Cosmos DB API 中的可还原数据库

> [!IMPORTANT]
> 时间点还原功能 (持续备份模式) 用于 Azure Cosmos DB 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

显示在可恢复帐户下的所有 Azure Cosmos DB MongoDB 数据库上完成的所有突变的事件源。 这在数据库被意外删除以获取删除时间的情况下非常有用。 此 API 需要 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 权限

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
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
| 200 正常 | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| 操作已成功完成。 |
| 其他状态代码 | [DefaultErrorResponse](#defaulterrorresponse)| 描述操作失败原因的错误响应。|

## <a name="examples"></a>示例

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**示例请求**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**示例响应**

状态代码：200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>定义

|定义 |说明 | |--- ||--- | | [DefaultErrorResponse](#defaulterrorresponse) |来自服务的错误响应。 | | [ErrorResponse](#errorresponse) |错误响应。 | | [OperationType](#operationtype) |指示事件的操作类型的枚举。 | | [资源](#resource) |MongoDB 数据库事件的 Azure Cosmos DB API 的资源 | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) |MongoDB 数据库事件的 Azure Cosmos DB API | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) |MongoDB 数据库事件的 Azure Cosmos DB API 的属性 | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) |列出操作响应，其中包含 MongoDB 数据库事件的 Azure Cosmos DB API 及其属性。 |

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

### <a name="resource"></a><a id="resource"></a>资源

用于 MongoDB 数据库事件的 Azure Cosmos DB API 的资源

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| _rid |字符串| 系统生成的属性。 唯一标识符。 |
| eventTimestamp |字符串| 此数据库事件发生的时间。 |
| operationType |[OperationType](#operationtype)| 此数据库事件的操作类型。  |
| ownerId |字符串| MongoDB 数据库的 Azure Cosmos DB API 的名称。|
| ownerResourceId |字符串| MongoDB 数据库的资源 ID Azure Cosmos DB API。 |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

用于 MongoDB 数据库事件的 Azure Cosmos DB API

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| ID |字符串| Azure 资源管理器资源的唯一资源标识符。 |
| name |字符串| 资源管理器资源的名称。 |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| 用于 MongoDB 数据库事件的 Azure Cosmos DB API 的属性。 |
| 类型 |字符串| Azure 资源的类型。 |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

用于 MongoDB 数据库事件的 Azure Cosmos DB API 的属性

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| resource |[资源](#resource)| 用于 MongoDB 数据库事件的 Azure Cosmos DB API 的资源 |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

列表操作响应，其中包含数据库事件及其属性。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| value |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| 数据库事件及其属性的列表。 |

## <a name="next-steps"></a>后续步骤

* 使用 REST API 列出 MongoDB Azure Cosmos DB API 中的可恢复[资源](restorable-mongodb-resources-list.md)。
* 使用 REST API 列出 MongoDB Azure Cosmos DB API 中的可[还原集合](restorable-mongodb-collections-list.md)。
* 连续备份模式的[资源模型](continuous-backup-restore-resource-model.md)。