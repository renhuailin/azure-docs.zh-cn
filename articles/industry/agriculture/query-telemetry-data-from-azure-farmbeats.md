---
title: 查询引入的遥测数据
description: 本文介绍如何查询引入的遥测数据。
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: 88cf9236ca33eddd9f86a60c210aae253f8d9c2c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361019"
---
# <a name="query-ingested-telemetry-data"></a>查询引入的遥测数据

本文介绍如何从 Azure FarmBeats 查询引入的传感器数据。

从诸如设备和传感器的物联网 (IoT) 资源引入数据是 FarmBeats 中的常见方案。 你可以为设备和传感器创建元数据，然后将历史数据以规范格式引入 FarmBeats。 在 FarmBeats Datahub 中提供了传感器数据后，我们可以查询这些数据以生成可付诸实施的见解或生成模型。

## <a name="before-you-begin"></a>开始之前

在继续阅读本文之前，请确保已安装 FarmBeats，并已将 IoT 设备中的传感器遥测数据引入到 FarmBeats。

若要引入传感器遥测数据，请访问[引入历史遥测数据](ingest-historical-telemetry-data-in-azure-farmbeats.md)

在继续之前，还需要确保熟悉 FarmBeats REST API，因为你将使用这些 API 查询引入的遥测数据。 有关 FarmBeats API 详细信息，请参阅 [FarmBeats REST API](rest-api-in-azure-farmbeats.md)。 确保能够向 FarmBeats Datahub 终结点发出 API 请求。

## <a name="query-ingested-sensor-telemetry-data"></a>查询引入的传感器遥测数据

可通过两种方式从 FarmBeats 访问和查询遥测数据：

- API 以及
- 时序见解 (TSI)。

### <a name="query-using-rest-api"></a>使用 REST API 进行查询

遵循以下步骤使用 FarmBeats REST API 查询引入的传感器遥测数据：

1. 确定所需的传感器。 为此，可以在 /Sensor API 中发出 GET 请求。

> [!NOTE]
> 所需传感器对象的 id 和 sensorModelId 。

2. 在 /SensorModel API 中针对步骤 1 所述的 sensorModelId 发出 GET/{id}。 “Sensor Model”包含有关从传感器引入的遥测数据的所有元数据和详细信息。 例如，“Sensor Model”对象中的“Sensor Measure”包含有关传感器发送的测量值及其类型和单位的详细信息 。 例如，

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
请记下针对 Sensor Model 调用 GET/{id} 后的响应。

3. 使用以下输入有效负载对 /Telemetry API 执行 POST 调用

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. /Telemetry API 的响应如下所示：

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
在以上示例响应中，查询的传感器遥测数据提供了两个时间戳的数据，并在这两个时间戳中提供了测量名称（“moist_soil_last”）和报告的遥测值。 需要引用关联的传感器模型（如步骤 2 中所述），以解释报告值的类型和单位。

### <a name="query-using-azure-time-series-insights-tsi"></a>使用 Azure 时序见解 (TSI) 进行查询

FarmBeats 利用 [Azure 时序见解 (TSI)](https://azure.microsoft.com/services/time-series-insights/) 来以 IoT 的规模引入、存储、查询和可视化数据 -- 这些数据的上下文区分度很高，并已经过时序优化。

遥测数据进入事件中心，在经过处理后推送到 FarmBeats 资源组中的 TSI 环境。 然后，可以直接从 TSI 查询数据。 有关详细信息，请参阅 [TSI 文档](../../time-series-insights/time-series-insights-explorer.md)

遵循以下步骤在 TSI 中可视化数据：

1. 转到“Azure 门户” > “FarmBeats DataHub 资源组”，选择“时序见解”环境 (tsi-xxxx) >“数据访问策略”   。 添加拥有“读取者”或“参与者”访问权限的用户。
2. 转到“时序见解”环境 (tsi-xxxx) 的“概述”页，选择“时序见解资源管理器 URL”  。 现在，可将引入的遥测数据可视化。

除了存储、查询和可视化遥测数据以外，TSI 还支持集成到 Power BI 仪表板。 有关详细信息，请参阅[此文](../../time-series-insights/how-to-connect-power-bi.md)

## <a name="next-steps"></a>后续步骤

你现已从 Azure FarmBeats 实例查询了传感器数据。 接下来，请了解如何为农场[生成地图](generate-maps-in-azure-farmbeats.md#generate-maps)。