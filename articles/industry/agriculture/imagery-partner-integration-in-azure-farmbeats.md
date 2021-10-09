---
title: 图像合作伙伴集成
description: 本文介绍图像合作伙伴集成。
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 85dff004bdaf61297d9f88766e4cadc97f7d6b88
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624247"
---
# <a name="imagery-partner-integration"></a>图像合作伙伴集成

本文介绍如何使用 Azure FarmBeats 转换器组件将图像数据发送到 FarmBeats。 可以从各种来源（例如多光谱相机、卫星和无人机）生成农业图像数据。 农业图像合作伙伴可以与 FarmBeats 集成，为客户提供自定义生成的农场地图。

数据可用后，可通过 FarmBeats 加速器将其可视化，且农业企业或客户系统集成商还可能将其用于数据融合和机器学习/人工智能 (ML/AI) 模型生成。

通过 FarmBeats，可：

- 使用 /ExtendedType API 定义自定义图像类型、源和文件格式。
- 通过 /Scene 和 /SceneFile API 从各种源引入图像数据。

以下信息重点介绍如何将任何形式的图像添加到 FarmBeats 系统中。

选择“无人机图像”部分时，会弹出一个窗口，显示高分辨率的无人机正射图像。 可以访问合作伙伴软件，这有助于规划无人机飞行并获取原始数据。 你将继续使用合作伙伴的软件进行路径规划和正射图像拼接。

无人机合作伙伴需要使客户能够将客户帐户与 Azure 上的 FarmBeats 实例链接。

必须在无人机合作伙伴软件中使用以下凭据来链接 FarmBeats：

- API 终结点
- 租户 ID
- 客户端 ID
- 客户端机密

## <a name="api-development"></a>API 开发

API 包含 Swagger 技术文档。 有关 API 及对应的请求或响应的信息，请参阅 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

## <a name="authentication"></a>身份验证

FarmBeats 使用 Microsoft Azure [Active Directory](../../app-service/overview-authentication-authorization.md) (Azure AD)。  Azure 应用服务提供内置的身份验证和授权支持。 

有关 Azure AD 的详细信息，请参阅 [Azure Active Directory](../../app-service/overview-authentication-authorization.md)。   

FarmBeats 数据中心使用持有者身份验证，该验证方法需要以下凭据：

- 客户端 ID
- 客户端机密
- 租户 ID

使用前面的凭据，调用方可以请求访问令牌，该令牌需要在后续 API 请求中发送（标头部分中），如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

以下 Python 代码示例检索访问令牌。 然后，可以将令牌用于对 FarmBeats 的后续 API 调用。

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```

## <a name="http-request-headers"></a>HTTP 请求标头

下面是在对 FarmBeats 数据中心进行 API 调用时需要指定的最常见请求标头。

**标头** | **说明和示例**
--- | ---
Content-Type  | 请求格式 (Content-Type: application/\<format\>)。 对于 FarmBeats 数据中心 API，格式为 JSON。 Content-Type: application/json
授权 | 指定进行 API 调用所需的访问令牌。 授权：持有者 \<Access-Token\>
Accept  | 响应格式。 对于 FarmBeats 数据中心 API，格式为 JSON。 Accept: application/json


## <a name="api-requests"></a>API 请求

若要发出 REST API 请求，需结合以下各项：

- HTTP 方法（GET、POST 和 PUT）。
- 指向 API 服务的 URL。
- 资源 URI（用于查询、提交数据、更新或删除）。
- 一个或多个 HTTP 请求头。

或者，可在 GET 调用中包含查询参数以筛选数据、限制数据的大小，并对响应中的数据进行排序。

下面的示例请求获取设备列表：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>"
```

大多数 GET、POST 和 PUT 调用都需要 JSON 请求正文。

下面的示例请求是要创建设备。 此示例包含一个带有请求正文的输入 JSON。


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"accept: application/json" -H
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>数据格式

JSON 是一种与语言无关的常见数据格式，该格式提供任意数据结构的简单文本表示形式。 有关详细信息，请参阅 [JSON org](https://JSON.org)。

## <a name="ingest-imagery-into-farmbeats"></a>将图像引入 FarmBeats

合作伙伴具有连接到 FarmBeats Datahub 的凭据后，合作伙伴在转换器组件中执行以下步骤。

1. 根据要上传的图像的类型，为以下字段创建新的扩展类型：

   - 场景源：例如 drone_partner_name
   - 场景类型：例如无人机
   - 场景文件类型：例如叶绿素指数
   - 场景文件内容类型：例如 image/tiff

2. 调用 /Farms API，从 Azure FarmBeats 系统中获取农场列表。

3. 让客户能够从农场列表中选择单个农场。

   合作伙伴系统必须在合作伙伴软件中显示该农场，才能执行路径规划及无人机飞行和图像收集。

4. 调用 /Scene API 并提供所需的详细信息，以创建具有唯一场景 ID 的新场景。

5. 接收一个 blob SAS URL，以将所需的图像上传到 FarmBeats 系统中所选农场的上下文中的 FarmBeats Datahub 中。

下面是有关 API 调用的详细流。

### <a name="step-1-extendedtype"></a>步骤 1：ExtendedType

签入 /ExtendedType API，以查看类型和文件源在 FarmBeats 上是否可用。 为此，请对 /ExtendedType API 调用 GET。

以下是系统定义的值：

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

此步骤是一次性设置。 此新场景类型的范围仅限于在其中安装了 Azure FarmBeats 的订阅。

例如，若要添加 SceneSource: “SlantRange”，请使用键“SceneSource”输入有效负载对 /ExtendedType API 的 ID 执行 PUT。

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

绿色字段是系统定义的场景源值的新添加项。

### <a name="step-2-get-farm-details"></a>步骤 2：获取农场详细信息

场景（.tiff 或 .csv 文件）以一个农场为背景。 你需要通过对 /Farm API 执行 GET 来获取农场详细信息。 API 返回 FarmBeats 中可用的农场列表。 你可以选择要为之引入数据的农场。

GET /Farm 响应：

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>步骤 3：创建场景 ID（POST 调用）

使用给定的信息（提供了与场景相关联的日期、序列和农场 ID）创建一个新的场景（.tiff 或 .csv 文件）。 可以在属性下定义与场景关联的元数据，其中包括持续时间和度量类型。

创建新场景会创建与农场关联的新场景 ID。 创建场景 ID 后，用户可以使用该 ID 创建新的文件（.tiff 或 .csv）并存储文件的内容。

对 /Scene API 的 POST 调用的示例输入有效负载：

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API 响应：

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

创建场景文件

步骤 3 中返回的场景 ID 是场景文件的输入。 场景文件返回一个 SAS URL 令牌，该令牌的有效期为 24 小时。

如果用户需要以编程方式上传图像流，可以使用 blob 存储 SDK，通过场景文件 ID、位置和 URL 来定义方法。

对 /SceneFile API 的 POST 调用的示例输入有效负载：

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API 响应：

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

对 /SceneFile API 的 POST 调用将返回 SAS 上传 URL，该 URL 可用于通过 Azure Blob 存储客户端或库上传 .csv 或 .tiff 文件。


## <a name="next-steps"></a>后续步骤

有关基于 REST API 的集成的详细信息，请参阅 [REST API](rest-api-in-azure-farmbeats.md)。