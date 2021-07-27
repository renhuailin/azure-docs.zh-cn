---
title: Azure Media Player 完全设置
description: 了解如何设置 Azure Media Player。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: d7c103a7eba2e5da7ac040b50fcc0009a0b7237b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449862"
---
# <a name="azure-media-player-full-setup"></a>Azure Media Player 完全设置 #

Azure Media Player 易于设置。 只需要片刻时间便可从你的 Azure 媒体服务帐中对媒体内容进行基本播放。 版本的示例目录中也提供了[示例](https://github.com/Azure-Samples/azure-media-player-samples)。

## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>步骤 1：在页面的头中包含 JavaScript 和 CSS 文件 ##

使用 Azure Media Player，你可以从 CDN 托管版本中访问脚本。 现在通常建议将 JavaScript 放在结束正文标记 `<body>` 之前，而不是放在 `<head>` 之前，但是 Azure Media Player 包含一个“HTML5 Shiv”，对于旧的 IE 版本来说，需要将它放在头部才能将视频标记视为有效元素。

> [!NOTE]
> 如果你已在使用 HTML5 shiv（例如 [Modernizr](https://modernizr.com/)），则可以在任意位置包含 Azure Media Player JavaScript。 但是，请确保你的 Modernizr 版本包含适用于视频的 shiv。

### <a name="cdn-version"></a>CDN 版本 ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
>  不应当在生产中使用`latest`版本，因为此版本可能会根据需要进行更改。 将 `latest` 替换为 Azure Media Player 的某个版本。 例如，将 `latest` 替换为 `2.1.1`。 可以从[此处](https://amp.azure.net/libs/amp/latest/docs/changelog.html)查询 Azure Media Player 版本。

> [!NOTE]
> 自 `1.2.0` 版本后，不再需要包含回退技术的位置（它会自动从 azuremediaplayer.min.js 文件的相对路径中获取该位置）。 可以通过在 `<head>` 中的上述脚本之后添加以下脚本来修改回退技术的位置。

> [!NOTE]
> 由于 Flash 和 Silverlight 插件的性质，swf 和 xap 文件应承载在域中，且不包含任何敏感信息或数据 - 使用 Azure CDN 托管版本时，系统会自动为你处理此事项。

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>步骤 2：将 HTML5 视频标记添加到页面 ##

有了 Azure Media Player，你就可以使用 HTML5 视频标记来嵌入视频。 然后，Azure Media Player 会读取标记并使其在所有浏览器中生效，而不是仅仅在支持 HTML5 视频的浏览器中生效。 除了基本标记外，Azure Media Player 还需要一些额外的片段。

1. `<video>` 上的 `<data-setup>` 属性指示 Azure Media Player 在页面就绪时自动设置视频，并从属性中读取任何内容（采用 JSON 格式）。
1. `id` 属性：应当用于同一页面上的每个视频，并且应独一无二。
1. `class` 属性包含两个类：
    - `azuremediaplayer` 应用 Azure Media Player UI 功能所需的样式
    - `amp-default-skin` 将默认外观应用于 HTML5 控件
1. `<source>` 包含两个必需的属性
    - `src` 属性可以包括一个 *.ism/manifest 文件，如果添加了 Azure 媒体服务，则 Azure Media Player 会自动将 DASH、SMOOTH 和 HLS 的 URL 添加到播放机中
    - `type` 属性是流的必需 MIME 类型。 与“.ism/manifest”关联的 MIME 类型是“application/vnd.ms-sstr+xml”。 
1. `<source>` 上的可选 `<data-setup>` 属性告诉 Azure Media Player，Azure 媒体服务的流是否有任何独特的传递策略，包括但不限于加密类型（AES 或 PlayReady、Widevine 或 FairPlay）和令牌。

包括/排除属性、设置、源和跟踪，完全就像对 HTML5 视频所做的那样。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

默认情况下，大型播放按钮位于左上角，因此它不会盖住海报中需关注的部分。 如果你希望将大型播放按钮居中放置，则可以向 `<video>` 元素添加一个附加的 `amp-big-play-centered` `class`。

### <a name="alternative-setup-for-dynamically-loaded-html"></a>动态加载的 HTML 的替代设置 ###

如果网页或应用程序以动态方式加载视频标记（ajax、appendChild，等等），因此该标记在页面加载时可能不存在，则你需要手动设置播放机，而不是依赖于 data-setup 属性。 为此，请首先从标记中删除 data-setup 属性，这样就不会在播放机初始化时产生混淆。 接下来，在加载 Azure Media Player JavaScript 并将视频标记加载到 DOM 之后，运行以下 JavaScript。

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            });
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

`amp` 函数中的第一个参数是视频标记的 ID。 请将其替换为你自己的值。

第二个参数是一个 options 对象。 它允许你设置其他选项，就像你使用 data-setup 属性时能够做的那样。

第三个参数是一个 'ready' 回调。 在 Azure Media Player 初始化后，它会调用此函数。 在 ready 回调中，“this”对象指的是播放机实例。

你还可以传递对元素本身的引用，而不使用元素 ID。

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
