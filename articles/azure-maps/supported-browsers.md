---
title: Web SDK 支持的浏览器 |Microsoft Azure Maps
description: 了解如何检查 Azure Maps Web SDK 是否支持浏览器。 查看支持的浏览器列表。 了解如何将映射服务与旧浏览器结合使用。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: a393d6c54f5d9918b3746bfc6c1de1c93cd9319c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435017"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK 支持的浏览器

Azure Maps Web SDK 提供了名为 [atlas.isSupported](/javascript/api/azure-maps-control/atlas#issupported-boolean-) 的帮助程序函数。 该函数将检测 Web 浏览器是否具有支持加载和提供地图控件所需的最小 WebGL 功能集。 以下示例呈现了如何使用该函数：

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>桌面

Azure Maps Web SDK 支持以下桌面浏览器：

- Microsoft Edge（当前版本和以前版本）
- Google Chrome（当前版本和以前版本）
- Mozilla Firefox（当前版本和以前版本）
- Apple Safari (macOS X)（当前版本和以前版本）

另请参阅本文后面的[使用旧版浏览器](#Target-Legacy-Browsers)。

## <a name="mobile"></a>移动

Azure Maps Web SDK 支持以下手机浏览器：

- Android
  - Android 6.0 及更高版本上的当前 Chrome 版本
  - Android 6.0 及更高版本上的 Chrome WebView
- iOS
  - 当前和以前的 iOS 主版本上的移动 Safari
  - 当前和以前的 iOS 主版本上的 UIWebView 和 WKWebView
  - iOS 的当前 Chrome 版本

> [!TIP]
> 如果要使用 WebView 控件在移动应用程序中嵌入地图，建议你使用 [Azure Maps WEB SDK 的 npm 包](https://www.npmjs.com/package/azure-maps-control)，而不是引用在 Azure 内容分发网络上托管的 SDK 版本。 这种方法可减少加载时间，因为 SDK 已在用户的设备上，并且无需在运行时下载。

## <a name="nodejs"></a>Node.js

Node.js 还支持以下 Web SDK 模块：

- 服务模块（[文档](how-to-use-services-module.md) |  [npm 模块](https://www.npmjs.com/package/azure-maps-rest)）

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>使用旧版浏览器

你可能想要使用不支持 WebGL 的旧版浏览器，或使用只对其提供有限支持的旧版浏览器。 在这种情况下，我们建议你结合使用 Azure Maps 服务和开源地图控件，如 [Leaflet](https://leafletjs.com/)。 下面是使用开源 [Azure Maps Leaflet 插件](https://github.com/azure-samples/azure-maps-leaflet)的示例。

<br/>

<iframe height="500" scrolling="no" title="Azure Maps + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Pen <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Leaflet</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

[此处](https://azuremapscodesamples.azurewebsites.net/?search=leaflet)提供了使用 Leaflet 中 Azure Maps 的其他代码示例。

[此处](open-source-projects.md#third-part-map-control-plugins)是 Azure Maps 团队为其创建了插件的一些热门开源地图控件。

## <a name="next-steps"></a>后续步骤

详细了解 Azure Maps Web SDK：

[地图控件](how-to-use-map-control.md)

[服务模块](how-to-use-services-module.md)
