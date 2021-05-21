---
title: 如何在 Azure API for FHIR 中运行重新索引Azure API for FHIR
description: 本文介绍如何运行重新索引作业，为数据库中尚未编制索引的任何搜索或排序参数编制索引。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
ms.openlocfilehash: 5285de1a8481f37238ce3e3f3038d0d4f4c3d54a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110078530"
---
# <a name="running-a-reindex-job"></a>运行重新索引作业

在某些情况下，你可能在尚未编制索引的Azure API for FHIR搜索或排序参数。 在定义自己的搜索参数时，这一点尤其相关。 在索引搜索参数之前，它不能用于搜索。 本文概述如何运行重新编制索引作业，为数据库中尚未编制索引的任何搜索或排序参数编制索引。

> [!Warning]
> 在入门之前阅读整篇文章非常重要。 重新索引作业可能会非常耗用性能。 本文包含用于限制和控制重新索引作业的选项。

## <a name="how-to-run-a-reindex-job"></a>如何运行重新索引作业 

若要启动重新索引作业，请使用以下代码示例：

```json
POST {{FHIR URL}}/$reindex 

{ 

“resourceType”: “Parameters”,  

“parameter”: [] 

}
 ```

如果请求成功，则返回状态 **"201 Created"。** 此消息的结果如下所示：

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
> 若要检查 状态或取消重新索引作业，需要重新索引 ID。 这是生成的 Parameters 资源的 ID。 在以上示例中，重新索引作业的 ID 为 `560c7c61-2c70-4c54-b86d-c53a9d29495e` 。

 ## <a name="how-to-check-the-status-of-a-reindex-job"></a>如何检查重新索引作业的状态

启动重新索引作业后，可以使用以下方法检查作业的状态：

`GET {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

重新索引作业结果的状态如下所示：

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

重新索引作业结果中显示了以下信息：

* **totalResourcesToReindex**：包含作为作业的一部分而重新编制索引的资源总数。

* **resourcesSuccessfullyReindexed**：已成功重新编制索引的总计。

* **进度**：已完成重新索引作业百分比。 等于 resourcesSuccessfullyReindexed/totalResourcesToReindex x 100。

* **状态**：如果重建索引作业已排队、正在运行、已完成、失败或已取消，这将会出现。

* **资源**：此列表列出了由重建索引作业影响的所有资源类型。

## <a name="delete-a-reindex-job"></a>删除重建索引作业

如果需要取消重建索引作业，请使用 delete 调用并指定重新索引作业 ID：

`Delete {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

## <a name="performance-considerations"></a>性能注意事项

重建索引作业的性能可能会相当高。 我们实现了一些限制控制，以帮助你管理重新编制索引作业对数据库的运行的方式。

> [!NOTE]
> 在较大的数据集上，对于每日运行的索引编制作业，这种情况并不常见。 对于包含30000000万个资源的数据库，我们4-5 注意到，在10万个

下表列出了可用参数、默认值和建议范围。 您可以使用这些参数来加速进程 (使用更多计算) 或减慢进程 (使用较少的计算) 。 例如，可以在较低的流量时间运行重新索引作业，并增加计算以使其更快完成。 相反，您可以使用这些设置来确保计算的使用率非常低，并使其在后台运行一天。 

| **参数**                     | **说明**              | **默认**        | **建议范围**           |
| --------------------------------- | ---------------------------- | ------------------ | ------------------------------- |
| QueryDelayIntervalInMilliseconds  | 这是在重建索引作业期间要启动的每一批资源之间的延迟。 | 500 MS ( 5 秒)  | 50 到 5000：50 将加快重新索引作业的速度，5000 会从默认值降低。 |
| MaximumResourcesPerQuery  | 这是要重新索引的资源批中包含的最大资源数。  | 100 | 1-500 |
| MaximumConcurreny  | 这是一次完成批处理的数量。  | 1 | 1-5 |
| targetDataStoreUsagePercentrage | 这样，可以指定要用于重新索引作业的数据存储百分比。 例如，可以指定 50%，这将确保重新索引作业最多使用 50% 的可用 COSMOS DB。  | 不存在，这意味着最多可以使用 100%。 | 1-100 |

如果要使用上述任何参数，可以在启动重新索引作业时将它们传递到 Parameters 资源。

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

本文介绍了如何启动重新索引作业。 若要了解如何定义需要重新索引作业的新搜索参数，请参阅 

>[!div class="nextstepaction"]
>[定义自定义搜索参数](how-to-do-custom-search.md)

         
     