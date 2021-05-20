---
title: Azure Media Player 选项
description: Azure Media Player 嵌入代码只是一个 HTML5 视频标记，因此对于许多选项，你可以使用标准标记属性来设置选项。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 392ecff2ab120e713a07d130493a4a8339dae5ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92366864"
---
# <a name="options"></a>选项 #

## <a name="setting-options"></a>设置选项 ##

Azure Media Player 嵌入代码只是一个 HTML5 视频标记，因此对于许多选项，你可以使用标准标记属性来设置选项。

`<video controls autoplay ...>`

或者，可以使用数据设置属性来提供采用 [JSON](http://json.org/example.html) 格式的选项。 这也是设置不以视频标记为标准的选项的方式。

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

最后，如果未使用数据设置属性来触发播放器设置，则可以将具有播放器选项的对象作为 JavaScript 设置函数中的第二个参数传入。

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> 仅在设置源之前的第一次初始化时设置构造函数中的选项。  如果希望修改同一已初始化 Azure Media Player 元素上的选项，则必须在更改源之前更新选项。 可以使用 `myPlayer.options({/*updated options*/});` 更新 JavaScript 中的选项。 请注意，仅更改的选项会受到影响，以前设置的所有其他选项都将保留。

## <a name="individual-options"></a>个人选项 ##

> [!NOTE]
>视频标记属性只能为 true 或 false（布尔值），你只需包括该属性（无等于符号）即可将其打开，或排除它以将其关闭。 例如，若要打开控件：WRONG `<video controls="true" ...>` RIGHT `<video controls ...>` 用户遇到的最大问题是，尝试将 false 用作值来将这些值设置为 false（例如 controls="false"），但实际上会进行相反的操作并将值设置为 true，因为该属性仍包含在内。

### <a name="controls"></a>controls ###

控件选项用于设置播放器是否具有用户可与之交互的控件。 如果不使用控件，开始播放视频的唯一方法是使用自动播放属性或通过 API。

`<video controls ...>` 或 `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

如果自动播放为 true，则在加载页面后，视频会立即开始播放（与用户无任何交互）。

> [!NOTE]
> Windows Phone、Apple iOS 和 Android 等移动设备不支持此选项。 移动设备阻止自动播放功能，以防止过度使用使用者的每月数据计划（通常会消耗大量资源）。 在这种情况下，用户需要触摸/单击才能启动视频。

`<video autoplay ...>` 或 `{ "autoplay": true }`

### <a name="poster"></a>海报 ###
海报属性设置视频开始播放前显示的图像。 这通常是视频或自定义标题屏幕的帧。 一旦用户单击播放，图像就会消失。

`<video poster="myPoster.jpg" ...>` 或 `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

宽度属性设置视频的显示宽度。

`<video width="640" ...>` 或 `{ "width": 640 }`

### <a name="height"></a>高度 ###

高度属性设置视频的显示高度。

`<video height="480" ...>` 或 `{ "height": 480 }`

### <a name="plugins"></a>插件 ###

插件 JSON 确定与该 AMP 实例一起加载的哪些插件可用于配置插件可能具有的任何选项。

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

有关插件开发和使用的详细信息，请参阅[编写插件](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>其他选项 ###

可以使用采用 JSON 的 `data-setup` 参数在 `<video>` 标记上设置其他选项。
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

此值显式设置为 false。 如果设置为 false，则将允许在平台之间呈现相同的 Azure Media Player 外观。  此外，与名称相反，仍将启用触摸。

### <a name="fluid"></a>流体 ###

如果将此选项设置为 true，则视频元素会采用父容器的完整宽度，并且将调整高度以适应具有标准 16:9 纵横比的视频。

`<video ... data-setup='{"fluid": true}'>`

`fluid` 选项替代显式 `width` 和 `height` 设置。 此选项仅在 Azure Media Player 版本 `2.0.0` 及更高版本中可用。

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` 选项控制可供用户使用的 playbackSpeed 控件和播放速度设置集。 `playbackSpeed` 采用一个对象。 若要在控件条上启用播放速度控件，需要将对象的 `enabled` 属性设置为 true。 在标记中启用播放速度的示例：

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


`playbackSpeed` 设置的其他属性由 [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) 对象提供。

在 JavaScript 中设置播放速度选项的示例：

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

此选项仅在 Azure Media Player 版本 2.0.0 及更高版本中可用。

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

`staleDataTimeLimitInSec` 选项是一种优化，可让你配置要在 mediaSource 缓冲区中保留过时数据的秒数。 此项已默认禁用。

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

如果将“已启用”设置为 true，则可在实时流和实时存档中显示实时 CEA 字幕。 不需要标签属性，如果未包含该属性，则播放器将回退到默认标签。

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

此选项仅在 Azure Media Player 版本 2.1.1 及更高版本中可用。

## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](azure-media-player-quickstart.md)
