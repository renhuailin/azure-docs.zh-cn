---
title: 实时听录：Azure 媒体服务 description: 了解 Azure 媒体服务实时听录。  
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: '' ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: ne ms.topic: how-to ms.date: 08/31/2020 ms.author: inhenkel

---

# <a name="live-transcription-preview"></a>实时听录（预览版）

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 媒体服务提供了不同协议的视频、音频和文本。 如果使用 MPEG-DASH 或 HLS/CMAF 发布实时传送流，并包含视频和音频，我们的服务将以 IMSC1.1 兼容的 TTML 格式传递听录文本。 该传递内容打包为 MPEG-4 Part 30 (ISO/IEC 14496-30) 片段。 如果通过 HLS/TS 使用内容传递，则文本将作为分块 VTT 传递。

启用实时听录需要额外付费。 请查看[媒体服务定价页](https://azure.microsoft.com/pricing/details/media-services/)的“实时视频”部分中的定价信息。

本文介绍如何在使用 Azure 媒体服务流式传输实时事件时启用实时听录。 继续操作之前，请确保熟悉媒体服务 v3 REST API 的用法（请参阅[此教程](stream-files-tutorial-with-rest.md)了解详细信息）。 还应熟悉[实时传送流](stream-live-streaming-concept.md)的概念。 建议完成[使用媒体服务进行实时流式传输](stream-live-tutorial-with-api.md)教程。

## <a name="live-transcription-preview-regions-and-languages"></a>实时听录预览区域和语言

以下区域中提供了实时听录功能：

- 东南亚
- 西欧
- 北欧
- 美国东部
- 美国中部
- 美国中南部
- 美国西部 2
- 巴西南部

这是可听录语言的列表，请在 API 中使用语言代码。

| 语言 | 语言代码 |
| -------- | ------------- |
| 加泰罗尼亚语  | ca-ES |
| 丹麦语（丹麦） | da-DK |
| 德语（德国） | de-DE |
| 英语（澳大利亚） | en-AU |
| 英语（加拿大） | en-CA |
| 英语（英国） | en-GB |
| 英语（印度） | en-IN |
| 英语（新西兰） | en-NZ |
| 英语（美国） | zh-CN |
| 西班牙语(西班牙) | es-ES |
| 西班牙语(墨西哥) | es-MX |
| 芬兰语（芬兰） | fi-FI |
| 法语（加拿大） | fr-CA |
| 法语（法国） | fr-FR |
| 意大利语（意大利） | it-IT |
| 荷兰语（荷兰） | nl-NL |
| 葡萄牙语（巴西） | pt-BR |
| 葡萄牙语(葡萄牙) | pt-PT |
| 瑞典语（瑞典） | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>创建实时事件和实时听录

若要在启用听录的情况下创建实时事件，请使用 2019-05-01-preview API 版本发送 PUT 操作，例如：

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

该操作具有以下主体（其中以 RTMP 作为引入协议创建了基本直通实时事件）。 注意增加了 transcriptions 属性。

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "PassthroughBasic"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>在实时事件开始后开始或停止

可以在实时事件处于运行状态时启动和停止实时听录。 有关启动和停止实时事件的详细信息，请参阅[通过媒体服务 v3 API 进行开发](media-services-apis-overview.md#long-running-operations)中的“长时间运行的操作”部分。

若要启用实时听录或更新听录语言，请修补实时事件以包含“transcriptions”属性。 若要禁用实时听录，请删除实时事件对象中的“transcriptions”属性。  

> [!NOTE]
> 不支持在实时事件期间多次启用或禁用听录。

这是用于启用实时听录的示例调用。

补丁：```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>听录传递和播放

查看[动态打包概述](encode-dynamic-packaging-concept.md#to-prepare-your-source-files-for-delivery)一文，了解我们的服务如何使用动态打包来传递不同协议的视频、音频和文本。 如果使用 MPEG-DASH 或 HLS/CMAF 发布实时传送流，并包含视频和音频，我们的服务将以 IMSC1.1 兼容的 TTML 格式传递听录文本。 该传递内容打包为 MPEG-4 Part 30 (ISO/IEC 14496-30) 片段。 如果通过 HLS/TS 使用内容传递，则文本将作为分块 VTT 传递。 可以使用 Web 播放器（如 [Azure Media Player](player-use-azure-media-player-how-to.md)）播放流。  

> [!NOTE]
> 如果使用 Azure Media Player，请使用 2.3.3 或更高版本。

## <a name="known-issues"></a>已知问题

对于预览版，实时听录存在以下已知问题：

- 应用需要使用预览版 API，如[媒体服务 v3 OpenAPI 规范](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)中所述。
- 数字版权管理 (DRM) 保护不适用于文本轨，只能采用 AES 信封加密。

## <a name="next-steps"></a>后续步骤

* [媒体服务概述](media-services-overview.md)
