---
title: 从天气合作伙伴获取天气数据
description: 本文介绍如何从合作伙伴获取天气数据。
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460876"
---
# <a name="get-weather-data-from-weather-partners"></a>从天气合作伙伴获取天气数据

借助基于 Docker 的连接器框架，Azure FarmBeats 可帮助你从天气数据提供程序引入天气数据。 通过此框架，天气数据提供程序实现了可与 FarmBeats 集成的 Docker。 目前支持以下天气数据提供程序。

  ![FarmBeats 合作伙伴](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

天气数据可用于在 FarmBeats 中生成可操作的见解，并生成 AI 或 ML 模型。

## <a name="before-you-start"></a>准备工作

若要获取天气数据，请确保已[安装 FarmBeats](./install-azure-farmbeats.md)。 版本 1.2.11 和更高版本支持天气集成。 

## <a name="enable-weather-integration-with-farmbeats"></a>启用与 FarmBeats 的天气集成

若要开始在 FarmBeats 数据中心中获取天气数据：

1. 请转到 FarmBeats 数据中心 Swagger `https://farmbeatswebsite-api.azurewebsites.net/swagger`。

2. 转到 /Partner API，然后发出 POST 请求。 使用以下输入有效负载：

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   例如，若要从 DTN 获取天气数据，请使用以下有效负载。 可以根据你的偏好修改名称和说明。

   > [!NOTE]
   > 以下步骤需要提供 API 密钥。 若要获取 DTN 订阅的密钥，请联系 DTN。

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > 有关合作伙伴对象的详细信息，请参阅本文[附录](get-weather-data-from-weather-partner.md#appendix)。

   上述步骤将预配资源，以使 Docker 在客户的 FarmBeats 环境中运行。  

   预配资源大约需要 10 到 15 分钟。

3. 检查上一步中创建的 /Partner 对象的状态。 若要检查状态，请对 /Partner API 发出 GET 请求，并检查合作伙伴对象的状态。 FarmBeats 成功预配合作伙伴后，状态将设置为“活动”。

4. 对 /JobType API 发出 GET 请求。 检查之前在添加合作伙伴过程中创建的天气作业。 在天气作业中，“pipelineName”字段采用以下格式：partner-name_partner-type_job-name。

      现在，FarmBeats 实例拥有处于活动状态的天气数据合作伙伴。 你可以运行作业来请求获得特定位置（经纬度）和日期范围内的天气数据。 作业类型将包含有关天气作业运行所需的参数的详细信息。

      例如，对于 DTN，将创建以下作业类型：
   
      - **get_dtn_daily_observations**：获取某个位置在某个时间段内的每日观察数据。
      - **get_dtn_daily_forecasts**：获取某个位置在某个时间段内的每日预测数据。
      - **get_dtn_hourly_observations**：获取某个位置在某个时间段内的每小时观察数据。
      - **get_dtn_hourly_forecasts**：获取某个位置在某个时间段内的每小时预测数据。

6. 记下作业类型的 ID 和参数。

7. 转到 /Jobs API 并对 /Jobs 发出 POST 请求。 使用以下输入有效负载：

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   例如，若要运行 get_dtn_daily_observations，请使用以下有效负载：

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. 上述步骤运行了在合作伙伴 Docker 中定义的天气作业，并将天气数据引入到 FarmBeats 中。 你可以通过对 /Jobs 发出 GET 请求来检查作业的状态。 在响应中，查找“currentState”。 完成后，“currentState”将设置为“已成功”。

## <a name="query-ingested-weather-data"></a>查询引入的天气数据

天气作业完成后，可以使用 FarmBeats 数据中心 REST API 查询引入的天气数据，以生成模型或可操作的见解。

若要使用 FarmBeats REST API 查询天气数据：

1. 在 FarmBeats 数据中心 [Swagger](https://yourdatahub.azurewebsites.net/swagger) 中，转到 /WeatherDataLocation API 并发出 GET 请求。 响应包括为作业运行所指定的位置（经纬度）创建的 /WeatherDataLocation 对象。 记下对象的 ID 和 weatherDataModelId。

2. 与先前操作一样，对 /WeatherDataModel API 发出 GET/{id} 请求以获取 weatherDataModelId。 天气数据模型显示有关引入的天气数据的所有元数据和详细信息。 例如，在天气数据模型对象中，天气度量值详细说明了支持的天气信息以及类型和度量单位。 例如：

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   记下对天气数据模型 GET/{id} 调用的响应。

3. 转到遥测 API 并发出 POST 请求。 使用以下输入有效负载：

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    响应显示指定时间范围内可用的天气数据：

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

在上面的示例中，响应显示两个时间戳的数据。 它还显示两个时间戳中报告的天气数据的度量值名称（温度）和值。 请参阅关联的天气数据模型，以解释报告值的类型和单位。

## <a name="troubleshoot-job-failures"></a>排查作业故障

若要排查作业故障，请[查看作业日志](troubleshoot-azure-farmbeats.md#weather-data-job-failures)。


## <a name="appendix"></a>附录

|        Partner   |  详细信息   |
| ------- | -------             |
|     DockerDetails-imageName         |          Docker 映像名。 例如，docker.io/mydockerimage（hub.docker.com 中的映像）或 myazureacr.azurecr.io/mydockerimage（Azure 容器注册表中的映像）等。 如果未提供注册表，则默认为 khub.docker.com。      |
|          DockerDetails - imageTag             |         Docker 映像的标记名。 默认值为“最新”。     |
|  DockerDetails - credentials      |  用于访问专用 Docker 的凭据。 合作伙伴将提供凭据。   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU。 有关详细信息，请参阅[所有可用 Linux 虚拟机](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 <BR> <BR> 有效值包括“Small”、“ExtraLarge”、“Large”、“A8”、“A9”、“Medium”、“A5”、“A6”、“A7”、“STANDARD_D1”、“STANDARD_D2”、“STANDARD_D3”、“STANDARD_D4”、“STANDARD_D11”、“STANDARD_D12”、“STANDARD_D13”、“STANDARD_D14”、“A10”、“A11”、“STANDARD_D1_V2”、“STANDARD_D2_V2”、“STANDARD_D3_V2”、“STANDARD_D4_V2”、“STANDARD_D11_V2”、“STANDARD_D12_V2”、“STANDARD_D13_V2”、“STANDARD_D14_V2”、“STANDARD_G1”、“STANDARD_G2”、“STANDARD_G3”、“STANDARD_G4”、“STANDARD_G5”、“STANDARD_D5_V2”、“BASIC_A1”、“BASIC_A2”、“BASIC_A3”、“BASIC_A4”、“STANDARD_A1”、“STANDARD_A2”、“STANDARD_A3”、“STANDARD_A4”、“STANDARD_A5”、“STANDARD_A6”、“STANDARD_A7”、“STANDARD_A8”、“STANDARD_A9”、“STANDARD_A10”、“STANDARD_A11”、“STANDARD_D15_V2”、“STANDARD_F1”、“STANDARD_F2”、“STANDARD_F4”、“STANDARD_F8”、“STANDARD_F16”、“STANDARD_NV6”、“STANDARD_NV12”、“STANDARD_NV24”、“STANDARD_NC6”、“STANDARD_NC12”、“STANDARD_NC24”、“STANDARD_NC24r”、“STANDARD_H8”、“STANDARD_H8m”、“STANDARD_H16”、“STANDARD_H16m”、“STANDARD_H16mr”、“STANDARD_H16r”、“STANDARD_A1_V2”、“STANDARD_A2_V2”、“STANDARD_A4_V2”、“STANDARD_A8_V2”、“STANDARD_A2m_V2”、“STANDARD_A4m_V2”、“STANDARD_A8m_V2”、“STANDARD_M64ms”、“STANDARD_M128s”和“STANDARD_D2_V3”。 默认值为“STANDARD_D2_V2”。  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  每个批处理池的专用计算机节点数。 默认值为 1。 |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch 节点代理 SKU ID。 目前仅支持“batch.node.ubuntu 18.04”批处理节点代理。    |
| DockerDetails - partnerCredentials | 用于在 Docker 中调用合作伙伴 API 的凭据。 合作伙伴基于支持的授权机制提供此信息，例如用户名和密码，或 API 密钥。 |
| partnerType | “天气”。 FarmBeats 中的其他合作伙伴类型为“传感器”和“图像”。  |
|  name   |   FarmBeats 系统中合作伙伴的所需名称。   |
|  description |  说明。   |

## <a name="next-steps"></a>后续步骤

现在，你已从 Azure FarmBeats 实例查询到了传感器数据，接下来请了解如何为你的农场[生成映像](generate-maps-in-azure-farmbeats.md#generate-maps)。