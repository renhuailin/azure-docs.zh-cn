---
title: 教程：通过 Microsoft Azure Maps 使用 Azure Notebooks (Python) 规划电动车路线
description: 本教程介绍如何使用 Microsoft Azure Maps 路线规划 API 和 Azure Notebooks 规划电动车路线
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 72fc6a96b588f3ca897fe69054e28029b34f2688
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733418"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>教程：使用 Azure Notebooks 规划电动车路线 (Python)

Azure Maps 是原生与 Azure 集成的地理空间服务 API 组合。 开发人员、企业和 ISV 可以使用这些 API 开发位置感知应用、IoT、移动、物流与资产跟踪解决方案。 

可以从 Python 和 R 等语言调用 Azure Maps REST API，以实现地理空间数据分析和机器学习方案。 Azure Maps 提供一组可靠的[路线 API](/rest/api/maps/route)，让用户根据各种条件计算多个数据点之间的路线。 计算基于各种条件，例如车辆类型或可抵达区域。 

本教程介绍如何为遇到电量不足情况的电动车司机提供帮助。 该司机需要找到离当前车辆位置最近的充电站。

在本教程中，将：

> [!div class="checklist"]
> * 在云中的 [Azure Notebooks](https://notebooks.azure.com) 上创建并运行 Jupyter Notebook 文件。
> * 在 Python 中调用 Azure Maps REST API。
> * 根据电动车的耗电模型搜索可抵达的范围。
> * 在可抵达范围（或等时线）内搜索电动车充电站。
> * 在地图上呈现可抵达范围的边界和充电站。
> * 基于驾驶时间查找并直观显示最近的电动车充电站的路线。

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)，并[选择第二代或 S1 定价层](choose-pricing-tier.md)。
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](how-to-manage-authentication.md)。


## <a name="create-an-azure-notebooks-project"></a>创建 Azure Notebooks 项目

若要遵循本教程，需要创建一个 Azure Notebooks 项目，然后下载并运行 Jupyter Notebook 文件。 该 Jupyter Notebook 文件包含用于实现本教程中的方案的 Python 代码。 若要创建 Azure Notebooks 项目并将 Jupyter Notebook 文档上传到其中，请执行以下步骤：

1. 转到 [Azure Notebooks](https://notebooks.azure.com) 并登录。 有关详细信息，请参阅[快速入门：登录并设置用户 ID](https://notebooks.azure.com)。
1. 在公共个人资料页的顶部，选择“我的项目”。 

    ![“我的项目”按钮](./media/tutorial-ev-routing/myproject.png)

1. 在“我的项目”页上，选择“新建项目”。  
 
   ![“新建项目”按钮](./media/tutorial-ev-routing/create-project.png)

1. 在“创建新项目”窗格中，输入项目名称和项目 ID。 
 
    ![“创建新项目”窗格](./media/tutorial-ev-routing/create-project-window.png)

1. 选择“创建”  。

1. 创建项目后，从 [Azure Maps Jupyter Notebook 存储库](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)下载此 [Jupyter Notebook 文档文件](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb)。

1. 在“我的项目”页上的项目列表中选择你的项目，然后选择“上传”以上传 Jupyter Notebook 文档文件。  

    ![上传 Jupyter Notebook](./media/tutorial-ev-routing/upload-notebook.png)

1. 上传计算机中的文件，然后选择“完成”。 

1. 成功完成上传后，项目页中会显示你的文件。 双击该文件，将其作为 Jupyter Notebook 打开。

尝试了解 Jupyter Notebook 文件中实现的功能。 以每次一个单元的形式运行 Jupyter Notebook 文件中的代码。 可以选择 Jupyter Notebook 应用顶部的“运行”按钮来运行每个单元中的代码。

  ![“运行”按钮](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>安装项目级包

若要运行 Jupyter Notebook 中的代码，请执行以下步骤，以便在项目级别安装包：

1. 从 [Azure Maps Jupyter Notebook 存储库](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)下载 [requirements.txt](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) 文件，并将其上传到自己的项目。
1. 在项目仪表板上，选择“项目设置”  。 
1. 在“项目设置”窗格中，依次选择“环境”选项卡、“添加”。   
1. 在“环境设置步骤”下执行以下操作：    
    a. 在第一个下拉列表中，选择“Requirements.txt”。   
    b. 在第二个下拉列表中，选择“requirements.txt”文件。   
    c. 在第三个下拉列表中，选择“Python 版本 3.6”作为版本。 
1. 选择“保存”。 

    ![安装包](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>加载所需的模块和框架

若要加载全部所需的模块和框架，请运行以下脚本。

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>请求可抵达的范围边界

包裹递送公司在其车队中添置了一些电动车。 在这一天，电动车必须能够在不返回仓库的情况下完成充电。 每当剩余电量不足一小时的行程时，你需要搜索可抵达范围内的一系列充电站。 实质上，每当电池电量不足时，就要搜索充电站。 同时，需要获取充电站分布范围的边界信息。 

由于公司倾向于使用经济性和速度均衡的路线，因此请求的线路类型为“经济”。  以下脚本调用 Azure Maps 路由服务的[获取路线范围 API](/rest/api/maps/route/getrouterange)。 它使用参数来表示车辆的耗电模型。 然后，该脚本分析响应以创建 geojson 格式的多边形对象来表示汽车的最大可抵达范围。

若要确定电动车可抵达范围的边界，请运行以下单元中的脚本：

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>在可抵达范围内搜索电动车充电站

获取电动车的可抵达范围（等时线）后，可以在该范围内搜索充电站。 

以下脚本调用 Azure Maps 的[在几何结构内发布搜索 API](/rest/api/maps/search/postsearchinsidegeometry)。 它在汽车的最大可抵达范围边界内搜索电动车充电站。 然后，脚本将响应分析成可抵达位置的数组。

若要在可抵达范围内搜索电动车充电站，请运行以下脚本：

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>将可抵达范围和充电站上传到 Azure Maps 数据服务

你需要在地图上直观显示电动车的充电站和最大可抵达范围边界。 为此，请将边界数据和充电站数据作为 geojson 对象上传到 Azure Maps 数据服务。 可使用[数据上传 API](/rest/api/maps/data-v2/upload-preview)。 

若要将边界和充电站数据上传到 Azure Maps 数据服务，请运行以下两个单元：

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data service.
uploadRangeResponse = await session.post("https://us.atlas.microsoft.com/mapData?subscription-key={}&api-version=2.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data service.
uploadPOIsResponse = await session.post("https://us.atlas.microsoft.com/mapData?subscription-key={}&api-version=2.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>在地图上呈现充电站和可抵达范围

将数据上传到数据服务后，调用 Azure Maps 的[获取地图图像服务](/rest/api/maps/render/getmapimage)。 此服务可运行以下脚本，在静态地图图像上呈现充电站和最大可抵达边界：

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![显示位置范围的地图](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>查找最佳充电站

首先需要确定可抵达范围内的所有可能的充电站。 然后需要知道在最短时间内可以抵达其中的哪个充电站。 

以下脚本调用 Azure Maps 的[矩阵路线 API](/rest/api/maps/route/postroutematrix)。 它会返回指定车辆的位置、行程时间，以及与每个充电站之间的距离。 下一个单元中的脚本将分析响应，以找到在给定时间内可抵达的最近充电站。

若要查找可在最短时间内抵达的最近充电站，请运行以下单元中的脚本：

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>计算到最近充电站的路线

找到最近的充电站后，可以调用[获取路线方向 API](/rest/api/maps/route/getroutedirections)，请求从电动车当前位置到该充电站的详细路线。

若要获取充电站的路线并分析响应以创建表示路线的 geojson 对象，请运行以下单元中的脚本：

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>直观显示路线

为了帮助直观显示路线，首先请将路线数据作为 geojson 对象上传到 Azure Maps 数据服务。 为此，可使用 Azure Maps 的[数据上传 API](/rest/api/maps/data-v2/upload-preview)。 然后调用呈现服务[获取地图图像 API](/rest/api/maps/render/getmapimage)，在地图上呈现并直观显示路线。

若要获取地图上呈现的路线图像，请运行以下脚本：

```python
# Upload the route data to Azure Maps Data service .
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData?subscription-key={}&api-version=2.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![显示路线的地图](./media/tutorial-ev-routing/route.png)

本教程已介绍如何使用 Python 直接调用 Azure Maps REST API 和直观显示 Azure Maps 数据。

若要了解本教程中使用的 Azure Maps API，请参阅：

* [获取路线范围](/rest/api/maps/route/getrouterange)
* [在几何结构中发布搜索](/rest/api/maps/search/postsearchinsidegeometry)
* [数据上传](/rest/api/maps/data-v2/upload-preview)
* [呈现器 - 获取地图图像](/rest/api/maps/render/getmapimage)
* [发布路线矩阵](/rest/api/maps/route/postroutematrix)
* [获取路线方向](/rest/api/maps/route/getroutedirections)
* [Azure Maps REST API](./consumption-model.md)

## <a name="clean-up-resources"></a>清理资源

没有需要清理的资源。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Notebooks，请参阅：

> [!div class="nextstepaction"]
> [Azure Notebook](https://notebooks.azure.com)
