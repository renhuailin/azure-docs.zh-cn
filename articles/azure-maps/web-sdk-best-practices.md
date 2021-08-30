---
title: Azure Maps Web SDK 最佳做法 | Microsoft Azure Maps
description: 了解优化使用 Azure Maps Web SDK 的提示和技巧。
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 630b354768e3ad0882911f11a99cca06fc87154e
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112234422"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Azure Maps Web SDK 最佳做法

本文档重点介绍 Azure Maps Web SDK 的最佳做法，不过，许多概述的最佳做法和优化可以应用于所有其他 Azure Maps SDK。

Azure Maps Web SDK 提供了一个功能强大的画布，用于以多种不同的方式呈现大空间数据集。 在某些情况下，可以采用多种方法来以相同的方式呈现数据，但根据数据集的大小和所需的功能，一种方法可能比其他方法更好。 本文重点介绍最佳做法以及提示和技巧来最大程度地提高性能并创建流畅的用户体验。

通常，当想要改进地图的性能时，要查找能够减少层和源的数量以及减少所使用数据集和呈现样式的复杂性的方法。

## <a name="security-basics"></a>安全性基本常识

应用程序的一个最重要的部分是其安全性。 不管用户体验有多好，如果应用程序不安全，黑客可能会破坏任何应用程序。 下面是一些保证 Azure Maps 应用程序安全的提示。 使用 Azure 时，请确保熟悉可用的安全工具。 请参阅本文档，了解 [Azure 安全性简介](../security/fundamentals/overview.md)。

> [!IMPORTANT]
> Azure Maps 提供两种身份验证方法。
> * 基于订阅密钥的身份验证
> * Azure Active Directory 身份验证在所有生产应用程序中使用 Azure Active Directory。
> 基于订阅密钥的身份验证很简单，大多数地图平台处于计费目的用它作为一种简易方法来度量平台的使用情况。 但是，这不是一种安全的身份验证形式，应该仅在开发应用时在本地使用。 一些平台提供了在请求中限制哪些 IP 地址和/或 HTTP 引荐者的功能，但是，此信息很容易伪装。 如果确实要使用订阅密钥，请务必[定期轮换](how-to-manage-authentication.md#manage-and-rotate-shared-keys)。
> Azure Active Directory 是一种企业标识服务，为各种类型的应用程序方案提供大量安全功能和设置选择。 Microsoft 建议所有使用 Azure Maps 的生产应用程序都使用 Azure Active Directory 进行身份验证。
> 详细了解本文档中的[管理 Azure Maps 中的身份验证](how-to-manage-authentication.md)。

### <a name="secure-your-private-data"></a>保护专用数据

将数据添加到 Azure Maps 交互式地图 SDK 时，它将在最终用户的设备上本地呈现，并且不会出于任何原因回发到 internet。

如果你应用程序正在加载不应公开访问的数据，请确保将数据存储在安全的位置，以安全的方式访问，并且该应用程序本身已锁定并仅供你想要的用户使用。 如果跳过这些步骤中的任何一个，则未经授权的人员可能会访问此数据。 Azure Active Directory 可以帮助锁定此问题。

请参阅教程[向 Azure 应用服务上运行的 Web 应用添加身份验证](../app-service/scenario-secure-app-authentication-app-service.md)

### <a name="use-the-latest-versions-of-azure-maps"></a>使用最新版本的 Azure Maps

Azure Maps SDK 会进行常规安全测试，以及检查 SDK 可能使用的任何外部依赖项库。 任何已知的安全问题都是及时修补并发布到生产应用。 如果应用程序指向托管版本的 Azure Maps Web SDK 的最新主版本，则它将自动接收包含与安全相关的修补程序的所有次要版本更新。

如果通过 NPM 模块自托管 Azure Maps Web SDK，请确保将插入 (^) 符号与 `package.json` 文件中的 Azure Maps NPM 包版本号一起使用，以便其始终指向最新的次要版本。

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>优化初始地图加载

在加载网页时，想要做的第一件事就是尽快开始呈现一些内容，以便用户不会盯着一个空白屏幕。

### <a name="watch-the-maps-ready-event"></a>观看地图就绪事件

同样，当地图最初加载时，通常需要尽快加载数据，因此，用户不会看到空的地图。 由于地图会以异步方式加载资源，因此，必须等到地图准备好进行交互，然后再尝试在其上呈现自己的数据。 可以等待两个事件：`load` 事件和 `ready` 事件。 地图完全完成加载初始地图视图并已加载每一个地图磁贴后，将激发加载事件。 当与地图进行交互所需的最小地图资源加载后，将激发就绪事件。 就绪事件通常会在加载事件一半的时间内激发，从而可以更快地开始将数据加载到地图中。

### <a name="lazy-load-the-azure-maps-web-sdk"></a>延迟加载 Azure Maps Web SDK

如果不立即需要地图，可以延迟到需要时加载 Azure Maps Web SDK。 这会延迟到需要时加载 Azure Maps Web SDK 使用的 JavaScript 和 CSS 文件。 发生这种情况的常见情形是，在页面加载时未显示的选项卡或浮出控件面板中加载地图。
下面的代码示例演示如何延迟到按钮按下之时加载 Azure Maps Web SDK。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="延迟加载地图" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的 <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>延迟加载地图</a> Pen。
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>为地图添加占位符

如果地图因网络限制或应用程序中的其他优先级需要等待一段时间加载，请考虑将小背景图像作为地图的占位符添加到地图 `div`。 这会在加载时填充地图 `div` 的空白。

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>在初始化时设置初始地图样式和相机选项

应用通常需要将地图加载到特定位置或样式。 有时，开发人员会等待地图加载完成（或等待 `ready` 事件），然后使用地图的 `setCemer` 或 `setStyle` 函数。 这通常需要更长的时间才能进入所需的初始地图视图，因为在加载所需地图视图需要的资源之前，默认情况下会加载许多资源。 更好的方法是在地图初始化时将所需的地图相机和样式选项传入到地图中。

## <a name="optimize-data-sources"></a>优化数据源

Web SDK 有两个数据源，

* “GeoJSON 源”：称为 `DataSource` 类，本地管理 GeoJSON 格式的原始位置数据。 适合中小型数据集（数十万以上功能）。
* “矢量磁贴源”：称为 `VectorTileSource` 类，基于地图磁贴系统，为当前的地图视图加载矢量磁贴格式的数据。 适合大型乃至超大型数据集（数百万或数十亿功能）。

### <a name="use-tile-based-solutions-for-large-datasets"></a>将基于磁贴的解决方案用于大型数据集

如果处理包含数百万种功能的较大数据集，要实现最优性能，建议使用服务器端解决方案（例如矢量或光栅图像磁贴服务）来公开数据。  
矢量磁贴经过优化，只加载视图中的数据，其中的几何图形剪裁到磁贴的焦点区域，并通用化以针对磁贴的缩放级别与地图的分辨率相匹配。

[Azure Maps Creator 平台](creator-indoor-maps.md)提供了以矢量磁贴格式检索数据的功能。 其他数据格式可以使用 [Tippecanoe](https://github.com/mapbox/tippecanoe) 等工具，也可以使用[此页上的资源列表](https://github.com/mapbox/awesome-vector-tiles)中的一个。

还可以创建自定义服务，在服务器端将数据集呈现为光栅图像磁贴，并使用地图 SDK 中的 TileLayer 类加载数据。 这提供了极佳的性能，因为地图只需要加载和管理最多几十个图像。 不过，由于原始数据在本地不可用，因此使用光栅磁贴存在一些限制。 通常需要辅助服务来支持任何类型的交互体验，例如，查找用户所单击的形状。 此外，光栅磁贴的文件大小通常大于包含通用化和缩放级别优化几何图形的压缩矢量磁贴。

详细了解[创建数据源](create-data-source-web-sdk.md)文档中的数据源。

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>将多个数据集合并为单个矢量磁贴源

地图需要管理的数据源越少，处理所有要显示的功能的速度就越快。 具体而言，当涉及磁贴源时，将两个矢量磁贴源合并在一起会将检索磁贴的 HTTP 请求数削减一半，并且数据总量将略小，因为只有一个文件头。

可以使用 [Tippecanoe](https://github.com/mapbox/tippecanoe) 等工具来将多个数据集合并在单个矢量磁贴源中。 数据集可以合并为单一功能集合，或分隔到矢量磁贴中称为源层的分隔层中。 将矢量磁贴源连接到呈现层时，可以指定包含要与层一起呈现的数据的源层。

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>减少由于数据更新而导致的画布刷新次数

可以通过多种方式来添加或更新 `DataSource` 类中的数据。 下面列出了不同的方法和一些注意事项来确保良好的性能。

* 数据源 `add` 函数可用于向数据源添加一个或多个功能。 每次调用此函数，它都会触发地图画布刷新。 如果添加许多功能，请将它们合并成数组或功能集合并一次性传递到此函数中，而不是循环访问数据集并针对每个功能调用此函数。
* 数据源 `setShapes` 函数可用于覆盖数据源中的所有形状。 这种情况下，它将数据源 `clear` 和 `add` 函数合并在一起，并执行单次地图画布刷新而不是两次，这将快得多。 如果要更新一个数据源中的所有数据，请务必使用此函数。
* 数据源 `importDataFromUrl` 函数可用于通过 URL 将 GeoJSON 文件加载到数据源中。 下载数据后，数据会传递到数据源 `add` 函数中。 如果 GeoJSON 文件托管在不同的域中，请确保其他域支持跨域请求 (CORs)。 如果不支持，请考虑将数据复制到域中的本地文件或创建启用了 CORs 的代理服务。 如果文件较大，请考虑将其转换为矢量磁贴源。
* 如果功能与 `Shape` 类包装在一起，则该形状的 `addProperty`、`setCoordinates` 和 `setProperties` 函数都将触发数据源中的更新和地图画布刷新。 数据源 `getShapes` 和 `getShapeById` 函数返回的所有功能都自动与 `Shape` 类包装在一起。 如果要更新多个形状，使用数据源 `toJson` 函数将它们转换为 JSON 更快，编辑 GeoJSON，然后将此数据传递到数据源 `setShapes` 函数中。

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>避免不必要地调用数据源清除函数

调用 `DataSource` 类的清除函数会导致地图画布刷新。 如果连续多次调用 `clear` 函数，则在地图等待每次刷新发生时会出现延迟。

通常在应用程序中出现此情况的常见情形是，应用清除数据源，下载新数据，再次清除数据源，然后将新数据添加到数据源。 根据所需的用户体验，以下替代方法更好。

* 先清除数据，再下载新数据，然后将新数据传递到数据源 `add` 或 `setShapes` 函数中。 如果这是地图上唯一的数据集，则在下载新数据时，地图将为空。
* 下载新数据，然后将其传递到数据源 `setShapes` 函数中。 这将替换地图上的所有数据。

### <a name="remove-unused-features-and-properties"></a>删除未使用的功能和属性

如果数据集包含应用中不会使用的功能，请将其删除。 同样，删除不需要的功能上的任何属性。 这样做有以下几个好处：

* 减少必须下载的数据量。
* 在呈现数据时减少需要循环遍历的功能的数量。
* 有时可以帮助简化或删除数据驱动的表达式和筛选器，这意味着在呈现时所需的处理更少。

当功能有很多属性或内容时，性能更高的方法是，将添加到数据源的内容限制为只用于呈现的内容，并在需要时使用单独的方法或服务来检索附加属性或内容。 例如，如果在地图上有一个简单地图显示位置，单击后，可显示一组详细内容。 如果要使用数据驱动样式来自定义地图上位置的呈现方式，请只将所需的属性加载到数据源中。 要显示详细内容时，请使用该功能的 ID 来单独检索附加内容。 如果内容存储在服务器端，则可以使用以异步方式检索的服务，这会大大减少初始加载地图时需要下载的数据量。

此外，减少功能坐标中的有效数字的数量还可以显著减少数据大小。 坐标包含 12 个或更多小数位数并不罕见，不过，六个小数位数的准确度约为 0.1 米，这通常比该坐标表示的位置更精确（在使用诸如室内建筑布局之类的小型位置数据时，建议使用六个小数位数）。 有六个以上的小数位数可能不会使数据的呈现方式产生任何差别，只会要求用户下载更多数据，而不会带来额外的好处。

下面是[用于处理 GeoJSON 数据的有用工具](https://github.com/tmcw/awesome-geojson)列表。

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>针对快速变化的数据使用单独的数据源

有时需要在地图上快速更新数据，如显示流式处理数据的实时更新或对功能进行动画处理。 更新数据源时，呈现引擎将循环遍历并呈现数据源中的所有功能。 将静态数据从快速变化的数据分离到不同的数据源中，可以显著减少每次对数据源更新所重新呈现的功能的数量，并提高总体性能。

如果结合实时数据使用矢量磁贴，则支持更新的一种简单方法是使用 `expires` 响应头。 默认情况下，任何矢量磁贴源或光栅磁贴层都将在 `expires` 的日期自动重新加载磁贴。 地图中的流量和事件磁贴使用此功能来确保新的实时流量数据显示在地图上。 可以通过将地图 `refreshExpiredTiles` 服务选项设置为 `false` 来禁用此功能。

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>在 GeoJSON 数据源中调整缓冲区和容差选项

`DataSource` 类将原始位置数据转换为用于动态呈现的本地矢量磁贴。 这些本地矢量磁贴将原始数据剪裁到磁贴区域的边界，带有一小部分缓冲区，以确保磁贴之间的平滑呈现。 `buffer` 选项越小，在本地矢量磁贴中存储的重叠数据越少，并且性能越好，但呈现项目发生的变化就越大。 请尝试调整此选项，以获得性能与最小呈现项目的适当组合。

`DataSource` 类还具有一个 `tolerance` 选项，该选项与 Douglas-Peucker 简化算法一起使用，以出于呈现目的减少几何图形的分辨率。 增加此容差值将降低几何图形的分辨率，进而提高性能。 调整此选项以针对数据集获取几何图形分辨率与性能的适当组合。

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>设置 GeoJSON 数据源的最大缩放选项

`DataSource` 类将原始位置数据转换为用于动态呈现的本地矢量磁贴。 默认情况下，它将执行此操作直到缩放级别 18，此时，在放大时，它将从为缩放级别 18 生成的磁贴中采样数据。 这适用于在这些级别放大时需要高分辨率的大多数数据集。 但是，当使用更有可能在缩小时查看的数据集时，例如查看州或省份多边形时，将数据源的 `minZoom` 选项设置为较小的值（如 `12`）将减少数量计算、发生的本地磁贴生成以及数据源使用的内存，并提高性能。

### <a name="minimize-geojson-response"></a>最小化 GeoJSON 响应

通过服务或通过加载平面文件来从服务器加载 GeoJSON 数据是，请确保将数据最小化，以删除使下载大小超出所需大小的不需要的空格字符。

### <a name="access-raw-geojson-using-a-url"></a>使用 URL 访问原始 GeoJSON

可以在 JavaScript 内部以内联方式存储 GeoJSON 对象，但这将使用大量内存，因为它的副本将存储在为此对象创建的变量中，而数据源实例会在单独的 web 辅助角色中管理对象。 改为使用 URL 向应用公开 GeoJSON，数据源会直接将数据的单个副本加载到数据源 web 辅助角色中。  

## <a name="optimize-rendering-layers"></a>优化呈现层

Azure 地图提供了几个不同的层来在地图上呈现数据。 可以利用很多优化功能来定制这些层，为你的方案提高性能和总体用户体验。

### <a name="create-layers-once-and-reuse-them"></a>一次性创建层并重复使用

Azure Maps Web SDK 确定是数据驱动的。 数据进入数据源，随后连接到呈现层。 如果要更改地图上的数据，请更新数据源中的数据或更改层上的样式选项。 这通常比删除并接着在每次更改时重新创建层要快得多。

### <a name="consider-bubble-layer-over-symbol-layer"></a>优先于符号层考虑气泡层

气泡层在地图上以圆的形式呈现点，并且可以使用数据驱动的表达式来轻松地设置其半径和颜色样式。 由于圆圈对于 WebGL 而言是绘制简单的形状，因此呈现引擎能够更快地呈现这些内容，而符号层则必须加载和呈现图像。 在呈现数以万计个点时，这两个呈现层的性能差异非常明显。

### <a name="use-html-markers-and-popups-sparingly"></a>尽量少用 HTML 标记和弹出窗口

与 Azure Maps Web 控件中使用 WebGL 进行呈现的大多数层不同，HTML 标记和弹出窗口使用传统的 DOM 元素进行呈现。 在这种情况下，添加页面的 HTML 标记和弹出窗口越多，DOM 元素就越多。 添加几百个 HTML 标记或弹出窗口后，可能会降低性能。 对于较大的数据集，可以考虑对数据进行聚类分析，或者使用符号或气泡层。 对于弹出窗口，常见的策略是创建单个弹出窗口，并通过更新其内容和位置来重复使用，如以下示例中所示：

<br/>

<iframe height='500' scrolling='no' title='重复使用具有多个固定项的弹出窗口' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的<a href='https://codepen.io/azuremaps/pen/rQbjvK/'>重复使用具有多个固定项的弹出窗口</a> Pen。
</iframe>

也就是说，如果地图上只有几个点要呈现，则 HTML 标记的简单性可能是首选的。 此外，如果需要，可以很容易将 HTML 标记变为可拖动。

### <a name="combine-layers"></a>合并层

地图能够呈现数百个层，然而，层越多，呈现场景所用的时间就越长。 减少层数的一种策略是合并具有相似样式的层或可以使用[数据驱动样式](data-driven-style-expressions-web-sdk.md)来设置样式的层。

例如，考虑一个数据集，其中所有功能都有 `isHealthy` 属性，该属性可以为 `true` 或 `false` 值。 如果创建基于此属性呈现不同彩色气泡的气泡层，可通过多种方式执行此操作，如下面从性能最低到最高所列。

* 基于 `isHealthy` 值将数据拆分为两个数据源，并将具有硬编码颜色选项的气泡层附加到每个数据源。
* 将所有数据放入单个数据源，并基于 `isHealthy` 属性创建具有硬编码颜色选项和筛选器的两个气泡层。 
* 将所有数据放入单个数据源，基于 `isHealthy` 属性创建具有 `case` 颜色选项样式表达式的单个气泡层。 下面的代码示例演示了这一点。

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>创建平滑符号层动画

默认情况下，符号层启用冲突检测。 此冲突检测旨在确保没有两个符号重叠。 符号层的图标和文本选项有两个选项，

* `allowOverlap` - 如果该符号与其他符号冲突，指定该符号是否可见。
* `ignorePlacement` - 指定是否允许其他符号与该符号发生冲突。

默认情况下，这两个选项都设置为 `false`。 对符号进行动画处理时，会在动画的每一帧上运行冲突检测计算，这会使动画变慢并使其看上去不够流畅。 若要使动画平滑，请将这些选项设置为 `true`。

下面的代码示例是对符号层进行动画处理的一种简单方式。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="符号层动画" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的<a href='https://codepen.io/azuremaps/pen/oNgGzRd'>符号层动画</a> Pen。
</iframe>

### <a name="specify-zoom-level-range"></a>指定缩放级别范围

如果数据满足以下条件之一，请确保指定层的最小和最大缩放级别，以便呈现引擎在缩放级别范围之外时可以跳过它。

* 如果数据来自矢量磁贴源，通常只能通过一系列缩放级别使用不同数据类型的源层。
* 如果使用的磁贴层没有用于从 0 到 24 的所有缩放级别的磁贴，并且想要它仅在有磁贴的级别上呈现，则请勿尝试使用其他缩放级别中的磁贴来填充缺少的磁贴。
* 如果只希望在特定缩放级别呈现层。
所有层都有 `minZoom` and `maxZoom` 选项，在这些缩放级别之间，将基于此逻辑 ` maxZoom > zoom >= minZoom` 呈现层。

**示例**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>指定磁贴层边界和源缩放范围

默认情况下，磁贴层将在整个地球上加载磁贴。 但是，如果磁贴服务只有特定区域的磁贴，地图会在此区域外尝试加载磁贴。 发生这种情况时，针对每个磁贴的请求会发出并等待响应，该响应会阻止地图发出的其他请求，从而使其他层的呈现速度变慢。 指定磁贴层的边界将导致地图只请求位于该边界框内的磁贴。 此外，如果磁贴层仅在特定缩放级别之间可用，请为同一原因指定最小和最大源缩放。

**示例**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>当基本地图不可见时使用空白地图样式

如果层在将完全覆盖基本地图的地图上被覆盖，请考虑将地图样式设置为 `blank` 或 `blank_accessible` 以便不呈现基本地图。 执行此操作的常见情形是：覆盖整个地球的磁贴在基本地图之上没有不透明或透明区域。

### <a name="smoothly-animate-image-or-tile-layers"></a>对图像或磁贴层进行平滑的动画处理

如果希望通过地图上的一系列图像或磁贴层进行动画处理。 通常，为每个图像或磁贴层创建一个层并更改不透明度比更新每个动画帧上单个层的源更快。 通过将不透明度设置为零来隐藏层并通过将其不透明度设置为大于零的值来显示新层，要比更新层中的源快得多。 或者，可以切换层的可见性，但一定要将层的淡化持续时间设置为零，否则，它会在显示时对层进行动画处理，这将导致闪烁效果，因为在新层可见之前，前一层可能已经隐藏。

### <a name="tweak-symbol-layer-collision-detection-logic"></a>调整符号层冲突检测逻辑

符号层具有两个选项，针对图标和文本存在，称为 `allowOverlap` 和 `ignorePlacement`。 这两个选项指定符号的图标或文本是否可以重叠或被重叠。 当这些设置为时 `false` ，符号层将在呈现每个点时进行计算，以查看其是否与层中任何其他已呈现的符号冲突，如果有，则不会呈现冲突符号。 这非常适合减少地图上的混乱并减少所呈现对象的数量。 通过将这些选项设置为 `false`，将跳过此冲突检测逻辑，并且地图上会呈现所有符号。 调整此选项以获得性能和用户体验的最佳组合。

### <a name="cluster-large-point-data-sets"></a>聚类大型点数据集

当使用大型数据点集时，可能会发现，当在特定缩放级别呈现时，许多点会重叠并且仅部分可见（如果有）。 聚类分析是将邻近的点组合在一起，并将其作为单个聚类点表示的过程。 当用户放大地图时，聚类的各个点将会分开。 这可以显著减少需要呈现的数据量，使地图感觉更简洁，并提高性能。 `DataSource` 类具有用于本地聚类分析数据的选项。 此外，许多生成矢量磁贴的工具也具有聚类分析选项。

另外，可以增加聚类半径的大小以提高性能。 聚类半径越大，保持跟踪和呈现的聚类点就越少。
有关详细信息，请参阅[聚类分析点数据文档](clustering-point-data-web-sdk.md)

### <a name="use-weighted-clustered-heat-maps"></a>使用加权聚类热度地图

热度地图层可以轻松呈现数万个数据点。 对于较大的数据集，请考虑对数据源启用聚类分析和使用小聚类半径，并使用聚类 `point_count` 属性作为高度地图的权重。 当聚类半径只有几个像素大时，呈现的热度地图几乎没有视觉差异。 使用较大的聚类半径会提高更多性能，但可能会降低所呈现热度地图的分辨率。

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

有关详细信息，请参阅 [本文档中的聚类分析和热度地图](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer)

### <a name="keep-image-resources-small"></a>减小图像资源大小

可以将图像添加到地图图像子画面，以便在符号层或多边形层的模式中呈现图标。 请减小这些图像的大小，以最大程度地减少必须下载的数据量，以及其在地图图像子画面中占用的空间量。 当使用通过 `size` 选项缩放图标的符号层时，使用的图像是计划在地图上显示的最大大小，不能更大。 这将确保图标以高分辨率呈现，同时最大限度地减少它使用的资源。 此外，SVG 还可以作为更小的文件格式用于简单的图标图像。

## <a name="optimize-expressions"></a>优化表达式

[数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)提供了很大的灵活性和强大功能，用于对地图上的数据进行筛选和样式设置。 可以通过多种方式优化表达式。 以下是一些提示。

### <a name="reduce-the-complexity-of-filters"></a>降低筛选器的复杂性

筛选器循环访问数据源中的所有数据，并检查每个筛选器是否与筛选器中的逻辑匹配。 如果筛选器变得很复杂，这可能会导致性能问题。 解决此情况的一些可能策略包含以下内容。

* 如果使用矢量磁贴，请将数据拆分成不同的源层。
* 如果使用 `DataSource` 类，请将数据拆分成单独的数据源。 请尝试平衡数据源的数量与筛选器的复杂性。 数据源太多也可能会导致性能问题，因此可能需要进行一些测试，以找出最适合你的方案。
* 在层上使用复杂筛选器时，请考虑将多个层与样式表达式结合使用来降低筛选器的复杂性。 当样式表达式可用作大量层时，避免使用硬编码样式创建一组层也会导致性能问题。

### <a name="make-sure-expressions-dont-produce-errors"></a>请确保表达式不产生错误

表达式通常用于生成代码，以用于在呈现时执行计算或逻辑操作。 与应用程序其余部分的代码一样，请确保计算和逻辑合理，并且不容易出错。 表达式中的错误会导致计算表达式时出现问题，这可能会导致性能下降和呈现问题。

需要注意的一个常见错误是，表达式依赖可能不存在于所有功能上的功能属性。 例如，下面的代码使用一个表达式将气泡层的颜色属性设置为一个功能的 `myColor` 属性。

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

如果数据源中的所有功能都有 `myColor` 属性，并且该属性的值是一种颜色，则上述代码将运行正常。 如果对数据源中的数据具有完全控制，并且确定知道所有功能会在 `myColor` 属性中具有一个有效颜色，则这可能不是问题。 也就是说，若要使此代码不发生错误，可将 `case` 表达式与 `has` 表达式结合使用来检查该功能是否具有 `myColor` 属性。 如果是，则可适用 `to-color` 类型表达式来尝试将该属性的值转换为一个颜色。 如果颜色无效，则可以使用回退颜色。 下面的代码演示了如何执行此操作，并将回退颜色设置为绿色。

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>从最特定到最不特定的顺序对布尔表达式排序

使用包含多个条件测试的布尔表达式时，将条件测试从最特定到最不特定进行排序。 通过执行此操作，第一个条件应可减少必须针对其测试第二个条件的数据量，从而减少需要执行的条件测试的总数。

### <a name="simplify-expressions"></a>简化表达式

表达式可能功能强大，但有时较复杂。 表达式越简单，计算的速度就越快。 例如，如果需要简单的比较，类似于 `['==', ['get', 'category'], 'restaurant']` 的表达式会优于使用类似于 `['match', ['get', 'category'], 'restaurant', true, false]` 的匹配表达式。 在这种情况下，如果所检查的属性为布尔值，则 `get` 表达式可以是甚至更简单的 `['get','isRestaurant']`。

## <a name="web-sdk-troubleshooting"></a>Web SDK 疑难解答

下面是对使用 Azure Maps Web SDK 开发时遇到的部分常见问题进行调试的一些提示。

**为什么加载 web 控件时地图不显示？**

请执行以下操作：

* 确保已将所添加的身份验证选项添加到地图。 如果未添加，则将以空白画布加载地图，因为不进行身份验证就不能访问基本地图数据，并且401 错误将出现在浏览器的开发人员工具的网络选项卡中。
* 确保有 Internet 连接。
* 检查浏览器的开发人员工具的控制台，寻找错误。 某些错误可能导致地图不呈现。 调试应用程序。
* 确保使用的是[支持的浏览器](supported-browsers.md)。

**我的所有数据都显示在世界的另一侧，怎么回事？**
Azure Maps SDK 中的坐标也称为位置，与地理空间行业标准格式 `[longitude, latitude]` 对齐。 此格式也是在 GeoJSON 架构中定义坐标的方式；Azure Maps SDK 中使用的格式的核心数据。 如果数据出现在世界的另一侧，则很可能是因为坐标/位置信息中的经度和纬度值反了。

**为什么 HTML 标记出现在 web 控件中错误的位置？**

要检查的事项：

* 如果对标记使用自定义内容，请确保 `anchor` 和 `pixelOffset` 选项正确。 默认情况下，内容的底部中心与地图上的位置对齐。
* 请确保已加载 Azure Maps 的 CSS 文件。
* 检查 HTML 标记 DOM 元素，查看应用程序中是否有任何 CSS 追加到标记并影响其位置。

**为什么符号层中的图标或文本出现在错误的位置？**
检查 `anchor` 和 `offset` 选项是否已正确配置，以使其与你希望与地图上的坐标对齐的图像或文本的部分对齐。
如果符号仅在地图旋转时离开位置，请检查 `rotationAlignment` 选项。 默认情况下，我们将以地图视区旋转符号，使其对用户竖直显示。 但是，根据具体情况，可能需要将符号与地图的方向锁定。 将 `rotationAlignment` 选项设置为 `’map’` 可执行此操作。
如果符号仅在地图俯仰/倾斜时离开位置，请检查 `pitchAlignment` 选项。 默认情况下，当地图俯仰或倾斜时，符号将与地图视区保持竖直。 但是，根据具体情况，可能需要将符号与地图的俯仰方向锁定。 将 `pitchAlignment` 选项设置为 `’map’` 可执行此操作。

**为什么地图上没有显示任何我的数据？**

要检查的事项：

* 检查浏览器的开发人员工具中的控制台，寻找错误。
* 确保数据源已创建并添加到地图，并确保数据源已连接到也添加到地图的呈现层。
* 在代码中添加断点并逐步执行，以确保数据添加到数据源，数据源和层添加到地图，且不发生任何错误。
* 尝试从呈现层中删除数据驱动的表达式。 可能其中一项存在导致了此问题的错误。

**是否可以在沙盒 iframe 中使用 Azure Maps Web SDK？**

是的。 请注意，[Safari 有一个 bug](https://bugs.webkit.org/show_bug.cgi?id=170075)，该 bug 阻止沙盒 iframe 运行 Azure Maps Web SDK 要求的 web 辅助角色。 解决方案是将 `"allow-same-origin"` 标签添加到 iframe 的沙盒属性。

## <a name="get-support"></a>获取支持

下面是根据你的问题获取对 Azure Maps 的支持的不同方法。

**如何报告数据问题或有关地址的问题？**

Azure Maps 提供了一个数据反馈工具，可以报告和跟踪数据问题。 [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) 提交的每个问题都会生成唯一的 URL，可以使用它来跟踪数据问题的进度。 解决数据问题所需的时间因问题类型以及验证更改是否正确的简易程度而变化。 修复后，呈现服务将按周更新，而其他服务（如地理编码和路由）将按月更新。 本[文档](how-to-use-feedback-tool.md)提供了有关如何报告数据问题的详细说明。

**如何报告服务或 API 中的 bug？**

https://azure.com/support

**从哪里获取 Azure Maps 的技术帮助？**

如果与 Power BI 中的 Azure Maps 视觉对象相关： https://powerbi.microsoft.com/support/ 对于所有其他 Azure Maps 服务： https://azure.com/support 或开发人员论坛：[https://docs.microsoft.com/answers/topics/azure-maps.html](/answers/topics/azure-maps.html)。

**如何发出功能请求？**

在我们的用户之声网站上发出功能请求： https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关在应用程序中改进用户体验的更多提示。

> [!div class="nextstepaction"]
> [使应用程序可供访问](map-accessibility.md)

详细了解 Azure Maps 和地理空间行业使用的术语。

> [!div class="nextstepaction"]
> [Azure Maps 术语表](glossary.md)