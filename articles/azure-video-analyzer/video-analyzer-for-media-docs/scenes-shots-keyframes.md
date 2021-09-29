---
title: Azure 视频分析器媒体版（以前称为视频索引器）场景、镜头和关键帧
description: 本主题概述 Azure 视频分析器媒体版（以前称为视频索引器）场景、镜头和关键帧。
ms.topic: how-to
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b6825b5a50793589a07c16ef274d1e4231a2d4e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655936"
---
# <a name="scenes-shots-and-keyframes"></a>场景、镜头和关键帧

Azure 视频分析器媒体版（以前称为视频索引器）支持基于结构和语义属性将视频分段为临时单元。 此功能使客户能够根据不同的粒度轻松浏览、管理和编辑视频内容。 例如，根据本主题中所述的场景、镜头和关键帧。   

![场景、镜头和关键帧](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>场景检测  
 
视频分析器媒体版根据视觉提示确定视频中的场景何时发生了变化。一个场景描述一个事件，它由一系列在语义上相关的连续镜头组成。 场景缩略图是其基础镜头的第一个关键帧。 视频分析器媒体版根据连续镜头的颜色一致性将视频分割成场景，并检索每个场景的开始时间和结束时间。 场景检测是一项极具挑战性的工作，因为它涉及对视频语义的量化。

> [!NOTE]
> 适用于至少包含 3 个场景的视频。

## <a name="shot-detection"></a>镜头检测

视频分析器媒体版根据视觉提示确定视频中的镜头何时发生了变化，方法是跟踪相邻帧的配色方案中的颜色突变和渐变转换。 镜头元数据包括开始时间和结束时间，以及该镜头中所包含的关键帧的列表。 镜头是在同一时间由同一台相机所拍摄的连续帧。

## <a name="keyframe-detection"></a>关键帧检测

视频分析器媒体版选择最能表示每个镜头的帧。 关键帧是根据审美属性（例如，对比度和稳定性）从整个视频中选取的代表性帧。 视频分析器媒体版检索关键帧 ID 列表作为镜头元数据的一部分，客户可以据此将关键帧提取为高分辨率图像。  

### <a name="extracting-keyframes"></a>提取关键帧

若要提取视频的高分辨率关键帧，必须先上传视频并编制视频索引。

![关键帧](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-analyzer-for-media-website"></a>使用视频分析器媒体版网站

若要使用视频分析器媒体版网站提取关键帧，请上传视频并编制视频索引。 视频索引编制完成后，请单击“下载”按钮，然后选择“项目 (ZIP)” 。 该操作会将项目文件夹下载到你的计算机中。 

![显示已选中“下载”下拉列表中的“项目”的屏幕截图。](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
解压缩并打开该文件夹。 在 _KeyframeThumbnail 文件夹中，你可以找到从视频中提取的所有关键帧。 

#### <a name="with-the-video-analyzer-for-media-api"></a>使用视频分析器媒体版 API

若要使用视频索引器 API 获取关键帧，请使用[上传视频](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)调用上传视频和编制视频索引。 视频索引编制完成后，调用[获取视频索引](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index)。 该操作将为你提供视频索引器从 JSON 文件中的内容中提取的所有见解。  

你将获得关键帧 ID 列表，作为每个镜头元数据的一部分。 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

现在需要通过[获取缩略图](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail)调用运行每个关键帧 ID。 该操作会将每个关键帧图像下载到你的计算机中。 

## <a name="editorial-shot-type-detection"></a>编辑镜头类型检测

关键帧与输出的 JSON 中的镜头相关联。 

与“见解 (JSON)”中的单个镜头关联的镜头类型表示其编辑类型。 在将视频编辑为片段、预告片或出于艺术目的搜索特定样式的关键帧时，这些镜头类型特征非常有用。 根据每个镜头第一个关键帧的分析来确定不同的类型。 通过第一个关键帧中人脸所占比例、大小和位置对镜头进行标识。 

根据相机和画面中人脸之间的距离来确定镜头大小和比例。 利用这些属性，视频分析器媒体版可检测以下镜头类型：

* 全景：展示人物身体全部。
* 中景：展示人物上半身和脸部。
* 特写：主要展示人脸。
* 大特写：人脸充满整个画面。 

镜头类型也可通过拍摄对象相对于画面中心的位置来确定。 该属性在视频分析器媒体版中定义以下镜头类型：

* 左人脸：人物出现在画面左侧。
* 中心人脸：人物出现在画面中心。
* 右人脸：人物出现在画面右侧。
* 户外：人物出现在户外环境中。
* 室内：人物出现在室内环境中。

其他特征：

* 两张人脸：展示两张人脸（中等大小）。
* 多张人脸：两人以上。


## <a name="next-steps"></a>后续步骤

[了解 API 生成的视频分析器媒体版输出](video-indexer-output-json-v2.md#scenes)
