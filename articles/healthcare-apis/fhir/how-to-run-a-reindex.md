---
title: 如何在 Azure API for FHIR 中运行重建索引作业
description: 本文介绍如何运行索引编制作业，为尚未在数据库中编制索引的任何搜索或排序参数编制索引。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
ms.openlocfilehash: ea679023b2b5bb620bb9684a0e841f6cc4fa310d
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2021
ms.locfileid: "109846997"
---
# <a name="running-a-reindex-job"></a>运行重建索引作业

在某些情况下，可能会在 Azure API for FHIR 中搜索或排序参数，这些参数尚未编制索引。 当你定义自己的搜索参数时，这尤其有用。 在对搜索参数编制索引之前，不能在搜索中使用它。 本文介绍如何运行索引编制作业，对尚未在数据库中编制索引的任何搜索或排序参数编制索引。

> [!Warning]
> 在开始之前阅读本文，这一点很重要。 重建索引作业的性能可能非常高。 本文包含有关如何限制和控制重建索引作业的选项。

## <a name="how-to-run-a-reindex-job"></a>如何运行重新索引作业 

若要启动重建索引作业，请使用下面的代码示例：

```json
POST {{FHIR URL}}/$reindex 

{ 

“resourceType”: “Parameters”,  

“parameter”: [] 

}
 ```

如果请求成功，则返回已创建的状态 **201** 。 此消息的结果如下所示：

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
> 若要检查的状态或取消索引编制作业，需要重新编制索引的 ID。 这是) 上方显示的结果参数资源的 ID (。 还可以在内容位置字符串的末尾找到重建索引 ID。 在上面的示例中，它会是 `560c7c61-2c70-4c54-b86d-c53a9d29495e`。

 ## <a name="how-to-check-the-status-of-a-reindex-job"></a>如何检查重建索引作业的状态

开始重建索引作业后，可以使用以下操作来检查作业的状态：

`GET {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

重建索引作业结果的状态如下所示：

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

重建索引作业结果中显示以下信息：

* **totalResourcesToReindex：** 包括作为作业的一部分重新索引的资源总数。

* **resourcesSuccessfullyReindexed：** 已成功重新索引的总计。

* **progress：** 重新索引作业完成百分比。 等于 resourcesSuccessfullyReindexed/totalResourcesToReindex x 100。

* **status：** 这将说明重新索引作业是排队、正在运行、完成、失败还是已取消。

* **resources：** 这会列出受重新索引作业影响的所有资源类型。

## <a name="delete-a-reindex-job"></a>删除重新索引作业

如果需要取消重新索引作业，请使用删除调用并指定重新索引作业 ID：

`Delete {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

## <a name="performance-considerations"></a>性能注意事项

重新索引作业可能会非常耗用性能。 我们实现了一些限制控件，以帮助你管理重新索引作业在数据库上的运行方式。

> [!NOTE]
> 重新索引作业在大型数据集上运行数天的情况并不少见。 对于具有 30，000，000 万个资源的数据库，我们注意到，在 100K 个 US 中重新索引整个数据库需要 4-5 天。

下表概述了可用参数、默认值和推荐的范围。 可以使用这些参数来加快进程， (使用更多的计算) 或降低进程 (使用更少的计算) 。 例如，可以在较低的流量时间运行重新索引作业，并增大计算速度以更快地完成该作业。 相反，可以使用设置来确保计算使用率非常低，并在后台运行数天。 

| **参数**                     | **说明**              | **默认**        | **建议的范围**           |
| --------------------------------- | ---------------------------- | ------------------ | ------------------------------- |
| QueryDelayIntervalInMilliseconds  | 这是在重新索引作业期间启动的每批资源之间的延迟。 | 500 MS (.5 秒)  | 50到5000：50将提高重建索引作业的速度，5000会使其从默认值降低。 |
| MaximumResourcesPerQuery  | 这是要重新编制索引的资源批中包含的最大资源数。  | 100 | 1-500 |
| MaximumConcurreny  | 这是一次完成的批处理数。  | 1 | 1-5 |
| targetDataStoreUsagePercentrage | 这允许您指定要用于重新索引作业的数据存储的百分比。 例如，你可以指定50%，这将确保在 Cosmos DB 上，最多可确保索引编制作业使用50% 的可用 ru。  | 不存在，这意味着可以使用最多100%。 | 1-100 |

如果要使用上述任意参数，则可以在启动重新索引作业时将其传递到参数资源中。

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

本文介绍了如何启动重建索引作业。 若要了解如何定义需要重建索引作业的新搜索参数，请参阅 

>[!div class="nextstepaction"]
>[定义自定义搜索参数](how-to-do-custom-search.md)

         
     