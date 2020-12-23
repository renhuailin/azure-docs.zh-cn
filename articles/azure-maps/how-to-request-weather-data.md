---
title: '使用 Azure Maps 天气服务 (预览版来请求实时和预测天气数据) '
description: '了解如何使用 Microsoft Azure 地图天气服务 (预览版，请求实时 (当前) 和预测 (分钟、每小时、每日) 天气数据) '
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fe1b337fe3e1dcf499f9a7428f66543108d0c050
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680423"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services-preview"></a>使用 Azure Maps 天气服务 (预览版来请求实时和预测天气数据)  

> [!IMPORTANT]
> Azure Maps 天气服务目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Maps [天气服务](/rest/api/maps/weather) 是一组 RESTful api，使开发人员能够将高度动态的历史、实时和预测天气数据和可视化效果集成到其解决方案中。 在本文中，我们将向您展示如何请求实时和预测天气数据。

在本文中，你将学习如何：

* 使用 " [获取当前条件" API](/rest/api/maps/weather/getcurrentconditionspreview)请求实时 (当前) 天气数据。
* 使用 " [获取严重天气警报" API](/rest/api/maps/weather/getsevereweatheralertspreview)请求严重的天气警报。
* 使用 [获取日常预测 API](/rest/api/maps/weather/getdailyforecastpreview)请求每日预测。
* 使用 [获取每小时预测 API](/rest/api/maps/weather/gethourlyforecastpreview)请求每小时预测。
* 使用 [获取分钟预测 API](/rest/api/maps/weather/getminuteforecastpreview)请求每分钟预测的预测。

此视频提供了 Azure Maps 天气服务进行 REST 调用的示例。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

    >[!IMPORTANT]
    >[获取分钟预测 API](/rest/api/maps/weather/getminuteforecastpreview)需要 S1 定价层密钥。 所有其他 Api 都需要 S0 定价层密钥。

本教程使用 [Postman](https://www.postman.com/) 应用，但你也可以选择其他 API 开发环境。

## <a name="request-real-time-weather-data"></a>请求实时天气数据

" [获取当前条件" API](/rest/api/maps/weather/getcurrentconditionspreview) 返回给定坐标位置的详细天气情况，如降水量、温度和风。 此外，还可以检索过去6小时或24小时内特定位置的观察值。 响应包含如下所示的详细信息：观察日期和时间、天气条件的简要说明、天气图标、降水量指示器标志和温度。 还返回 RealFeel™温度和 ultraviolet (UV) 索引。

在此示例中，将使用 " [获取当前条件" API](/rest/api/maps/weather/getcurrentconditionspreview) 来检索位于华盛顿州西雅图的当前天气状况。

1. 打开 Postman 应用。 在 Postman 应用顶部附近，选择“新建”。 在“新建”窗口中，选择“集合”。  命名集合，然后选择“创建”按钮。 本文档中的示例的其余部分将使用此集合。

2. 若要创建请求，请再次选择“新建”。 在“新建”窗口中，选择“请求”。 在“请求名称”中，输入请求名称。 选择在上一步中创建的集合，然后选择“保存”。

3. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. 单击蓝色的 " **发送** " 按钮。 响应正文包含当前天气信息。

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>请求严重天气警报

[Azure Maps 获取严重天气警报 API](/rest/api/maps/weather/getsevereweatheralertspreview) 将返回官方政府种气象站机构和全球领先天气警报提供商提供的严重天气警报。 该服务可以返回有关请求位置的活动严重警报的详细信息，如警报类型、类别、级别和详细说明，例如飓风、thunderstorms、闪电、导热或林触发。 例如，后勤经理可以直观显示地图上的严重天气情况、业务位置和计划路线，并与驱动程序和本地工作人员进行进一步协调。

在此示例中，你将使用 " [获取严重天气警报" API](/rest/api/maps/weather/getsevereweatheralertspreview) 来检索位于 CHEYENNE，WY 中的坐标处的当前天气状况。

>[!NOTE]
>此示例将在撰写本文时检索严重的天气预报警报。 请求的位置上不再有任何严重的天气警报。 若要在运行此示例时检索实际的严重警报数据，需要在不同坐标位置检索数据。

1. 打开 Postman 应用，单击 " **新建**"，然后选择 " **请求**"。 在“请求名称”中，输入请求名称。 选择在上一部分中创建的集合，或创建一个新集合，然后选择 " **保存**"。

2. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 单击蓝色的 " **发送** " 按钮。 如果没有严重天气警报，响应正文将包含一个空 `results[]` 数组。 如果出现严重天气警报，响应正文将包含类似于下面的 JSON 响应：

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>请求每日天气预测数据

" [获取日常预测" API](/rest/api/maps/weather/getdailyforecastpreview) 返回详细的每日天气预报，如温度和风。 请求可以指定为给定坐标位置返回的天数：1、5、10、15、25或45天。 响应包括温度、风、降水量、空气质量和 UV 指数等详细信息。  在此示例中，我们通过设置请求5天 `duration=5` 。

>[!IMPORTANT]
>在 S0 定价层中，你可以请求接下来的1、5、10和15天的日常预测。 在 S1 定价层中，还可以请求未来25天和45天的每日预测。

在此示例中，您将使用 " [获取每日预测 API](/rest/api/maps/weather/getdailyforecastpreview) " 来检索位于华盛顿州西雅图的5天天气预报的坐标。

1. 打开 Postman 应用，单击 " **新建**"，然后选择 " **请求**"。 在“请求名称”中，输入请求名称。 选择在上一部分中创建的集合，或创建一个新集合，然后选择 " **保存**"。

2. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 单击蓝色的 " **发送** " 按钮。 响应正文包含5天天气预测数据。 为了简洁起见，下面的 JSON 响应显示第一天的预测。
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>请求每小时天气预测数据

" [获取每小时预测 API](/rest/api/maps/weather/gethourlyforecastpreview) " 将按小时返回详细的天气预报，按小时计算下1小时、12、24 (1 天) 、72 (3 天) 、120 (5 天) 和240小时 (给定坐标位置) 10 天。 API 返回温度、湿度、风、降水量和 UV 指数等详细信息。

>[!IMPORTANT]
>在 S0 定价层中，你可以请求下1小时、12小时、24小时 (1 天) 和72小时)  (3 天的每小时预测。 在 S1 定价层中，你还可以请求在下一 120 (5 天的每小时预测， (10) 天) 和240小时。

在此示例中，你将使用 " [获取每小时预测 API](/rest/api/maps/weather/gethourlyforecastpreview) " 来检索位于华盛顿州西雅图市的下12小时的每小时天气预报。

1. 打开 Postman 应用，单击 " **新建**"，然后选择 " **请求**"。 在“请求名称”中，输入请求名称。 选择在上一部分中创建的集合，或创建一个新集合，然后选择 " **保存**"。

2. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 单击蓝色的 " **发送** " 按钮。 响应正文包含接下来的12小时的天气预测数据。 为了简洁起见，下面的 JSON 响应显示第一个小时的预测。

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>请求每分钟天气预测数据

 " [获取分钟预测 API](/rest/api/maps/weather/getminuteforecastpreview) " 将为下一个120分钟返回给定位置的每分钟预测。 用户可以按1、5和15分钟的间隔请求天气预测。 响应包括详细信息，如降水量的类型 (，其中包括 rain、雪，或者) 、开始时间和降水量强度值混合 (dBZ) 。

在此示例中，你将使用 " [获取分钟预测 API](/rest/api/maps/weather/getminuteforecastpreview) " 来检索位于华盛顿州西雅图市的按分钟天气预报的天气预报。 天气预测将于接下来的120分钟提供。 查询请求按15分钟的间隔提供预测，但你可以将参数调整为1或5分钟。

1. 打开 Postman 应用，单击 " **新建**"，然后选择 " **请求**"。 在“请求名称”中，输入请求名称。 选择在上一部分中创建的集合，或创建一个新集合，然后选择 " **保存**"。

2. 在 "生成器" 选项卡中选择 " **获取** HTTP" 方法，然后输入以下 URL。 对于此请求和本文中提到的其他请求，请将 `{Azure-Maps-Primary-Subscription-key}` 替换为你的主订阅密钥。

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 单击蓝色的 " **发送** " 按钮。 响应正文包含接下来的120分钟的天气预测数据，时间间隔为15分钟。

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Maps 天气服务 (预览版) 概念](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [Azure Maps 天气服务 (预览版) REST API](/rest/api/maps/weather)