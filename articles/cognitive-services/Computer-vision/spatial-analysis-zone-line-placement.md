---
title: 空间分析区域和线条放置
titleSuffix: Azure Cognitive Services
description: 了解如何通过空间分析设置区域和线条
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: cfd3bc406407298c6daf7723cb684911d7c9a9cf
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284662"
---
# <a name="zone-and-line-placement-guide"></a>区域和线条放置指南

本文将指导你如何为空间分析操作定义区域和线条，以准确分析人在空间中的运动。 这适用于所有操作。 

区域和线条使用 JSON SPACEANALYSIS_CONFIG 参数进行定义。 有关详细信息，请参阅[空间分析操作](spatial-analysis-operations.md)一文。

## <a name="guidelines-for-drawing-zones"></a>区域绘制指南

请记住，每个空间都不同；需要根据需求更新位置或大小。

如果要查看相机视图的特定部分，请创建最大区域，覆盖你感兴趣的特定楼层区，但不包括不感兴趣的其他区域。 这提高了所收集数据的准确性，并防止来自你不想跟踪的区域的误报。在放置多边形的角时要小心，确保它们在你想要跟踪的区域之内。  

### <a name="example-of-a-well-shaped-zone"></a>形状规整的区域示例

这个区域应该足够大，可以容纳三个人站在每个边缘，并关注感兴趣的区域。 空间分析可以识别出哪些人的脚放置在这个区域，所以当在 2D 图像上绘制区域时，可以把这个区域想象成铺在地板上的地毯。

![形状规整的区域](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>形状不规整的区域示例

下面的示例显示了形状欠佳的区域。 在这些示例中，感兴趣的区域是“游戏时间”显示器前的空间。

区域不在地面上。

![形状欠佳的区域](./media/spatial-analysis/zone-not-on-floor.png) 

区域太小。

![区域太小](./media/spatial-analysis/zone-too-small.png)

区域没有完全捕获显示器周围的范围。

![区域没有完全捕获末端周围的范围](./media/spatial-analysis/zone-bad-capture.png)

区域太接近相机图像的边缘，不能捕获正确的显示器。

![区域太接近相机图像的边缘，不能捕获正确的显示器](./media/spatial-analysis/zone-edge.png)

部分区域被货架阻挡，因此不能完全看到人员和地面。

![部分区域被阻挡，因此不能完全看到人员](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>形状规整的线条示例

线条长度应足以容纳整个入口。 空间分析将识别出脚跨过这条线的人员，所以当你在 2D 图像上画线时，请想象在地面上画线条的样子。 

如果可以，将线条延伸至比实际入口更宽的位置。 如果此做法不会导致额外的交叉（如下图所示，当线条靠墙时），则延伸它。

![形状规整的线条](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>形状不规整的线条示例

下面的示例显示了形状欠佳的线条。

线条没有覆盖地面上的整个入口。

![线条没有覆盖地面上的整个入口](./media/spatial-analysis/zone-line-bad-coverage.png)

线条太高，没有覆盖整个门。

![线条太高，没有覆盖整个门](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>后续步骤

* [部署人员计数 Web 应用程序](spatial-analysis-web-app.md)
* [配置空间分析操作](./spatial-analysis-operations.md)
* [日志记录和故障排除](spatial-analysis-logging.md)
* [相机放置指南](spatial-analysis-camera-placement.md)
