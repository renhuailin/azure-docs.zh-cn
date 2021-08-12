---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 68965017dc3f6d062f5a5c18acb1774d38e385c4
ms.sourcegitcommit: 20abee54e48f9b40b83d39c5b970bd0193812cb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2021
ms.locfileid: "113659402"
---
作为最低要求，我们建议在初始训练集中每个标记使用至少 30 张图像。 此外还需要收集一些额外的图像，以便在训练模型后测试模型。

为了有效地训练模型，请使用具有视觉多样性的图像。 选择在以下方面有所不同的图像：
* 照相机角度
* 照明
* background
* 视觉样式
* 个人/分组主题
* 大小
* type

此外，请确保所有训练图像满足以下条件：
* .jpg、.png、.bmp 或 .gif 格式
* 大小不超过 6 MB （预测图像不超过 4 MB）
* 最短的边不小于 256 像素；任何小于此像素的图像将通过自定义影像服务自动纵向扩展

> [!NOTE]
> 是否需要一组范围更广的图像来完成训练？ Trove 是一个 Microsoft Garage 项目，可用于收集和购买图像集以便进行训练。 收集图像后，可以通过一般方式下载映像，然后将其导入到自定义视觉项目。 若要了解详细信息，请访问 [Trove 页面](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3)。
