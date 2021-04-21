---
title: 在 Azure 媒体服务中使用 Video.js 播放器
description: 本文介绍如何在 Azure 媒体服务中使用 HTML 视频对象和 JavaScript
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 0ac00ecbbe5e0a72bccf6effe5e82fc7d973c91e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281191"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>如何在 Azure 媒体服务中使用 Video.js 播放器

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>概述

Video.js 是为 HTML5 领域打造的 Web 视频播放器。 它在浏览器中播放自适应媒体格式（例如 DASH 和 HLS），而无需使用插件或 Flash。 Video.js 使用开放 Web 标准媒体源扩展和加密媒体扩展。 此外，它支持在桌面设备和移动设备上播放视频。

在 [https://docs.videojs.com/](https://docs.videojs.com/) 中可以找到其官方文档。

## <a name="sample-code"></a>代码示例
本文的示例代码可在 [Azure-Samples/media-services-3rdparty-player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples) 中获取。

## <a name="implement-the-player"></a>实现播放器

1. 创建用于承载播放器的 `index.html` 文件。 添加以下代码行（在适当的情况下可以替换为更新的版本）：

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. 添加包含以下代码的 `index.js` 文件：

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. 请将 `manifestUrl` 替换为资产流式处理定位符中的 HLS 或 DASH URL，该 URL 可在 Azure 门户的流式处理定位符页面中找到。
    ![流式处理定位符 URL](media/player-shaka-player-how-to/streaming-urls.png)

4. 请将 `protocolType` 替换为以下选项：

    - 适用于 HLS 协议的“application/x-mpegURL”
    - 适用于 DASH 协议的“application/dash+xml”

### <a name="set-up-captions"></a>设置字幕

运行 `addRemoteTextTrack` 方法，并进行以下替换：

- 将 `subtitleKind` 替换为 `"captions"`、`"subtitles"`、`"descriptions"` 或 `"metadata"`  
- 将 `caption` 替换为 .vtt 文件路径（vtt 文件需位于同一宿主中，以避免发生 CORS 错误）
- 将 `subtitleLang` 替换为语言为 BCP 47 的代码，例如，`"eng"` 表示英语，`"es"` 表示西班牙语
- 将 `subtitleLabel` 替换为字幕的所需显示名称

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>设置令牌身份验证

必须在请求头的授权字段中设置令牌。 为了避免 CORS 出现问题，只能在其 URL 中包含 `'keydeliver'` 的请求中设置此令牌。 下面代码行应该可以执行该工作：

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

然后，必须将以上函数附加到 `videojs.Hls.xhr.beforeRequest` 事件。

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>设置 AES-128 加密

无需经过任何额外的配置，Video.js 即可支持 AES-128 加密。 

> [!NOTE] 
> 目前存在加密和 HLS/DASH CMAF 内容无法播放的[问题](https://github.com/videojs/video.js/issues/6717)。

### <a name="set-up-drm-protection"></a>设置 DRM 保护

若要支持 DRM 保护，必须添加 [videojs-contrib-eme](https://github.com/videojs/videojs-contrib-eme) 官方扩展。 也可以使用 CDN 版本。

1. 在上面详述的 `index.js` 文件中，必须通过先添加 `videoJS.eme();`，再添加视频源，来初始化 EME 扩展：

   ```javascript
    videoJS.eme();
   ```

2. 现在，可按如下所示定义 DRM 服务的 URL，以及相应许可证的 URL：

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>获取许可证 URL

若要获取许可证 URL，可以：

- 查阅 DRM 提供商配置
- 或者，如果你使用的是示例，请查阅对 VOD 运行 setup-vod.ps1 PowerShell 脚本，或者对实时流运行 start-live.ps1 脚本时生成的 `output.json` 文档 。 在此文件中还可以找到 KID。

#### <a name="using-tokenized-drm"></a>使用标记化 DRM

若要支持标记化 DRM 保护，必须将以下行添加到播放器的 `src` 属性：

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>后续步骤

- [使用 Azure Media Player](../azure-media-player/azure-media-player-overview.md)  
- [快速入门：加密内容](drm-encrypt-content-how-to.md)
