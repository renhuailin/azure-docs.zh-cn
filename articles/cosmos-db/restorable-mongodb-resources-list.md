---
title: 使用 REST API 列出 MongoDB Azure Cosmos DB API 中可恢复的资源
description: 返回存在于给定时间戳和位置的帐户上的数据库和集合组合框的列表。 这有助于方案验证给定时间戳和位置中存在的资源。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: b5800c2e9c1f3b36a3ac9afe6cd68f706505fbe0
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537435"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>使用 REST API 列出 MongoDB Azure Cosmos DB API 中可恢复的资源

> [!IMPORTANT]
> 时间点还原功能 (持续备份模式) 用于 Azure Cosmos DB 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

返回存在于给定时间戳和位置的帐户上的数据库和集合组合框的列表。 这有助于方案验证给定时间戳和位置中存在的资源。 此 API 需要 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 权限。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

具有可选参数：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>URI 参数

| 名称 | 在 | 必需 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |字符串| 可还原数据库帐户的 instanceId GUID。 |
| **location** | path | True | 字符串| Azure Cosmos DB 区域中，单词与每个单词之间的空格均为大写字母。 |
| **subscriptionId** | path | True | 字符串| 目标订阅的 ID。 |
| **api 版本** | query | True | 字符串 | 要用于此操作的 API 版本。 |
| **restoreLocation** | query | |字符串| 可恢复的资源所在的位置。 |
| **restoreTimestampInUtc** | query | |字符串| 可恢复资源存在时的时间戳。 |

## <a name="responses"></a>响应

| 名称 | 类型 | 说明 |
| --- | --- | --- |
| 200 正常 | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult)| 操作已成功完成。 |
| 其他状态代码 | [DefaultErrorResponse](#defaulterrorresponse)| 描述操作失败原因的错误响应。 |


## <a name="examples"></a>示例

### <a name="cosmosdbrestorablemongodbresourcelist"></a>CosmosDBRestorableMongodbResourceList

**示例请求**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**示例响应**

状态代码：200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>定义

|定义 |说明 | |--- ||--- | | [DatabaseRestoreResource](#databaserestoreresource) |要还原的特定数据库。 | | [DefaultErrorResponse](#defaulterrorresponse) |来自服务的错误响应。 | | [ErrorResponse](#errorresponse) |错误响应。 | | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult) |列表操作响应，其中包含可还原的 SQL 资源。 |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

要还原的特定数据库。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| collectionNames |string[]| 可用于还原的集合的名称。 |
| databaseName |字符串| 可用于还原的数据库的名称。 |

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

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>RestorableMongodbResourcesListResult

列表操作响应，其中包含 MongoDB 资源的可还原 Azure Cosmos DB API。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| value |[DatabaseRestoreResource](#databaserestoreresource)[]| MongoDB 资源的可恢复 Azure Cosmos DB API 列表，包括数据库名称和集合名称。 |

## <a name="next-steps"></a>后续步骤

* 使用 REST API 列出 MongoDB Azure Cosmos DB API 中可恢复的[数据库](restorable-mongodb-databases-list.md)。
* 连续备份模式的[资源模型](continuous-backup-restore-resource-model.md)。