---
title: Azure FarmBeats 作为事件网格源（预览版）
description: 介绍针对 Azure 事件网格中的 Azure FarmBeats 事件提供的属性和架构
ms.topic: conceptual
ms.date: 06/06/2021
ms.openlocfilehash: 552a676fcfc457a662276aa7b5948670f700d208
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726735"
---
# <a name="azure-farmbeats-as-event-grid-source-preview"></a>Azure FarmBeats 作为事件网格源（预览版）
本文提供 Azure FarmBeats 事件的属性和架构。  有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 

## <a name="available-event-types"></a>可用事件类型

|事件名称 | 描述|
|-----|----|
|Microsoft.AgFoodPlatform.FarmerChanged|创建/更新/删除农民时发布。 
|Microsoft.AgFoodPlatform.FarmChanged| 创建/更新/删除农场时发布。
|Microsoft.AgFoodPlatform.BoundaryChanged|创建/更新/删除边界时发布。
|Microsoft.AgFoodPlatform.FieldChanged|创建/更新/删除土地时发布。
|Microsoft.AgFoodPlatform.SeasonalFieldChanged|创建/更新/删除季节性土地时发布。
|Microsoft.AgFoodPlatform.SeasonChanged|创建/更新/删除季节时发布。
|Microsoft.AgFoodPlatform.CropChanged|创建/更新/删除作物时发布。
|Microsoft.AgFoodPlatform.CropVarietyChanged|创建/更新/删除作物品种时发布。
|Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged| 卫星数据引入作业的状态发生更改（例如作业创建、进行中或完成）时发布。
|Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged|天气数据引入作业的状态发生更改（例如作业创建、进行中或完成）时发布。
|Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged| 农场经营数据引入作业的状态发生更改（例如作业创建、进行中或完成）时发布。
|Microsoft.AgFoodPlatform.ApplicationDataChanged|创建/更新/删除应用程序数据时发布。 此事件与农场经营数据相关联。
|Microsoft.AgFoodPlatform.HarvestingDataChanged|创建/更新/删除产量数据时发布。此事件与农场经营数据相关联。
|Microsoft.AgFoodPlatform.TillageDataChanged|创建/更新/删除耕作数据时发布。 此事件与农场经营数据相关联。
|Microsoft.AgFoodPlatform.PlantingDataChanged|创建/更新/删除种植数据时发布。此事件与农场经营数据相关联。

## <a name="event-properties"></a>事件属性
每个 FarmBeats 事件都有两个部分，一个是事件中的通用部分，另一个（数据对象）包含每个事件特定的属性。 

以下架构详细说明了事件中的通用部分。

### <a name="event-grid-event-schema"></a>事件网格事件架构
事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `topic` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 事件主题的发布者定义路径。 |
| `eventType` | string | 此事件源的一个注册事件类型。 |
| `eventTime` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | string | 事件的唯一标识符。 |
| `data` | 对象 | 应用配置事件数据。 |
| `dataVersion` | string | 数据对象的架构版本。 发布者定义架构版本。 |
| `metadataVersion` | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |


下表详细列出了每个事件的数据对象中的属性。

对于 FarmerChanged、FarmChanged、SeasonChanged、CropChanged、CropVarietyChanged FarmBeats 事件，数据对象包含以下属性：

|属性 | 类型| 说明|
|----| ----| ----|
id| string| 用户定义的资源 ID，例如农场 ID、农民 ID 等。
actionType| string| 指示在发布事件期间触发的更改。 适用的值为“已创建”、“已更新”和“已删除”
状态| string| 包含用户定义的资源状态。
properties| object| 它包含用户定义的键值对
modifiedDateTime| 日期时间|上次修改资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
createdDateTime|日期时间|创建资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
eTag|   string| 实现乐观并发
description| 字符串|    资源的文本说明


BoundaryChanged FarmBeats 事件具有以下数据对象：

|属性 | 类型| 说明|
|----| ----| ----|
id| string| 用户定义的边界 ID
actionType| string| 指示在发布事件期间触发的更改。 适用的值为“已创建”、“已更新”和“已删除”。
parentId|   string| 父级边界所属的 ID。
parentType| string| 父级边界所属的类型。
isPrimary|  boolean|    指示边界是否为主要边界。
farmerId|   string| 包含与边界关联的农民 ID。
properties| object| 它包含用户定义的键值对。
modifiedDateTime| 日期时间|上次修改资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
createdDateTime|日期时间|创建资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
状态| string| 包含用户定义的资源状态。
eTag|   string| 实现乐观并发。
description| 字符串|    资源的文本说明。

FieldChanged FarmBeats 事件具有以下数据对象：

属性|   类型|   说明
|----| ----| ----|
id| string| 用户定义的土地 ID
farmId| string| 用户定义的与土地关联的农场 ID
farmerId|   string| 用户定义的与土地关联的农民 ID
name|   string| 用户定义的土地名称
actionType| string| 指示在发布事件时触发的更改。 适用的值为“已创建”、“已更新”和“已删除”
properties| object| 它包含用户定义的键值对
modifiedDateTime| 日期时间|上次修改资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
createdDateTime|日期时间|创建资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
状态| string| 包含用户定义的资源状态。
eTag|   string| 实现乐观并发
description|字符串| 资源的文本说明

SeasonalFieldChanged FarmBeats 事件具有以下数据对象：

属性|   类型|   说明
|----| ----| ----|
id| string| 用户定义的季节性土地 ID
farmId| string| 用户定义的与季节性土地关联的农场 ID
farmerId|   string| 用户定义的与季节性土地关联的农民 ID
seasonId|   string| 用户定义的与季节性土地关联的季节 ID
fieldId|    string| 用户定义的与季节性土地关联的土地 ID
name|   string| 用户定义的季节性土地名称
actionType| string| 指示在发布事件时触发的更改。 适用的值为“已创建”、“已更新”和“已删除”
properties| object| 它包含用户定义的键值对
modifiedDateTime| 日期时间|上次修改资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
createdDateTime|日期时间|创建资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
状态| string| 包含用户定义的资源状态。
eTag|   string| 实现乐观并发
description| 字符串|    资源的文本说明

SatelliteDataIngestionJobChanged、WeatherDataIngestionJobChanged 和 FarmOperationsDataIngestionJobChanged FarmBeats 事件具有以下数据对象：

属性|   类型|   说明
|----|----|----|
id|字符串| 作业的唯一 ID。
name| string| 用户定义的作业名称。
状态|string|作业可以处于各种状态。
isCancellationRequested| boolean|请求取消作业时设置的标记。
description|字符串| 作业的文本说明。
farmerId|string| 为其创建作业的农民的 ID。
message|string| 用于捕获更多作业详细信息的状态消息。
lastActionDateTime|日期时间|对作业执行的上一个操作的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
createdDateTime|日期时间|创建资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。


FarmBeats 农场经营数据更改事件（如 ApplicationDataChanged、HarvestingDataChanged、PlantingDataChanged 和 TillageDataChanged）具有以下数据对象：

属性|   类型|   说明
|----|----|----|
id| string| 用户定义的资源 ID，例如农场 ID、农民 ID 等。
状态| string| 包含作业的状态。 
actionType|string|
source| string| 来自 FarmBeats 的消息，提供关于作业的详细信息。    
modifiedDateTime| 日期时间|上次修改资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
createdDateTime|日期时间|创建资源的日期时间，示例格式：yyyy-MM-ddTHH:mm:ssZ。
eTag|   string| 实现乐观并发
description|字符串| 资源的文本说明


## <a name="sample-events"></a>样本事件 
这些事件示例表示事件通知。

**事件类型：Microsoft.AgFoodPlatform.FarmerChanged**

```json
{
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:53:28Z",
      "eTag": "860197cc-0000-0700-0000-60420da80000",
      "id": "UNIQUE-FARMER-ID",
      "name": "sample farmer",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:53:28Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "81fbe1de-4ae4-4284-964f-59da80a6bfe7",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmerChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:53:28.2783745Z"
  }
```

**事件类型：Microsoft.AgFoodPlatform.FarmChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:55:57Z",
      "eTag": "8601e3d5-0000-0700-0000-60420e3d0000",
      "id": "UNIQUE-FARM-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:55:57Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "31a31be7-51fb-48f3-adfd-6fb4400be002",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farms/UNIQUE-FARM-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:55:57.6026173Z"
  }
```
**事件类型：Microsoft.AgFoodPlatform.BoundaryChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "parentId": "OPTIONAL-UNIQUE-FIELD-ID",
      "isPrimary": true,
      "actionType": "Created",
      "modifiedDateTime": "2021-03-05T11:15:29Z",
      "eTag": "860109f7-0000-0700-0000-604212d10000",
      "id": "UNIQUE-BOUNDARY-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:15:29Z"
    },
    "id": "3d3453b2-5a94-45a7-98eb-fc2979a00317",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/boundaries/UNIQUE-BOUNDARY-ID",
    "eventType": "Microsoft.AgFoodPlatform.BoundaryChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:15:29.4797354Z"
  }
  ```

**事件类型：Microsoft.AgFoodPlatform.FieldChanged**

```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T10:58:43Z",
      "eTag": "860124dc-0000-0700-0000-60420ee30000",
      "id": "UNIQUE-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T10:58:43Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "1ad04ed0-ac05-4c4e-aa3d-87facb3cc97c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/fields/UNIQUE-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.FieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T10:58:43.3222921Z"
  }
  ```
**事件类型：Microsoft.AgFoodPlatform.SeasonalFieldChanged**
```json
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "seasonId": "UNIQUE-SEASON-ID",
      "fieldId": "UNIQUE-FIELD-ID",
      "farmId": "UNIQUE-FARM-ID",
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:24:56Z",
      "eTag": "8701300b-0000-0700-0000-604215080000",
      "id": "UNIQUE-SEASONAL-FIELD-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:24:56Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ff59a0a3-6226-42c0-9e70-01da55efa797",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/seasonalFields/UNIQUE-SEASONAL-FIELD-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonalFieldChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:24:56.4210287Z"
  }
```
**事件类型：Microsoft.AgFoodPlatform.SeasonChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:18:38Z",
      "eTag": "86019afd-0000-0700-0000-6042138e0000",
      "id": "UNIQUE-SEASON-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:18:38Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "63989475-397b-4b92-8160-8743bf8e5804",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/seasons/UNIQUE-SEASON-ID",
    "eventType": "Microsoft.AgFoodPlatform.SeasonChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:18:38.5804699Z"
  }
  ```

  **事件类型：Microsoft.AgFoodPlatform.CropChanged**

```json
  {
    "data": {
      "actionType": "Created",
      "status": "Sample status",
      "modifiedDateTime": "2021-03-05T11:03:48Z",
      "eTag": "8601c4e5-0000-0700-0000-604210150000",
      "id": "UNIQUE-CROP-ID",
      "name": "Display name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:03:48Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "4c59a797-b76d-48ec-8915-ceff58628f35",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/crops/UNIQUE-CROP-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:03:49.0590658Z"
  }
  ```

**事件类型：Microsoft.AgFoodPlatform.CropVarietyChanged**

```json
  {
    "data": {
      "cropId": "UNIQUE-CROP-ID",
      "actionType": "Created",
      "status": "string",
      "modifiedDateTime": "2021-03-05T11:10:21Z",
      "eTag": "860130ef-0000-0700-0000-6042119d0000",
      "id": "UNIQUE-CROP-VARIETY-ID",
      "name": "Sample status",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:10:21Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "29aefdb9-d648-442c-81f8-694f3f47583c",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/cropVarieties/UNIQUE-CROP-VARIETY-ID",
    "eventType": "Microsoft.AgFoodPlatform.CropVarietyChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:10:21.4572495Z"
  }
```
**事件类型：Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE - FARMER - ID",
      "message": "Created job 'job1' to fetch satellite data for boundary 'boundary1' from startDate '06/01/2021' to endDate '06/01/2021' (both inclusive).",
      "status": "Waiting",
      "lastActionDateTime": "2021-06-01T11:25:37.8634096Z",
      "isCancellationRequested": false,
      "id": "UNIQUE - JOB - ID",
      "name": "samplejob",
      "description": "Sample for testing events",
      "createdDateTime": "2021-06-01T11:25:32.3421173Z",
      "properties": {
        "key1": "testvalue1",
        "key2": 123.45
      }
    },
    "id": "925c6be2-6561-4572-b7dd-0f3084a54567",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/{UNIQUE-FARMER-ID}/satelliteDataIngestionJobs/{UNIQUE-JOB-ID}",
    "eventType": "Microsoft.AgFoodPlatform.SatelliteDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:25:37.8634764Z"
  }
]
```
**事件类型：Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Created job to fetch weather data for job name 'job2', farmer id 'farmer2' and boundary id 'boundary2'.",
      "status": "Running",
      "lastActionDateTime": "2021-06-01T11:22:27.9031003Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "createdDateTime": "2021-06-01T07:13:54.8843617Z"
    },
    "id": "ec30313a-ff2f-4b50-882b-31188113c15b",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/weatherDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.WeatherDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:22:27.9031302Z"
  }
]

```
**事件类型：Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged**
```json
[
  {
    "data": {
      "farmerId": "UNIQUE-FARMER-ID",
      "message": "Job completed successfully. Data statistics:{ Processed operations count = 6, Organizations count = 1, Processed organizations count = 1, Processed fields count = 2, Operations count = 6, ShapefileAttachmentsCount = 0, Fields count = 2 }",
      "status": "Succeeded",
      "lastActionDateTime": "2021-06-01T11:30:54.733625Z",
      "isCancellationRequested": false,
      "id": "UNIQUE-JOB-ID",
      "name": "sample-job",
      "description": "sample description",
      "createdDateTime": "2021-06-01T11:30:39.0905288Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "ebdbb7a1-ad28-4af7-b3a2-a4a3a2dd1b4f",
    "topic": "/subscriptions/{Subscription -ID}/resourceGroups/{RESOURCE - GROUP - NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/farmOperationDataIngestionJobs/UNIQUE-JOB-ID",
    "eventType": "Microsoft.AgFoodPlatform.FarmOperationDataIngestionJobStatusChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-01T11:30:54.733671Z"
  }
]

```
**事件类型：Microsoft.AgFoodPlatform.ApplicationDataChanged**

```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:27:24Z",
      "eTag": "87011311-0000-0700-0000-6042159c0000",
      "id": "UNIQUE-APPLICATION-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:27:24Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "e499f6c4-63ba-4217-8261-0c6cb0e398d2",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/applicationData/UNIQUE-APPLICATION-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.ApplicationDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:27:24.164612Z"
  }
```

**事件类型：Microsoft.AgFoodPlatform.HarvestDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:33:41Z",
      "eTag": "8701141b-0000-0700-0000-604217150000",
      "id": "UNIQUE-HARVEST-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:33:41Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "dc3837c0-1eed-4bfa-88b6-d018cf6af4db",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/harvestData/UNIQUE-HARVEST-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.HarvestDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:33:41.3434992Z"
  }
```
**事件类型：Microsoft.AgFoodPlatform.TillageDataChanged**
```json
  {
    "data": {
      "actionType": "Updated",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "sample source",
      "modifiedDateTime": "2021-06-15T10:31:07Z",
      "eTag": "6405f027-0000-0100-0000-60c8816b0000",
      "id": "c9858c3f-fb94-474a-a6de-103b453df976",
      "createdDateTime": "2021-06-15T10:31:07Z",
      "name": "sample name",
      "description":"sample description"
      "properties": {
        "_orgId": "498221",
        "_fieldId": "e61b83f4-3a12-431e-8010-596f2466dc27",
        "_cropSeason": "2010"
      }
    },
    "id": "f06f6686-1fa8-41fd-be99-46f40f495cce",
    "topic": "/subscriptions/da9091ec-d18f-456c-9c21-5783ee7f4645/resourceGroups/internal-farmbeats-resources/providers/Microsoft.AgFoodPlatform/farmBeats/internal-eus",
    "subject": "/farmers/10e3d7bf-c559-48be-af31-4e00df83bfcd/tillageData/c9858c3f-fb94-474a-a6de-103b453df976",
    "eventType": "Microsoft.AgFoodPlatform.TillageDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-06-15T10:31:07.6778047Z"
  }
```

**事件类型：Microsoft.AgFoodPlatform.PlantingDataChanged**
```json
  {
    "data": {
      "actionType": "Created",
      "farmerId": "UNIQUE-FARMER-ID",
      "source": "Sample source",
      "modifiedDateTime": "2021-03-05T11:41:18Z",
      "eTag": "8701242a-0000-0700-0000-604218de0000",
      "id": "UNIQUE-PLANTING-DATA-ID",
      "status": "Sample status",
      "name": "sample name",
      "description": "Sample description",
      "createdDateTime": "2021-03-05T11:41:18Z",
      "properties": {
        "key1": "value1",
        "key2": 123.45
      }
    },
    "id": "42589c7f-4e16-4a4d-9314-d611c822f7ac",
    "topic": "/subscriptions/{SUBSCRIPTION-ID}/resourceGroups/{RESOURCE-GROUP-NAME}/providers/Microsoft.AgFoodPlatform/farmBeats/{FARMBEATS-RESOURCE-NAME}",
    "subject": "/farmers/UNIQUE-FARMER-ID/plantingData/UNIQUE-PLANTING-DATA-ID",
    "eventType": "Microsoft.AgFoodPlatform.PlantingDataChanged",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-03-05T11:41:18.1744322Z"
  }
```



## <a name="next-steps"></a>后续步骤
* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
