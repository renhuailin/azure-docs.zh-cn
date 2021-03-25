---
title: 媒体服务的媒体播放器概述
description: 哪些媒体播放器可与 Azure 媒体服务一起使用？ 目前有 Azure Media Player、Shaka 和 Video.js。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 3/08/2021
ms.openlocfilehash: 1a1d415b374818d9a51c87e78e7ac422fa374bc5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510364"
---
# <a name="media-players-for-media-services"></a>媒体服务的媒体播放器

可以将多个媒体播放器与媒体服务一起使用。

## <a name="azure-media-player"></a>Azure Media Player

Azure Media Player 是适用于各种浏览器和设备的视频播放器。 Azure Media Player 使用行业标准（如 HTML5、媒体源扩展 (MSE) 和加密媒体扩展插件 (EME)）来提供更丰富的自适应流式处理体验。 如果无法在设备或浏览器中提供这些标准，Azure Media Player 会采用 Flash 和 Silverlight 作为回退技术。 无论使用哪种播放技术，开发人员都有一个统一的 JavaScript 接口来访问 API。 Azure 媒体服务提供的内容可以在各种设备和浏览器中播放，而无需其他措施。

请参阅 [Azure Media Player 文档](https://docs.microsoft.com/azure/media-services/azure-media-player/azure-media-player-overview)。

## <a name="shaka"></a>Shaka

Shaka Player 是适用于自适应媒体的开源 JavaScript 库。 它在浏览器中播放自适应媒体格式（例如 DASH 和 HLS），而无需使用插件或 Flash。 相反，Shaka Player 使用开放 Web 标准媒体源扩展和加密媒体扩展。

请参阅[如何将 Shaka Player 与 Azure 媒体服务一起使用](how-to-shaka-player.md)。

## <a name="videojs"></a>Video.js

Video.js 是在浏览器中播放自适应媒体格式（例如 DASH 和 HLS）的视频播放器。 Video.js 使用开放 Web 标准媒体源扩展和加密媒体扩展。 它支持在桌面和移动设备上播放视频。 可在 https://docs.videojs.com/ 中找到其官方文档。

请参阅[如何将 Video.js 与 Azure 媒体服务一起使用](how-to-video-js-player.md)。


## <a name="next-steps"></a>后续步骤 ##

- [Azure Media Player 快速入门](../azure-media-player/azure-media-player-quickstart.md)
