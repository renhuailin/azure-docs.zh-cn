---
title: 为 Azure Media Player 编写插件
description: 了解如何使用 JavaScript 编写 Azure Media Player 插件
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7f14be29a33a885bfcef27fa9ef2775e72df16d9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449794"
---
# <a name="writing-plugins-for-azure-media-player"></a>为 Azure Media Player 编写插件 #

插件是为扩展或增强播放器而编写的 JavaScript。 你可以编写插件来更改 Azure Media Player 的外观、功能，甚至使其与其他服务交互。 可以通过两个简单的步骤完成此操作：

## <a name="step-1"></a>步骤 1 ##

在函数中编写 JavaScript，如下所示：

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

可以直接在 HTML 页的 `<script>` 标记内或某个外部 JavaScript 文件中编写代码。 如果是后者，请确保在 AMP 脚本之后的 HTML 页的 `<head>` 中包括 JavaScript 文件。

示例：

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>步骤 2 ##
通过以下两种方式之一，使用 JavaScript 初始化插件：

方法 1：

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

方法 2：

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

插件选项不是必需的，包括这些选项只是允许使用插件的开发人员配置其行为而不必更改源代码。

有关创建插件的灵感和更多示例，请查看我们的[库](azure-media-player-plugin-gallery.md)

>[!NOTE]
> 插件代码会在观看者的播放器体验期间动态更改 DOM 中的项目，而永远不会对播放器的源代码进行永久更改。 这就是了解浏览器的开发人员工具的方便之处。 例如，如果想要更改播放器中某个元素的外观，则可以通过其类名找到其 HTML 元素，然后在那里添加或更改属性。 下面是有关[更改 HTML 属性](http://www.w3schools.com/js/js_htmldom_html.asp)的有用资源。

### <a name="integrated-plugins"></a>集成插件 ###

 目前有两个插件烘焙到 AMP 中：[时间提示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)和[热键](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)。 这些插件最初是为播放器开发的模块化插件，但现在已包含在播放器源代码中。

### <a name="plugin-gallery"></a>插件库 ###

[插件库](https://aka.ms/ampplugins)有几个由社区贡献的插件，适用于时间线标记、缩放、分析等功能。 该页提供对插件的访问、有关如何设置插件的说明，以及介绍如何使用插件的演示。 如果你创建了一个很酷的插件，认为应该将其包括在我们的库中，请随时提交，以便我们对其进行检查。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
