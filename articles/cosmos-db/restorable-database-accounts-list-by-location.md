---
title: 使用 Azure Cosmos DB REST API 按位置列出可还原数据库帐户
description: 列出订阅和区域中可用的所有可恢复 Azure Cosmos DB 数据库帐户。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 26c07ddaf4db71961d4aeff50e482740d969f0ef
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537672"
---
# <a name="list-restorable-database-accounts-by-location-using-azure-cosmos-db-rest-api"></a>使用 Azure Cosmos DB REST API 按位置列出可还原数据库帐户

> [!IMPORTANT]
> 时间点还原功能 (持续备份模式) 用于 Azure Cosmos DB 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

列出订阅和区域中可用的所有可恢复 Azure Cosmos DB 可还原数据库帐户。 此调用需要 `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` 权限。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts?api-version=2020-06-01-preview

```

## <a name="uri-parameters"></a>URI 参数

| 名称 | 在 | 必需 | 类型 | 说明 |
| --- | --- | --- | --- | --- |
| **location** | path | True | 字符串| Azure Cosmos DB 区域中，单词与每个单词之间的空格均为大写字母。 |
| **subscriptionId** | path | True | 字符串| 目标订阅的 ID。 |
| **api 版本** | query | True | 字符串 | 要用于此操作的 API 版本。 |

## <a name="responses"></a>响应

| 名称 | 类型 | 说明 |
| --- | --- | --- |
| 200 正常 | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| 操作已成功完成。 |
| 其他状态代码 | [DefaultErrorResponse](#defaulterrorresponse)| 描述操作失败原因的错误响应。 |

## <a name="examples"></a>示例

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**示例请求**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**示例响应**

状态代码：200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>定义

|定义  | 说明|
| --- | --- |
| [ApiType](#apitype) | 枚举，用于指示可还原数据库帐户的 API 类型。 |
| [DefaultErrorResponse](#defaulterrorresponse) | 来自服务的错误响应。 |
| [ErrorResponse](#errorresponse) | 错误响应。 |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Azure Cosmos DB 可还原的数据库帐户。 |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | 列表操作响应，其中包含可还原的数据库帐户及其属性。 |
| [RestorableLocationResource](#restorablelocationresource) | 区域可还原帐户的属性。 |

### <a name="apitype"></a><a id="apitype"></a>ApiType

枚举，用于指示可还原数据库帐户的 API 类型。

| **名称** | **类型** |
| --- | --- |
| Cassandra |字符串|
| Gremlin |字符串|
| GremlinV2 |字符串|
| MongoDB |字符串|
| Sql |字符串|
| 表 |字符串|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Azure Cosmos DB 可还原的数据库帐户。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| ID |字符串| Azure 资源管理器资源的唯一资源标识符。 |
| location |字符串| 资源所属的资源组的位置。 |
| name |字符串| 资源管理器资源的名称。 |
| 属性 accountName |字符串| 全局数据库帐户的名称。 |
| apiType |[ApiType](#apitype)| 可还原数据库帐户的 API 类型。 |
| creationTime |字符串| 可还原数据库帐户的创建时间 (ISO-8601 格式) 。 |
| deletionTime |字符串| 已将可恢复的数据库帐户删除 (ISO-8601 格式) 的时间。 |
| restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| 可从中还原数据库帐户的区域的列表。 |
| 类型 |字符串| Azure 资源的类型。 |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

列表操作响应，其中包含可还原的数据库帐户及其属性。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| value |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| 可还原数据库帐户及其属性的列表。 |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

区域可还原帐户的属性。

| **名称** | **类型** | **说明** |
| --- | --- | --- |
| creationTime |字符串| 区域可恢复数据库帐户的创建时间 (ISO-8601 格式) 。 |
| deletionTime |字符串| 删除区域可还原数据库帐户的时间 (ISO-8601 格式) 。 |
| locationName |字符串| 区域可恢复帐户的位置。 |
| regionalDatabaseAccountInstanceId |字符串| 区域可恢复帐户的实例 ID。 |

## <a name="next-steps"></a>后续步骤

* 使用 REST API 列出 MongoDB Azure Cosmos DB API 中的可[还原集合](restorable-database-accounts-list-by-location.md)。
* 连续备份模式的[资源模型](continuous-backup-restore-resource-model.md)。