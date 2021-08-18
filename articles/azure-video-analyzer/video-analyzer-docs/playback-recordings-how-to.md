---
title: 播放视频录制内容 - Azure 视频分析器
description: 可以使用 Azure 视频分析器进行连续视频录制，持续数周或数月将视频录制到云中。 还可以通过基于事件的录制将录制限制为你感兴趣的剪辑。 本文讨论如何播放这些录制内容。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 415edea10eaf635b88786d33bbd439bc386a57fc
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602188"
---
# <a name="playback-of-video-recordings"></a>播放视频录制内容 

## <a name="pre-read"></a>预读  

* [连续视频录制](continuous-video-recording.md)
* [基于事件的视频录制](event-based-video-recording-concept.md)

## <a name="background"></a>背景  

可以使用 Azure 视频分析器进行[连续视频录制 (CVR)](continuous-video-recording.md)，持续数周或数月将视频录制到云中。 还可以通过[基于事件的视频录制](event-based-video-recording-concept.md) (EVR) 将录制限制为你感兴趣的剪辑。 在这两种情况下，如果[管道](pipeline.md)使用 AI 生成推理结果，则应该[将这些结果与视频一起录制](record-stream-inference-data-with-video.md)。 

如果要评估视频分析器的功能，应该完成[有关 CVR 的教程](use-continuous-video-recording.md)或[有关通过推理元数据播放视频](record-stream-inference-data-with-video.md)的教程，你在其中可以学习如何使用 Azure 门户播放录制内容。

如果使用视频分析器 API 生成应用程序或服务，则除了查看有关[访问策略](access-policies.md)和[视频分析器播放器小组件](player-widget.md)的文章以外，还应查看以下内容以了解如何播放录制内容。

<!-- TODO - add a section here about 1P/3P SaaS and how to use widgets to allow end users to view videos without talking to ARM APIs -->

## <a name="determining-that-a-video-recording-is-ready-for-playback"></a>确定视频录制内容是否已准备好进行播放

视频分析器帐户已链接到 Azure 存储帐户，将视频录制到云中时，内容会写入[视频资源](terminology.md#video)。 你可以在录制完成后或在录制进行时[流式传输该内容](terminology.md#streaming)。 这通过 `canStream` [标志](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-05-01-preview/Videos.json)表示，视频资源的该标志将设置为 `true`。 

## <a name="video-analyzer-player-widget"></a>视频分析器播放器小组件
视频分析器提供将流通过 HLS 或 MPEG-DASH 协议交付到播放设备（客户端）所需的功能。 你可以使用视频分析器播放器小组件获取流式处理的 URL 和播放授权令牌，并在客户端应用中使用它们播放视频和推理元数据。

可以安装视频分析器播放器小组件以播放视频录制内容。 可以使用 `npm` 或 `yarn` 安装小组件，这会将其包含在自己的客户端应用程序中。 运行以下命令之一，将小组件包括在自己的应用程序中：

NPM：
```
npm install –-save @azure/video-analyzer-widgets
```
YARN：
```
yarn add @azure/video-analyzer-widgets 
```
或者，可以仿照以下示例，将 type="module" 添加到引用预生成位置的脚本元素，以嵌入现有的预生成脚本：

```
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script> 
``` 

## <a name="recording-and-playback-latencies"></a>录制和播放延迟

使用视频分析器录制到视频资源时，你将指定 [`segmentLength` 属性](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzer.json)，该属性指示模块在将视频写入到云之前聚合视频的最小持续时间（以秒为单位）。 例如，如果将 `segmentLength` 设置为 300，则在上传一段 5 分钟的“区块”之前，该模块将首先累积 5 分钟的视频，然后在下一个 5 分钟进入累积模式，然后再次上传。 增加 `segmentLength` 有助于降低 Azure 存储事务成本，因为读取次数和写入次数将不会超过每 `segmentLength` 秒一次。

因此，从视频分析器帐户处理视频将至少延迟这么长时间。 

确定播放延迟（延迟处于相机前事件发生的时间与可以在播放设备上观看的时间之间）的另一个因素是画面组 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 持续时间。 正如[通过使用 3 个简单的技术来降低实时流的延迟](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641)所解释的一样，GOP 持续时间越长，延迟越长。 通常会在监视和安全性方案中使用 IP 相机，将使用 GOP 的时间配置为超过 30 秒。 这会对总体延迟会产生很大的影响。

## <a name="next-steps"></a>后续步骤

[连续视频录制教程](use-continuous-video-recording.md)
