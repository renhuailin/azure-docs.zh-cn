---
title: 实现 Azure Maps Creator 室内定位的动态样式
description: 了解如何实现 Creator 室内定位的动态样式
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/20/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 05e169667067033428d5fc995af4d866dc46d20b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751434"
---
# <a name="implement-dynamic-styling-for-creator-indoor-maps"></a>实现 Creator 室内定位的动态样式

你可以使用 Azure Maps Creator [特征状态服务](/rest/api/maps/v2/feature-state)，应用基于室内定位数据特征动态属性的样式。  例如，可以使用特定颜色来渲染设施会议室，以反映占用状态。 本文介绍如何使用[特征状态服务](/rest/api/maps/v2/feature-state)和[室内 Web 模块](how-to-use-indoor-module.md)动态渲染室内定位特征。

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。
3. [创建 Creator 资源](how-to-manage-creator.md)
4. 下载[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。
5. [创建室内定位](tutorial-creator-indoor-maps.md)，以获取 `tilesetId` 和 `statesetId`。
6. 按照[如何使用“室内定位”模块](how-to-use-indoor-module.md)中的步骤操作，以生成 Web 应用。

本教程使用 [Postman](https://www.postman.com/) 应用，但你也可以选择其他 API 开发环境。

## <a name="implement-dynamic-styling"></a>实现动态样式

满足先决条件后，应该使用订阅密钥、`tilesetId` 和 `statesetId` 配置一个简单的 Web 应用。

### <a name="select-features"></a>选择特征

若要实现动态样式，必须按特征 `id` 引用特征（比如会议室）。 可以使用特征 `id` 更新相应特征的动态属性或状态。 若要查看数据集中定义的特征，可以使用以下方法之一：

* WFS API（Web 特征服务）。 可以使用 [WFS API](/rest/api/maps/v2/wfs) 查询数据集。 WFS 遵循[开放地理空间信息联盟 API 功能](http://docs.opengeospatial.org/DRAFTS/17-069r1.html)。 WFS API 有助于查询数据集中的特征。 例如，可以使用 WFS 查找特定设施和楼层的所有中型会议室。

* 实现自定义代码，让用户能够使用 Web 应用选择定位的特征。 本文使用此选项。  

下面的脚本实现鼠标单击事件。 此代码根据单击点来检索特征 `id`。 在应用中，可以在室内管理器代码块之后插入此代码。 运行应用，然后检查控制台，以获取单击点的特征 `id`。

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

[创建室内定位](tutorial-creator-indoor-maps.md)教程将特征状态集配置为接受 `occupancy` 的状态更新。

在下一部分，我们将办公室 `UNIT26` 的占用状态设置为 `true`，将办公室 `UNIT27` 的占用状态设置为 `false`。

### <a name="set-occupancy-status"></a>设置占用状态

 现在要更新 `UNIT26` 和 `UNIT27` 这两间办公室的状态：

1. 在 Postman 应用中，选择“新建”。

2. 在“新建”窗口中，选择“HTTP 请求” 。

3. 在“请求名称”中输入请求名称，比如 POST Data Upload。

4. 在[特征更新状态 API](/rest/api/maps/v2/feature-state/update-states) 中输入以下 URL（将 `{Azure-Maps-Primary-Subscription-key}` 替换为主订阅密钥，将 `statesetId` 替换为 `statesetId`）：

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT26?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. 选择“标头”选项卡  。

6. 在“键”字段中，选择 `Content-Type`。 在“值”字段中，选择 `application/json`。

     :::image type="content" source="./media/indoor-map-dynamic-styling/stateset-header.png"alt-text="用于创建状态集的“标头”选项卡信息。":::

7. 选择“正文”选项卡。

8. 在下拉列表中，选择“原始”和“JSON”。

9. 复制以下 JSON 样式，然后将其粘贴到“正文”窗口中：

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!IMPORTANT]
    >只有当 POST 请求的时间戳晚于同一特征 `ID` 的上一特征状态更新请求的时间戳时，才会保存更新。

10. 通过将 `UNIT26` 替换为 `UNIT27`，更改步骤 7 中使用的 URL：

    ```http
    https://us.atlas.microsoft.com/featurestatesets/{statesetId}/featureStates/UNIT27?api-version=2.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

11. 复制以下 JSON 样式，然后将其粘贴到“正文”窗口中：

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2020-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>可视化定位上的动态样式

之前在浏览器中打开的 Web 应用现在应该会反映定位特征的已更新状态：
- 办公室 `UNIT27`(142) 应显示为绿色。
- 办公室 `UNIT26`(143) 应显示为红色。

![可用会议室显示为绿色，被占用的会议室显示为红色](./media/indoor-map-dynamic-styling/room-state.png)

[观看实时演示](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请阅读以下文章：

> [!div class="nextstepaction"]
> [适用于室内定位的 Creator](creator-indoor-maps.md)

有关本文中提到的 API，请参阅以下参考资料：

> [!div class="nextstepaction"]
> [数据上传](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [数据转换](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [数据集](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [图块集](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [特征状态集](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS 服务](creator-indoor-maps.md#web-feature-service-api)
