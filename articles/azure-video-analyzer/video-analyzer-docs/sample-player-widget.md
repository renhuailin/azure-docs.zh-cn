---
title: Azure 视频分析器播放器小组件示例
description: 此参考文章简要概述了视频分析器播放器小组件示例应用程序
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 08/13/2021
ms.openlocfilehash: abea3bdd1098dbb32be35d632c7a8b0ad51fc49e
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058484"
---
# <a name="azure-video-analyzer-player-widget-sample"></a>Azure 视频分析器播放器小组件示例

此示例应用程序展示了视频分析器的播放器小组件与视频播放、区域绘制和视频剪辑生成功能的集成。

* 克隆 [AVA C# 示例存储库](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)
* 按照 [src/video-player 文件夹中的 README](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/blob/main/src/video-player/README.md) 中的说明操作

## <a name="get-started"></a>开始使用
按照 README 说明操作后，应用会在 http://localhost:3000/ 上运行
1. 单击“获取视频”
2. 在视频分析器帐户中，从包含所有视频的下拉列表中选择一个视频

## <a name="video-player"></a>视频播放器
“视频播放器”页面显示典型的播放器，如门户中所见。  
![视频播放器的屏幕截图。](./media/sample-player-widget/widget-video-player.png)

## <a name="zone-drawer"></a>区域抽屉
使用区域抽屉，你可以通过在视频中绘制多边形和绘制线条来创建区域。 还可以保存这些区域和线条，以接收相应区域和线条的坐标。  
  
**示例：**
```json
  {
    "@type": "#Microsoft.VideoAnalyzer.NamedLineString",
    "name": "Line 1",
    "line": [
      {
        "x": 0.6987951807228916,
        "y": 0.4430992736077482
      },
      {
        "x": 0.6987951807228916,
        "y": 0.7046004842615012
      }
    ]
  }{
    "@type": "#Microsoft.VideoAnalyzer.NamedPolygonString",
    "name": "Zone 2",
    "polygon": [
      {
        "x": 0.10575635876840696,
        "y": 0.7481840193704601
      },
      {
        "x": 0.16599732262382866,
        "y": 0.7796610169491526
      },
      {
        "x": 0.07764390896921017,
        "y": 0.9007263922518159
      },
      {
        "x": 0.024096385542168676,
        "y": 0.8547215496368039
      }
    ]
  }
```
可以使用“复制到剪贴板”按钮复制这些坐标。 可以使用每个区域和线条旁边的三个点重命名和删除区域和线条。  
![区域抽屉的屏幕截图。](./media/sample-player-widget/widget-zone-drawer.png)

## <a name="video-clips"></a>视频剪辑
使用视频剪辑可以选择开始日期和时间以及结束日期和时间。 可以使用“添加”按钮生成视频剪辑。
可以从下拉列表中选择任何生成的剪辑，其中，每个剪辑的标题为开始和结束日期和时间。
（示例：格式）然后，可以使用典型的视频播放器格式观看视频剪辑。  
![视频剪辑的屏幕截图。](./media/sample-player-widget/widget-video-clips.png)

## <a name="next-steps"></a>后续步骤
按照这些[说明](./player-widget.md)尝试创建自己的自定义视频分析器播放器小组件

