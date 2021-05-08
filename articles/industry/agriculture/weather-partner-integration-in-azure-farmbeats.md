---
title: 天气应用合作伙伴集成
description: 了解天气数据提供程序如何与 FarmBeats 集成。
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147073"
---
# <a name="weather-partner-integration-with-farmbeats"></a>天气合作伙伴与 FarmBeats 集成

本文介绍了有关 Azure FarmBeats 连接器 Docker 组件 的信息。 作为天气数据提供商，用户可以使用连接器 Docker 与 FarmBeats 集成。 使用其 API 将天气数据发送到 FarmBeats。 在 FarmBeats 中，数据可用于数据合成，并用于构建机器学习模型或人工智能模型。

 > [!NOTE]
 > 在本文中，我们将使用[引用实现](https://github.com/azurefarmbeats/noaa_docker)，其通过使用 Azure 开放数据集以及美国国家海洋和大气管理局 (NOAA) 的天气数据构建。 我们还使用相应的 [Docker 映像](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)。

必须提供[合适的 docker 映像或程序](#docker-specifications)，并在客户可以访问的容器注册表中托管 Docker 映像。 向客户提供以下信息：

- Docker 映像 URL
- Docker 映像标记
- 用于访问 Docker 映像的密钥或凭据
- 用于从系统访问数据的客户特定 API 密钥或凭据
- VM SKU 详细信息（如果 Docker 映像具有特定的 VM 要求，则提供这些详细信息。 否则，客户可以从 Azure 中支持的 VM SKU 中进行选择。）

客户使用此 Docker 信息在其 FarmBeats 实例中注册天气合作伙伴。 有关客户如何使用 Docker 在 FarmBeats 中引入天气数据的详细信息，请参阅“[从天气合作伙伴获取数据](./get-weather-data-from-weather-partner.md)”。

## <a name="connector-docker-development"></a>连接器 Docker 开发

**基于 REST API 的集成**

FarmBeats API 包含 Swagger 技术文档。 有关 API 及其相应请求或响应的详细信息，请参阅“[FarmBeats Swagger](https://aka.ms/farmbeatsswagger)”。 

如果已安装 FarmBeats，请访问 FarmBeats Swagger，网址为 `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

请注意， *-api* 将追加到用户的 FarmBeats 网站名称。 API 终结点是 `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub lib

FarmBeats 提供了一个可以使用的 lib。 此 lib 当前作为[引用实现的一部分](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)提供。 之后，它将作为用于多种语言的 SDK 提供。

### <a name="authentication"></a>身份验证

**通过 FarmBeats API 进行身份验证**

FarmBeats 使用持有者身份验证。 可以通过在请求的标头部分提供访问令牌来访问 API。 下面是一个示例：

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

用户可以从在客户 FarmBeats 实例上运行的 Azure Functions 应用程序请求访问令牌。 系统会将 Azure Functions URL 提供给 Docker 程序作为参数。 可以通过对 URL 发出 `GET` 请求来获取访问令牌。 URL 中的响应包含访问令牌。 

使用 Datahub lib 中的 helper 函数获取访问令牌。 有关详细信息，请参阅“[NOAA Docker 映像的 GitHub 页面](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)”。

访问令牌仅在几个小时内有效。 过期后，必须再次请求。

**通过合作伙伴端 API 进行身份验证**

若要在 Docker 作业正在运行时通过合作伙伴端 API 进行身份验证，客户需要提供合作伙伴注册期间的凭据。 下面是一个示例：

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API 服务会将此 dict 串行化，并将其存储在[密钥保管库](../../key-vault/general/basic-concepts.md)中。

[Azure 数据工厂](../../data-factory/introduction.md)可用于协调天气作业。 其会旋转资源以运行 Docker 代码。 数据工厂还提供了一种机制，用于将数据安全地推送到运行 Docker 作业的 VM。 然后，系统会将 API 凭据安全地存储在密钥保管库中。 

凭据将以安全字符串的形式从密钥保管库中读取。 它们作为 Docker 容器的工作目录中的扩展属性提供。 它们的文件路径为 */mnt/working_dir/activity.json*。 

Docker 代码在运行时可读取 *activity.json* 中的凭据，以访问客户的合作伙伴端 API。 在 JSON 文件中，凭据如以下代码示例所示：

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
`partnerCredentials` 凭据以客户在合作伙伴注册期间相同的方式提供。

FarmBeats lib 可提供 helper 函数。 使用这些函数可从活动属性中读取凭据。 有关详细信息，请参阅“[NOAA Docker 映像的 GitHub 页面](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)”。

该文件仅在 Docker 代码运行时使用。 代码完成后，系统将删除该文件。

有关数据工厂管道和活动工作原理的详细信息，请参阅“[架构和数据类型映射](../../data-factory/copy-activity-schema-and-type-mapping.md)”。

**HTTP 请求标头**

下表显示了在对 FarmBeats 进行 API 调用时需要指定的最常见请求标头。

标头 | 说明和示例
--- | ---
Content-Type | 请求格式。 示例： `Content-Type: application/<format>` <br/>对于 FarmBeats 数据中心 API，格式为 JSON。 示例： ` Content-Type: application/json`
授权 | 进行 API 调用所需的访问令牌。 示例： `Authorization: Bearer <Access-Token>`
Accept | 响应格式。 对于 FarmBeats 数据中心 API，格式为 JSON。 示例： `Accept: application/json`

## <a name="data-format"></a>数据格式

JSON 是一种与语言无关的常见数据格式，该格式提供任意数据结构的简单文本表示形式。 有关详细信息，请参阅 [JSON.org](https://json.org)。

## <a name="docker-specifications"></a>Docker 规范

Docker 程序需要两个组件：启动和作业。 此程序可以具有多个作业。

### <a name="bootstrap"></a>Bootstrap

当客户在 FarmBeats 上开始 Docker 注册时，启动组件应处于运行状态。 以下参数（`arg1` 和 `arg2`）将传递给程序：

- **FarmBeats API 终结点**：API 请求的 FarmBeats API 终结点。 此终结点会对 FarmBeats 部署进行 API 调用。
- **Azure Functions URL**：用户自己的终结点。 此 URL 提供了 FarmBeats API 的访问令牌。 您可以对此 URL 上的 `GET` 进行调用以提取访问令牌。

启动会创建用户运行作业所需的元数据，从而获取天气数据。 有关详细信息，请参阅“[引用实现](https://github.com/azurefarmbeats/noaa_docker)”。 

如果自定义 *bootstrap_manifest.json* 文件，则引用启动程序将创建所需的元数据。 启动程序将创建以下元数据： 

 > [!NOTE]
 > 如果按照“[引用实现](https://github.com/azurefarmbeats/noaa_docker)”中所述更新 *bootstrap_manifest.json* 文件，则无需创建以下元数据。 启动程序将使用清单文件来创建必要的元数据。

- /**WeatherDataModel**：WeatherDataModel 元数据表示天气数据。 它对应于源提供的数据集。 例如，DailyForecastSimpleModel 可以每天提供一次平均温度、湿度和降水量信息。 与此相反，DailyForecastAdvancedModel 可能会为每小时粒度提供更多的详细信息。 用户可以创建任意数量的天气数据模型。
- /**JobType**：FarmBeats 具有可扩展的作业管理系统。 作为天气数据提供商，用户将拥有各种数据集和 API（例如 GetDailyForecasts）。 可以使用 JobType 在 FarmBeats 中启用这些数据集和 API。 创建作业类型后，客户可以触发该类型的作业，以获取其位置或其相关场的天气数据。 有关详细信息，请参阅“[FarmBeats Swagger](https://aka.ms/farmbeatsswagger)”中的 JobType 和 Job API。

### <a name="jobs"></a>作业

每次 FarmBeats 用户运行在启动过程中创建的 /JobType 作业时，系统都会调用作业组件。 作业的 Docker run 命令定义为用户所创建的 /JobType 的一部分。

作业从源提取数据并将其推送到 FarmBeats。 在启动过程中，应将获取数据所需的参数定义为 /JobType 的一部分。

作为作业的一部分，该程序必须根据在启动过程中创建的 /WeatherDataModel 创建 /WeatherDataLocation。 /WeatherDataLocation 对应于一个位置（纬度和经度坐标），用户提供该位置作为作业的参数。

### <a name="object-details"></a>对象详细信息

WeatherDataModel | 说明 |
--- | ---
名称  | 天气数据模型的名称。 |
说明  | 对模型的有意义说明。 |
属性  | 由数据提供程序定义的附加属性。 |
weatherMeasures > Name  | 天气度量值的名称。 例如，humidity_max。 |
weatherMeasures > DataType  | Double 或 Enum。 如果为 Enum，则需要 measureEnumDefinition。 |
weatherMeasures > measureEnumDefinition  | 仅当数据类型为 Enum 时才需要。 例如： `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
weatherMeasures > Type  | 天气遥测数据的类型。 例如，RelativeHumidity。 以下是系统定义的类型：AmbientTemperature、NoUnit、CO2、Depth、 ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、 Phosphate、PointInTime、Potassium、Pressure、RainGauge、RelativeHumidity、 Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、 UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、 Evapotranspiration 和PAR。 若要添加更多类型，请参阅本文中的“[添加 ExtendedType](#add-extendedtype)”部分。
weatherMeasures > Unit | 天气遥测数据的单位。 以下是系统定义的单位：NoUnit、Celsius、Fahrenheit、Kelvin, Rankine、Pascal、Mercury、PSI, MilliMeter、CentiMeter、Meter、Inch、Feet、Mile、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、Degree、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、Percentage、PartsPerMillion、MicroMole、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、Liter、MilliLiter、Seconds、UnixTimestamp、MicroMolePerMeterSquaredPerSecond 和 InchesPerHour。 若要添加更多单位，请参阅本文中的“[添加 ExtendedType](#add-extendedtype)”部分。
SensorMeasures > AggregationType  | 聚合类型。 可能的值为 None、Average、Maximum、Maximum、StandardDeviation、Sum 和 Total。
weatherMeasures > Depth  | 传感器的深度（以厘米为单位）。 例如，地下 10 厘米的湿度度量。
weatherMeasures > Description  | 对度量的有意义说明。 

JobType | 说明 |
--- | ---
名称  | 作业的名称。 例如，Get_Daily_Forecast。 客户将运行此作业以获取天气数据。|
pipelineDetails > parameters > name  | 参数的名称。 |
pipelineDetails > parameters > type | 参数类型。 可能的值包括 String、Int、Float、Bool 和 Array。 |
pipelineDetails > parameters > isRequired | 参数的布尔值。 如果参数是必需的，则值为 true。 否则，该值为 false。 默认值为 true。 |
pipelineDetails > parameters > defaultValue | 参数的默认值。 |
pipelineDetails > parameters > description | 参数的说明。 |
属性  | 制造商提供的其他属性。
Properties > programRunCommand | Docker 运行命令。 当客户运行天气作业时，此命令将运行。 |

WeatherDataLocation | 说明 |
--- | ---
weatherDataModelId  | 在启动过程中创建的相应 WeatherDataModel 的 ID。|
location  | 纬度、经度和高程。 |
名称 | 对象的名称。 |
说明 | 天气数据位置的说明。 |
farmId | 场 ID（可选）。 客户提供此 ID 作为作业参数的一部分。 |
属性  | 制造商提供的其他属性。

有关对象及其属性的详细信息，请参阅“[FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger)”。

> [!NOTE]
> API 会为创建的每个实例返回唯一 ID。 用于设备管理和元数据同步的转换器需要保留此 ID。

**元数据同步**

连接器 Docker 组件应该能够在元数据中发送更新。 例如，当天气提供商向数据集添加新参数或添加了新功能（如新的 30 天预测）时，它应发送更新。

> [!NOTE]
> 天气数据模型中的元数据不支持删除。
>
> 若要更新元数据，必须在天气数据模型上调用 `/Get/{ID}`。 更新已更改的属性，然后执行`/Put/{ID}`以保留用户设置的任何属性。

## <a name="weather-data-telemetry-specifications"></a>天气数据（遥测）规范

天气数据映射到一个规范消息，该消息将被推送到 Azure 事件中心进行处理。 Azure 事件中心是一个服务，可用于从连接的设备和应用程序引入实时数据（遥测）。 

若要将天气数据发送到 FarmBeats，请创建一个客户端，将消息发送到 FarmBeats 中的事件中心。 有关详细信息，请参阅“[将遥测数据发送到事件中心](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)”。

以下示例 Python 代码将遥测数据作为客户端发送到指定的事件中心。

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

下面是规范消息格式：

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

下面是遥测消息的示例：

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>故障排除和错误管理

### <a name="error-logging"></a>错误日志记录

合作伙伴作业可在现有作业框架中运行。 因此，对于其他预先存在的 FarmBeats 作业，错误之间的记录方式相同（如 GetFarmData 和 SensorPlacement）。 在数据工厂管道中运行的数据工厂活动将记录`STDERR`和`STDOUT`。 FarmBeats 资源组中的`datahublogs-xxx`存储帐户中提供了这两个文件。

Datahub lib 提供 helper 函数，用于启用日志记录作为总体 Datahub 日志的一部分。 有关详细信息，请参阅“[NOAA Docker 映像的 GitHub 页面](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)”。

### <a name="troubleshooting-and-support"></a>故障排除和支持

如果客户无法在 FarmBeats 实例中接收天气数据，请提供支持和用于排除此问题的机制。

## <a name="add-extendedtype"></a>添加 ExtendedType

FarmBeats 支持添加新的传感器度量值类型和单位。 您可以在 [引用实现](https://github.com/azurefarmbeats/noaa_docker)中更新 *bootstrap_manifest.json* 文件，从而添加新的单位或类型。

按照以下步骤添加新的 WeatherMeasure 类型，例如，PrecipitationDepth。

1. 使用查询 `filter - key = WeatherMeasureType` 发出关于 /ExtendedType 的 `GET` 请求。
2. 记下返回对象的 ID。
3. 将新类型添加到已返回对象的列表中。 使用以下新列表发出关于 /ExtendedType{ID} 的`PUT`请求。 输入有效负载应与前面收到的响应相同。 新单元应在值列表的末尾追加。

有关 /ExtendedType API 的详细信息，请参阅“[FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger)”。

## <a name="next-steps"></a>后续步骤

现在，用户便得到了一个与 FarmBeats 集成的连接器 Docker 组件。 接下来，了解如何使用 FarmBeats 中的 Docker 映像[获取天气数据](get-weather-data-from-weather-partner.md)。 
