---
title: 在应用中嵌入 Azure 视频分析器媒体版（以前称为视频索引器）小组件
description: 了解如何在应用中嵌入 Azure 视频分析器媒体版（以前称为视频索引器）小组件。
ms.topic: how-to
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 7ab396292a1fe7d44391f9f6ba75d69a57e57dd3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615940"
---
# <a name="embed-video-analyzer-for-media-widgets-in-your-apps"></a>在应用中嵌入视频分析器媒体版小组件

本文介绍如何在应用中嵌入 Azure 视频分析器媒体版（以前称为视频索引器）小组件。 视频分析器媒体版支持将三类小组件嵌入应用：认知见解、播放器和编辑器  。

从版本 2 开始，小组件基 URL 将包含指定帐户所属的区域。 例如，美国西部区域中的帐户将生成：`https://www.videoindexer.ai/embed/insights/.../?location=westus2`。

## <a name="widget-types"></a>小组件类型

### <a name="cognitive-insights-widget"></a>认知见解小组件

认知见解小组件包括从视频索引过程中提取的所有视觉见解。 认知见解小组件支持以下可选的 URL 参数：

|名称|定义|说明|
|---|---|---|
|`widgets` | 用逗号分隔的字符串 | 用于控制要呈现的见解。<br/>示例：`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` 仅呈现人员和关键字 UI 见解。<br/>可用选项：人员、animatedCharacters、关键字、标签、情绪、情感、主题、关键帧、脚本、OCR、扬声器、场景和 namedEntities。|
|`controls`|用逗号分隔的字符串|用于控制要呈现的控件。<br/>示例：`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` 仅呈现搜索选项和“下载”按钮。<br/>可用选项：搜索、下载、预设和语言。|
|`language`|短语言代码（语言名称）|控制见解语言。<br/>示例：`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>或 `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | 短语言代码 | 控制 UI 的语言。 默认值为 `en`。 <br/>示例：`locale=de`。|
|`tab` | 默认选定的选项卡 | 控制默认呈现的“见解”选项卡。 <br/>示例：`tab=timeline` 在“时间线”选项卡处于选中状态时呈现见解。|
|`location` ||`location` 参数必须包含在嵌入的链接中，请参阅[如何获取区域名称](regions.md)。 如果你的帐户为预览版，则位置值必须使用 `trial`。 `trial` 为参数 `location` 的默认值。| 

### <a name="player-widget"></a>播放器小组件

可使用播放器小组件通过自适应比特率流式传输视频。 播放器小组件支持以下可选的 URL 参数。

|名称|定义|说明|
|---|---|---|
|`t` | 开始后的秒数 | 让播放器从给定时间点开始播放。<br/> 示例：`t=60`。 |
|`captions` | 语言代码 | 在小组件加载过程中提取指定语言的字幕，使之在“字幕”菜单中可用。<br/> 示例：`captions=en-US`。 |
|`showCaptions` | 布尔值 | 使播放器与已启用的字幕一起加载。<br/> 示例：`showCaptions=true`。 |
|`type`| | 激活音频播放器外观（视频部件已删除）。<br/> 示例：`type=audio`。 |
|`autoplay` | 布尔值 | 确定播放器是否会在加载后开始播放视频。 默认值为 `true`。<br/> 示例：`autoplay=false`。 |
|`language`/`locale` | 语言代码 | 控制播放器语言。 默认值为 `en-US`。<br/>示例：`language=de-DE`。|
|`location` ||`location` 参数必须包含在嵌入的链接中，请参阅[如何获取区域名称](regions.md)。 如果你的帐户为预览版，则位置值必须使用 `trial`。 `trial` 为参数 `location` 的默认值。| 

### <a name="editor-widget"></a>编辑器小组件

可以使用编辑器小组件来创建新项目并管理视频见解。 编辑器小组件支持以下可选的 URL 参数。

|名称|定义|说明|
|---|---|---|
|`accessToken`<sup>*</sup> | 字符串 | 提供对仅在用于嵌入小组件的帐户中的视频的访问。<br> 编辑器小组件需要 `accessToken` 参数。 |
|`language` | 语言代码 | 控制播放器语言。 默认值为 `en-US`。<br/>示例：`language=de-DE`。 |
|`locale` | 短语言代码 | 控制见解语言。 默认值为 `en`。<br/>示例：`language=de`。 |
|`location` ||`location` 参数必须包含在嵌入的链接中，请参阅[如何获取区域名称](regions.md)。 如果你的帐户为预览版，则位置值必须使用 `trial`。 `trial` 为参数 `location` 的默认值。| 

<sup>*</sup>所有者应小心提供 `accessToken`。

## <a name="embed-videos"></a>嵌入视频

本部分介绍如何[使用门户](#the-portal-experience)或者通过[手动汇编 URL](#assemble-the-url-manually) 将视频嵌入应用中。 

`location` 参数必须包含在嵌入的链接中，请参阅[如何获取区域名称](regions.md)。 如果你的帐户为预览版，则位置值必须使用 `trial`。 `trial` 为参数 `location` 的默认值。 例如：`https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`。

### <a name="the-portal-experience"></a>门户体验

若要嵌入视频，请按如下所述使用门户：

1. 登录到[视频分析器媒体版](https://www.videoindexer.ai/)网站。
1. 选择要使用的视频，并按“播放”。
1. 选择所需的小组件类型（“认知见解”、“播放器”或“编辑器”）  。
1. 单击 &lt;/&gt;“嵌入”。
5. 复制嵌入代码（出现在“共享和嵌入”对话框的“复制嵌入的代码”中） 。
6. 将代码添加到应用。

> [!NOTE]
> 共享“播放器”或“见解”小组件的链接将包含访问令牌并向帐户授予只读权限。

### <a name="assemble-the-url-manually"></a>手动汇编 URL

#### <a name="public-videos"></a>公用视频

可按如下所示通过汇编 URL 来嵌入公用视频：

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>`
  
  
#### <a name="private-videos"></a>私用视频

若要嵌入私有视频，必须在 iframe 的 `src` 特性中传递访问令牌（使用[获取视频访问令牌](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token)）：

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
  
### <a name="provide-editing-insights-capabilities"></a>提供编辑见解功能

若要在嵌入的小组件中提供编辑见解功能，必须传递包含编辑权限的访问令牌。 在指定 `&allowEdit=true` 的情况下使用[获取视频访问令牌](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token)。

## <a name="widgets-interaction"></a>小组件交互

认知见解小组件可以与应用的视频交互。 本部分说明如何实现此交互。

![视频分析器媒体版的认知见解小组件](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>流概述

编辑脚本时，将发生以下流：

1. 可以在时间线中编辑脚本。
1. 视频分析器媒体版获取这些更新，并将它们保存在语言模型中的[来自脚本编辑](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts)内。
1. 更新字幕：

    * 如果使用视频分析器媒体版的播放器小组件 - 它会自动更新。
    * 如果使用外部播放器 - 可使用“获取视频字幕”调用获得新字幕文件。

### <a name="cross-origin-communications"></a>跨域通信

为了让视频分析器媒体版小组件与其他组件通信，视频分析器媒体版服务将执行以下操作：

- 使用跨域通信 HTML5 方法 `postMessage`。
- 跨 VideoIndexer.ai 域验证消息。

如果实现你自己的播放器代码并与认知见解小组件集成，则你应负责验证来自 VideoIndexer.ai 的消息的来源。

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>在应用或博客中嵌入小组件（建议）

本部分介绍如何在两个视频分析器媒体版小组件之间实现交互，这样当某个用户选择应用中的见解控件时，播放器就会跳到相关的时刻。

1. 复制播放器小组件嵌入代码。
2. 复制认知见解嵌入代码。
3. 添加[转存进程文件](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)，以便处理两个小组件之间的通信：<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

现在，当某个用户选择应用中的见解控件时，播放器就会跳到相关的时刻。

有关详细信息，请参阅[视频分析器媒体版 -“嵌入两个小组件”演示](https://codepen.io/videoindexer/pen/NzJeOb)。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>嵌入认知见解小组件并使用 Azure Media Player 来播放内容

本部分介绍如何使用 [AMP 插件](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)来实现认知见解小组件与 Azure Media Player 实例之间的交互。

1. 添加 AMP 播放器的视频分析器媒体版插件：<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. 使用视频分析器媒体版插件实例化 Azure Media Player。

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. 复制认知见解嵌入代码。

现在，你可以与 Azure Media Player 进行通信。

有关详细信息，请参阅 [Azure Media Player + VI Insights 演示](https://codepen.io/videoindexer/pen/rYONrO)。

### <a name="embed-the-video-analyzer-for-media-cognitive-insights-widget-and-use-a-different-video-player"></a>嵌入视频分析器媒体版认知见解小组件并使用其他视频播放器

如果使用 Azure Media Player 以外的其他视频播放器，则必须手动操作视频播放器才能实现通信。

1. 插入视频播放器。

    例如，标准 HTML5 播放器：

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. 嵌入认知见解小组件。
3. 通过侦听“消息”事件实现播放器的通信。 例如：

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videoindexer domain.
          if ((origin === "https://www.videoindexer.ai") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

有关详细信息，请参阅 [Azure Media Player + VI Insights 演示](https://codepen.io/videoindexer/pen/YEyPLd)。

## <a name="adding-subtitles"></a>添加字幕

如果将视频分析器媒体版见解嵌入到你自己的 [Azure Media Player](https://aka.ms/azuremediaplayer) 中，则可使用 `GetVttUrl` 方法获取隐藏式字幕。 也可从视频分析器媒体版 AMP 插件 `getSubtitlesUrl`（如前所示）调用 JavaScript 方法。

## <a name="customizing-embeddable-widgets"></a>自定义可嵌入式小组件

### <a name="cognitive-insights-widget"></a>认知见解小组件

你可以选择所需的见解类型。 为此，请将它们指定为要添加到从 API 或 Web 应用获取的嵌入代码中的以下 URL 参数的值：`&widgets=<list of wanted widgets>`。

可能的值为 `people`、`animatedCharacters`、`keywords`、`labels`、`sentiments`、`emotions`、`topics`、`keyframes`、`transcript`、`ocr`、`speakers`、`scenes` 和 `namedEntities`。

例如，如果需要嵌入的小组件仅包含人员和关键字见解，则 iframe 嵌入 URL 将如下所示：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

iframe 窗口的标题也可自定义，只需为 iframe URL 提供 `&title=<YourTitle>` 即可。 （它将自定义 HTML `<title>` 值）。
   
例如，如果需要为 iframe 窗口提供标题“MyInsights”，则 URL 将如下所示：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

请注意，仅当你需要在新窗口中打开见解时，此选项才适用。

### <a name="player-widget"></a>播放器小组件

如果嵌入视频分析器媒体版播放器，则可通过指定 iframe 的大小来选择播放器的大小。

例如：

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

默认情况下，视频分析器媒体版播放器已自动生成基于视频脚本的隐藏式字幕。 此脚本是从视频中提取的，其中包含上传视频时选择的源语言。

如果要嵌入其他语言，可以在嵌入播放器 URL 中添加 `&captions=<Language Code>`。 如果需要默认显示字幕，则可以传递 &showCaptions=true。

然后，嵌入 URL 将如下所示：

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>自动播放

播放器会按照默认设置开始播放视频。 你可以通过将 `&autoplay=false` 传递到上述嵌入 URL，选择不按默认设置播放。

## <a name="code-samples"></a>代码示例

请参阅包含视频分析器媒体版 API 和小组件示例的[代码示例](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets)存储库：

| 文件/文件夹                       | 说明                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | 在自定义 Azure Media Player 中加载视频分析器媒体版视频。                        |
| `azure-media-player-vi-insights`  | 在自定义 Azure Media Player 中嵌入 VI Insights。                             |
| `control-vi-embedded-player`      | 嵌入 VI Player 并从外部控制它。                                    |
| `custom-index-location`           | 嵌入来自自定义外部位置的 VI Insights（可以是自定义 blob）。     |
| `embed-both-insights`             | VI Insights 的基本用法包括播放器和见解。                            |
| `embed-insights-with-AMP`         | 在自定义 Azure Media Player 中嵌入 VI Insights 小组件。                      |
| `customize-the-widgets`           | 将 VI 小组件嵌入自定义选项。                                     |
| `embed-both-widgets`              | 嵌入 VI Player 和 Insights，并在它们之间进行通信。                      |
| `url-generator`                   | 根据用户指定的选项生成小组件自定义嵌入 URL。             |
| `html5-player`                    | 在默认 HTML5 视频播放器中嵌入 VI Insights。                           |

## <a name="supported-browsers"></a>支持的浏览器

有关详细信息，请参阅[支持的浏览器](video-indexer-overview.md#supported-browsers)。

## <a name="next-steps"></a>后续步骤

有关如何查看和编辑视频分析器媒体版见解的信息，请参阅[查看和编辑视频分析器媒体版见解](video-indexer-view-edit.md)。

另请查看[视频分析器媒体版 CodePen](https://codepen.io/videoindexer/pen/eGxebZ)。
