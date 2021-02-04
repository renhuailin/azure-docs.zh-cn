---
title: 列出 Azure Cosmos DB MongoDB API 中的可还原集合-REST API
description: 显示在特定数据库下的所有 Azure Cosmos DB MongoDB 集合上完成的所有突变的事件源。 这有助于防止容器被意外删除。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: c19de134f40c58a687dcf6bac6ac156e46cef7da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537537"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>使用 REST API 在 MongoDB Azure Cosmos DB API 中列出可恢复的集合

> [!IMPORTANT]
> 时间点还原功能 (持续备份模式) 用于 Azure Cosmos DB 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

显示特定数据库下 MongoDB 集合的所有 Azure Cosmos DB API 上完成的所有突变的事件源。 这有助于防止容器被意外删除。 此 API 需要 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 权限

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

具有可选参数：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>URI 参数

| 名称 | 在 | 必需 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |字符串| 可还原数据库帐户的 instanceId GUID。 |
| **location** | path | True | 字符串| Azure Cosmos DB 区域中，单词与每个单词之间的空格均为大写字母。 |
| **subscriptionId** | path | True | 字符串| 目标订阅的 ID。 |
| **api 版本** | query | True | 字符串 | 要用于此操作的 API 版本。 |
| **restorableMongodbDatabaseRid** | query | |字符串| MongoDB 数据库的 Azure Cosmos DB API 的资源 ID。 |

## <a name="responses"></a>响应

| 名称 | 类型 | 说明 |
| --- | --- | --- |
| 200 正常 | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| 操作已成功完成。 |
| 其他状态代码 | [DefaultErrorResponse](#defaulterrorresponse)| 描述操作失败原因的错误响应。|

## <a name="examples"></a>示例

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**示例请求**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**示例响应**

状态代码：200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>定义

|定义  | 说明|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | 来自服务的错误响应。 |
| [ErrorResponse](#errorresponse) | 错误响应。 |
| [OperationType](#operationtype) | 指示事件的操作类型的枚举。 |
| [资源](#resource) | MongoDB 收集事件的 Azure Cosmos DB API 的资源 |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | MongoDB 收集事件的 Azure Cosmos DB API |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | MongoDB 收集事件的 Azure Cosmos DB API 的属性 |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | 列表操作响应，其中包含集合事件及其属性。 |

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
| 创建 |字符串|集合创建事件|
| 删除 |字符串|集合删除事件|
| Replace |字符串|集合修改事件|

### <a name="resource"></a><a id="resource"></a>资源

Azure Cosmos DB MongoDB 集合事件的资源

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| _rid |字符串| 系统生成的属性。 唯一标识符。 |
| eventTimestamp |字符串| 此收集事件发生的时间。 |
| operationType |[OperationType](#operationtype)|  此集合事件的操作类型。 |
| ownerId |字符串| MongoDB 集合的名称。|
| ownerResourceId |字符串| MongoDB 集合的资源 ID。 |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Azure Cosmos DB MongoDB 收集事件

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| ID |字符串| Azure 资源管理器资源的唯一资源标识符。 |
| name |字符串| 资源管理器资源的名称。 |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| 集合事件的属性。 |
| 类型 |字符串| Azure 资源的类型。 |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

Azure Cosmos DB MongoDB 集合事件的属性

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| resource | [资源](#resource)| MongoDB 收集事件的 Azure Cosmos DB API 的资源 |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

列表操作响应，其中包含集合事件及其属性。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| value |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| MongoDB 收集事件及其属性的 Azure Cosmos DB API 列表。 |

## <a name="next-steps"></a>后续步骤

* 使用 REST API 列出 MongoDB Azure Cosmos DB API 中可恢复的[数据库](restorable-mongodb-databases-list.md)。
* 连续备份模式的[资源模型](continuous-backup-restore-resource-model.md)。