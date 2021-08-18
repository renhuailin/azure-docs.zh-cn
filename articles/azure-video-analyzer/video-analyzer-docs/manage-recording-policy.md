---
title: 使用 Azure 视频分析器管理录制策略
description: 本主题说明如何使用 Azure 视频分析器管理录制策略。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 31b9d4f14a9c60687b9a6e8f32fcc3a1fba82856
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601582"
---
# <a name="manage-recording-policy-with-video-analyzer"></a>使用视频分析器管理录制策略

可以使用 Azure 视频分析器在数周、数月或数年的时间内将实时视频[录制](video-recording.md)到云中。 此录制可以是[连续的](continuous-video-recording.md)，也可以是稀疏或[基于事件的](event-based-video-recording-concept.md)。 不管是哪种情况，录制都可以长达数年。 可以使用 Azure 存储中内置的[生命周期管理工具](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)来管理云存档的时间长度（天）。  

视频分析器帐户链接到 Azure 存储帐户，在录制到视频资源时，媒体数据将写入存储帐户中的容器。 生命周期管理允许你为存储帐户定义[策略](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy)，你可以在其中指定[规则](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules)，如下所示。

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
* 指定当 blob 期限超过 30 天时，它们将从[热访问层移动到冷访问层](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)。
* 如果 blob 期限超过 90 天，则将其删除。

使用视频分析器录制到视频资源时，请指定 `segmentLength` 属性，该属性指定在将视频写入存储帐户之前要聚合的视频的最短持续时间（秒）。 你的视频资源将包含一系列片段，每个片段的创建时间戳都晚于上一个片段 `segmentLength`。 生命周期管理策略启动后，它会删除早于指定阈值的段。 但你可以继续通过视频分析器 API 访问和播放剩余的片段。 有关详细信息，请参阅[播放录制内容](playback-recordings-how-to.md)。 

## <a name="limitations"></a>限制

以下是生命周期管理的一些已知限制：

* 策略中最多可以有 100 个规则，每个规则最多可以指定 10 个容器。 因此，如果需要使用不同的录制策略（例如，面向停车场的摄像机存档 3天，装货码头的摄像机存档 30 天，收银台后面的摄像机存档 180 天），那么使用一个存储帐户，你最多可以为 1000 个摄像机自定义规则。
* 生命周期管理策略更新不是即时的。 有关更多详细信息，请参阅[常见问题解答部分](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq)。
* 如果选择应用将 blob 移动到冷访问层的策略，则该存档部分的播放可能会受到影响。 你可能会看到其他延迟，或偶尔发生的错误。 视频分析器不支持在存档层中播放内容。

## <a name="next-steps"></a>后续步骤

[播放录制内容](playback-recordings-how-to.md)
