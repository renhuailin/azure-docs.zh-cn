---
title: Azure Media Player API 方法
description: 通过 Azure Media Player API，无论浏览器是通过 HTML5 视频、Flash、Silverlight 还是任何其他受支持的播放技术来播放视频，都可通过 JavaScript 与视频交互。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 3b199223c7e77f31cd4bf6e99d6a170f6868848a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448689"
---
# <a name="api"></a>API #

通过 Azure Media Player API，无论浏览器是通过 HTML5 视频、Flash、Silverlight 还是任何其他受支持的播放技术来播放视频，都可通过 JavaScript 与视频交互。

## <a name="referencing-the-player"></a>引用播放器 ##

若要使用 API 函数，需要访问 player 对象。 幸运的是，很容易获得此对象。 只需确保视频标记具有 ID 即可。 示例嵌入代码具有一个 ID `vid1`。 如果一个页面上有多个视频，请确保每个视频标记都具有唯一的 ID。

`var myPlayer = amp('vid1');`

> [!NOTE]
> 如果尚未通过 data-setup 属性或其他方法初始化播放器，这还将初始化播放器。

## <a name="wait-until-the-player-is-ready"></a>等到播放器准备就绪 ##

Azure Media Player 设置视频和 API 所需的时间取决于所使用的播放技术。 HTML5 的加载速度通常比 Flash 或 Silverlight 要快得多。 因此，应使用播放器的“ready”函数来触发任何需要播放器 API 的代码。

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OR

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API 方法 ##

现在你已有权访问准备就绪的播放器，接下来可控制视频、获取值或响应视频事件。 Azure Media Player API 函数名称会尝试采用 [HTML5 媒体 API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)。 主要区别是，对视频属性使用 getter/setter 函数。

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>注册事件 ##
应在首次初始化播放器后直接注册事件，确保所有事件均适当地报告给应用程序，而且应在就绪事件之外完成。

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>后续步骤 ##

<!---Some context for the following links goes here--->
- [Azure Media Player 快速入门](azure-media-player-quickstart.md)