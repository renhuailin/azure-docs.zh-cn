---
title: Azure 数据工厂中的 Power Query 活动
description: 了解如何使用数据工厂管道中的数据整理功能的 Power Query 活动
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: fc4f20db55f8e7e0b2f92cb8309c1c128b235089
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385449"
---
# <a name="power-query-activity-in-data-factory"></a>数据工厂中的 Power query 活动

Power Query 活动允许生成和执行 Power Query 混合，以在数据工厂管道中大规模执行数据整理。 可以通过 "新资源" 菜单选项或通过将电源活动添加到管道来创建新的 Power Query 混合。

![显示工厂资源窗格 Power Query 的屏幕截图。](media/data-flow/power-query-wrangling.png)

以前，Azure 数据工厂中的数据整理是从 "数据流" 菜单选项中编写的。 此操作已更改为使用新的 Power Query 活动进行创作。 可以直接在 Power Query 混合编辑器中进行操作，以执行交互式数据浏览，并保存工作。 完成后，可以获取 Power Query 活动，并将其添加到管道。 Azure 数据工厂将使用 Azure 数据工厂的数据流 Spark 环境自动扩展并操作数据整理。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>转换为数据流脚本

为了通过 Power Query 活动实现缩放，Azure 数据工厂将脚本转换 ```M``` 为数据流脚本，以便可以使用 Azure 数据工厂数据流 Spark 环境大规模执行 Power Query。 使用无代码数据准备创作你的整理数据流。 有关可用函数的列表，请参阅 [转换函数](wrangling-functions.md)。

## <a name="next-steps"></a>后续步骤

了解有关使用[Azure 数据工厂中 Power Query 的](wrangling-tutorial.md)数据整理概念的详细信息
