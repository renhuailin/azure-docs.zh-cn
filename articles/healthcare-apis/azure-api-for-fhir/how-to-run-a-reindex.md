---
title: 如何在适用于 FHIR 的 Azure API 中运行重编索引作业
description: 本文介绍如何运行重编索引作业，为数据库中尚未编制索引的任何搜索或排序参数编制索引。
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
ms.openlocfilehash: 31377dbae6016e7a505b92b983bc5368c456981a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778236"
---
# <a name="running-a-reindex-job-in-azure-api-for-fhir"></a>在适用于 FHIR 的 Azure API 中运行重编索引作业

在某些情况下，你可能在适用于 FHIR 的 Azure API 中有一些尚未建立索引的搜索或排序参数。 在定义自己的搜索参数时，这一点尤其重要。 在为搜索参数编制索引之前，它不能用于搜索。 本文概述如何运行重编索引作业，为数据库中尚未编制索引的任何搜索或排序参数编制索引。

> [!Warning]
> 在开始之前请务必阅读整篇文章。 重编索引作业可能会非常耗用性能。 本文包含用于限制和控制重编索引作业的选项。

## <a name="how-to-run-a-reindex-job"></a>如何运行重新索引作业 

要启动重编索引作业，请使用以下代码示例：

```json
POST {{FHIR URL}}/$reindex 

{ 

“resourceType”: “Parameters”,  

“parameter”: [] 

}
 ```

如果请求成功，则返回状态“201 Created”。 此消息的结果如下：

```json
HTTP/1.1 201 Created 
Content-Location: https://{{FHIR URL}}/_operations/reindex/560c7c61-2c70-4c54-b86d-c53a9d29495e 

{
  "resourceType": "Parameters",
  "id": "560c7c61-2c70-4c54-b86d-c53a9d29495e",
  "meta": {
    "versionId": "\"4c0049cd-0000-0100-0000-607dc5a90000\""
  },
  "parameter": [
    {
      "name": "id",
      "valueString": "560c7c61-2c70-4c54-b86d-c53a9d29495e"
    },
    {
      "name": "queuedTime",
      "valueDateTime": "2021-04-19T18:02:17.0118558+00:00"
    },
    {
      "name": "totalResourcesToReindex",
      "valueDecimal": 0.0
    },
    {
      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 0.0
    },
    {
      "name": "progress",
      "valueDecimal": 0.0
    },
    {
      "name": "status",
      "valueString": "Queued"
    },
    {
      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {
      "name": "resources",
      "valueString": ""
    },
    {
      "name": "searchParams",
      "valueString": ""
    }
  ]
}
```

> [!NOTE]
> 若要检查重索引作业的状态或取消重索引作业，则需要重编索引 ID。 这是生成的参数资源的 ID。 在上面的示例中，重编索引作业的 ID 为 `560c7c61-2c70-4c54-b86d-c53a9d29495e`。

 ## <a name="how-to-check-the-status-of-a-reindex-job"></a>如何检查重编索引的状态

启动重编索引作业后，可以使用以下方法检查作业的状态：

`GET {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

重编索引作业结果的状态如下所示：

```json
{

  "resourceType": "Parameters",
  "id": "b65fd841-1c62-47c6-898f-c9016ced8f77",
  "meta": {

    "versionId": "\"1800f05f-0000-0100-0000-607a1a7c0000\""
  },
  "parameter": [

    {

      "name": "id",
      "valueString": "b65fd841-1c62-47c6-898f-c9016ced8f77"
    },
    {

      "name": "startTime",
      "valueDateTime": "2021-04-16T23:11:35.4223217+00:00"
    },
    {

      "name": "queuedTime",
      "valueDateTime": "2021-04-16T23:11:29.0288163+00:00"
    },
    {

      "name": "totalResourcesToReindex",
      "valueDecimal": 262544.0
    },
    {

      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 5754.0
    },
    {

      "name": "progress",
      "valueDecimal": 2.0
    },
    {

      "name": "status",
      "valueString": "Running"
    },
    {

      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {

      "name": "resources",
      "valueString": 
      "{LIST OF IMPACTED RESOURCES}"
    },
    {
```

在重编索引作业结果中显示如下信息：

* **totalResourcesToReindex**：包括在作业中重编索引的资源的总数。

* **resourcesSuccessfullyReindexed**：已成功重编索引的总数。

* **progress**：重编索引作业完成进度的百分比。 等于 resourcesSuccessfullyReindexed/totalResourcesToReindex x 100。

* **status**：这将显示重编索引作业是排队、正在运行、完成、失败还是已取消。

* **resources**：这会列出受重编索引作业影响的所有资源类型。

## <a name="delete-a-reindex-job"></a>删除重编索引作业

如果需要取消重编索引作业，请使用删除调用并指定重编索引作业 ID：

`Delete {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

## <a name="performance-considerations"></a>性能注意事项

重编索引作业可能会非常耗用性能。 我们实现了一些限制控件，帮助你管理重编索引作业在数据库上的运行方式。

> [!NOTE]
> 重编索引作业在大型数据集上运行数天的情况并不少见。 对于拥有 30 万亿个资源的数据库，我们注意到在有 10 万个 RU 的情况下为整个数据库重新编制索引需要 4-5 天的时间。

下表列出了可用参数、默认值和建议范围。 可以使用这些参数加快进程（使用更多的计算）或减缓进程（使用更少的计算）。 例如，可以在流量较低的时候运行重编索引作业，并增加计算以更快地完成该作业。 相反，可以使用设置来确保计算的使用率非常低，并让它在后台运行几天。 

| **参数**                     | **说明**              | **默认**        | **建议范围**           |
| --------------------------------- | ---------------------------- | ------------------ | ------------------------------- |
| QueryDelayIntervalInMilliseconds  | 该参数是在重编索引作业期间启动每批资源之间的延迟。 | 500 MS（0.5 秒） | 50 到 5000：50 将加快重索引作业，5000 将降低它的默认速度。 |
| MaximumResourcesPerQuery  | 该参数是在要重编索引的一批资源中包含的最大资源数。  | 100 | 1-500 |
| MaximumConcurrency  | 该参数是一次完成的批数。  | 1 | 1-5 |
| targetDataStoreUsagePercentage | 该参数可以指定用于重编索引作业的数据存储的百分比。 例如，可以指定 50%，这将确保重编索引作业最多使用 Cosmos DB 上可用 RU 的 50%。  | 如果没有指定，则意味着最多可以使用 100%。 | 1-100 |

如果要使用上述任何参数，可以在启动重索引作业时将它们传递到参数资源。

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "maximumConcurrency",
      "valueInteger": "3"
    },
    {
      "name": "targetDataStoreUsagePercentage",
      "valueInteger": "20"
    },
    {
      "name": "queryDelayIntervalInMilliseconds",
      "valueInteger": "1000"
    },
    {
      "name": "maximumNumberOfResourcesPerQuery",
      "valueInteger": "1"
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何启动重编索引作业。 要了解如何定义需要重编索引作业的新搜索参数，请参阅 

>[!div class="nextstepaction"]
>[定义自定义搜索参数](how-to-do-custom-search.md)

         
     
