---
title: 使用实时视频分析管理录制策略 - Azure
description: 本主题说明如何使用实时视频分析管理录制策略。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: c9ec5a2efd56a0a2872f946dd23db72d2e7836b9
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273640"
---
# <a name="manage-recording-policy-with-live-video-analytics"></a>使用实时视频分析管理录制策略

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

可以使用 IoT Edge 上的实时视频分析进行[连续视频录制](continuous-video-recording-concept.md)，持续数周或数月将视频录制到云中。 可以使用 Azure 存储中内置的[生命周期管理工具](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal)来管理云存档的时间长度（天）。  

媒体服务帐户已链接到 Azure 存储帐户，将视频录制到云中时，内容会写入媒体服务[资产](../latest/assets-concept.md)。 每个资产都映射到存储帐户中的一个容器。 生命周期管理允许你为存储帐户定义[策略](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal)，你可以在其中指定[规则](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal#lifecycle-management-rule-definition)，如下所示。

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

上述规则：

* 应用于存储帐户中的所有块 blob。
* 指定当 blob 期限超过 30 天时，它们将从[热访问层移动到冷访问层](../../storage/blobs/access-tiers-overview.md?tabs=azure-portal)。
* 如果 blob 期限超过 90 天，则将其删除。

使用实时视频分析来记录到资产时，请指定 `segmentLength` 属性，该属性指示模块在将视频写入云之前聚合视频的最小持续时间（以秒为单位）。 你的资产将包含一系列片段，每个片段的创建时间戳都在上一个之后 `segmentLength`。 生命周期管理策略启动后，它会删除早于指定阈值的段。 但你可以继续通过媒体服务 API 访问和播放剩余的片段。 有关详细信息，请参阅[播放录制内容](playback-recordings-how-to.md)。 

## <a name="limitations"></a>限制

以下是生命周期管理的一些已知限制：

* 策略中最多可以有 100 个规则，每个规则最多可以指定 10 个容器。 因此，如果你需要具有不同的录制策略（例如，正对停车场的摄像头为 3 天存档、装货码头的摄像头为 30 天、收银台后面的摄像头为 180 天），请使用媒体服务帐户，为最多 1000 个摄像头自定义规则。
* 生命周期管理策略更新不是即时的。 有关更多详细信息，请参阅[常见问题解答部分](../../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal#faq)。
* 如果选择应用将 blob 移动到冷访问层的策略，则该存档部分的播放可能会受到影响。 你可能会看到其他延迟，或偶尔发生的错误。 媒体服务不支持在存档层中播放内容。

## <a name="next-steps"></a>后续步骤

[播放录制内容](playback-recordings-how-to.md)