---
title: 使用 REST API 列出 Azure Cosmos DB 中的可还原 SQL API 资源
description: 返回位于给定时间戳和位置的帐户上存在的数据库和容器组合框的列表。 这有助于方案验证给定时间戳和位置中存在的资源。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dbee87098dcc712669c332deac656cf5656ef4c4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527295"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>使用 REST API 列出 Azure Cosmos DB 中的可还原 SQL API 资源

返回位于给定时间戳和位置的帐户上存在的数据库和容器组合框的列表。 这有助于方案验证给定时间戳和位置中存在的资源。 此 API 需要 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 权限。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

具有可选参数：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
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
| 200 正常 | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| 操作已成功完成。 |
| 其他状态代码 | [DefaultErrorResponse](#defaulterrorresponse)| 描述操作失败原因的错误响应。 |

## <a name="examples"></a>示例

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**示例请求**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**示例响应**

状态代码：200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
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

|定义 |说明 | |--- ||--- | | [DatabaseRestoreResource](#databaserestoreresource) |要还原的特定数据库。 | | [DefaultErrorResponse](#defaulterrorresponse) |来自服务的错误响应。 | | [ErrorResponse](#errorresponse) |错误响应。 | | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult) |列表操作响应，其中包含可还原的 SQL 资源。 |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

列表操作响应，其中包含可还原的 SQL 资源。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| value |[DatabaseRestoreResource](#databaserestoreresource)[]| 可还原 SQL 资源的列表，包括数据库和集合名称。 |

## <a name="next-steps"></a>后续步骤

* 使用 REST API 列出 Azure Cosmos SQL API 中的可[还原数据库](restorable-sql-databases-list.md)。
* 连续备份模式的[资源模型](continuous-backup-restore-resource-model.md)。