---
title: 如何使用 Azure Maps 空间 IO 模块 | Microsoft Azure Maps
description: 了解如何使用 Azure Maps Web SDK 提供的空间 IO 模块。 此模块提供了强大的功能，使开发者能够轻松地将空间数据与 Azure Maps Web SDK 相集成。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: ecafca0e337aa23223ca60e594cd8b2aece5ac57
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751436"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>如何使用 Azure Maps 空间 IO 模块

Azure Maps Web SDK 提供 **空间 IO 模块**，该模块通过使用 JavaScript 或 TypeScript，将空间数据与 Azure Maps Web SDK 相集成。 此模块具有强大功能，使开发者能够：

- [读取和写入通用空间数据文件](spatial-io-read-write-spatial-data.md)。 受支持的文件格式包括：KML、KMZ、GPX、GeoRSS、GML、GeoJSON，以及包含了空间信息相关列的 CSV 文件。 还支持已知文本 (WKT)。
- [连接到开放地理空间信息联盟 (OGC) 服务并与 Azure Maps WEB SDK 集成。作为地图上的图层覆盖 Web 地图服务 (WMS) 和 Web 地图图块服务 (WMTS)](spatial-io-add-ogc-map-layer.md)。
- [ 在 Web 地理要素服务 (WFS) 中查询数据](spatial-io-connect-wfs-service.md)。
- [覆盖包含样式信息的复杂数据集，并让它们使用最少的代码自动呈现](spatial-io-add-simple-data-layer.md)。
- [利用高速 XML 和带分隔符的文件读取器和编写器类](spatial-io-core-operations.md)。

在本指南中，我们将了解如何在 Web 应用程序中集成和使用空间 IO 模块。

此视频提供了 Azure Maps Web SDK 中空间 IO 模块的概述。

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player?format=ny]

> [!WARNING]
> 仅使用可信来源的数据和服务，尤其是从另一个域进行引用时更应如此。 空间 IO 模块会执行一定措施来最大程度地降低风险，不过最安全的方法还是从一开始就不允许任何危险数据进入应用程序。 

## <a name="prerequisites"></a>先决条件

在使用空间 IO 模块之前，需要先[创建一个 Azure Maps 帐户](./quick-demo-map-app.md#create-an-azure-maps-account)，并[获取帐户的主要订阅密钥](./quick-demo-map-app.md#get-the-primary-key-for-your-account)。

## <a name="installing-the-spatial-io-module"></a>安装空间 IO 模块

可以使用以下两个选项之一，加载 Azure Maps 空间 IO 模块：

* 用于 Azure Maps 空间 IO 模块的全局托管 Azure CDN。 对于此选项，请在 HTML 文件的 `<head>` 元素中添加对 JavaScript 的引用。

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* 可在本地加载 [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) 的源代码，然后由应用进行托管。 此程序包还包括了 TypeScript 定义。 对于此选项，请使用以下命令来安装包：

    ```sh
    npm install azure-maps-spatial-io
    ```

    然后在 HTML 文档的 `<head>` 元素中添加对 JavaScript 的引用：

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>使用空间 IO 模块

1. 创建新的 HTML 文件。

2. 加载 Azure Maps Web SDK 并初始化地图控件。 有关详细信息，请参阅 [Azure Maps 地图控件](./how-to-use-map-control.md)指南。 完成此步骤后，HTML 文件应如下所示：

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. 加载 Azure Maps 空间 IO 模块。 对于本练习，请使用针对 Azure Maps 空间 IO 模块的 CDN。 向 HTML 文件的 `<head>` 元素添加以下引用：

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. 初始化 `datasource`，并将数据源添加到地图上。 初始化 `layer`，并将数据源添加到地图层。 然后，呈现数据源和图层。 在继续向下滚动以查看完整代码之前，请先考虑好放置数据源和图层代码片段的最佳位置。 请注意，在以编程方式操作地图之前，应等待地图资源准备就绪。

    ```javascript
    var datasource, layer;
    ```

    和

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. 上述操作都完成后，HTML 代码应如下面代码所示。 此示例演示了如何从 URL 读取 XML 文件。 然后，在地图上加载并显示该文件的地理要素数据。 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. 记得将 `<Your Azure Maps Key>` 替换为主密钥。 打开 HTML 文件，显示结果应类似于下图所示：

    <center>

    ![空间数据示例](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>后续步骤

此处演示的地理要素只是空间 IO 模块中的众多地理要素之一。 阅读下面的指南，了解如何使用空间 IO 模块中的其他功能：

> [!div class="nextstepaction"]
> [添加简单的数据层](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [读取和写入空间数据](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [添加 OGC 地图层](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [连接到 WFS 服务](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心操作](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)

请参阅 Azure Maps 空间 IO 文档：

> [!div class="nextstepaction"]
> [Azure Maps 空间 IO 包](/javascript/api/azure-maps-spatial-io/)