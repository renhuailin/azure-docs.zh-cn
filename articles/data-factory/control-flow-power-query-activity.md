---
title: Azure 数据工厂中的 Power Query 活动
description: 了解如何将 Power Query 活动用于数据工厂管道中的数据整理功能
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: be5c2cbe0d4f364abb68ad8b01f00ca2c8396806
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358525"
---
# <a name="power-query-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Power Query 活动

Power Query 活动可让你生成和执行 Power Query 混合活动，以便在数据工厂管道中大规模执行数据整理操作。 可以通过“新建资源”菜单选项创建新的 Power Query 混合活动，也可以通过将 Power 活动添加到管道来这样做。

![此屏幕截图显示了工厂资源窗格中的 Power Query。](media/data-flow/power-query-activity-1.png)

可以直接在 Power Query 混合活动编辑器中进行操作，以执行交互式数据浏览操作并保存工作。 完成后，可以获取 Power Query 活动并将其添加到管道。 Azure 数据工厂会使用 Azure 数据工厂的数据流 Spark 环境自动将其横向扩展并使数据整理可以操作。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>转换为数据流脚本

为了通过 Power Query 活动实现缩放，Azure 数据工厂会将 ```M``` 脚本转换为数据流脚本，以便你可以使用 Azure 数据工厂数据流 Spark 环境大规模执行 Power Query。 请通过无代码数据准备创作整理数据流。 有关可用函数的列表，请参阅[转换函数](wrangling-functions.md)。

## <a name="settings"></a>设置

* Power Query：选择要执行的现有 Power Query 或新建一个。
* 在 Azure IR 上运行：选择现有 Azure Integration Runtime 来为 Power Query 定义计算环境，或者新建一个。
* 计算类型：如果选择默认自动解析集成运行时，则可以选择要应用于 Power Query 执行的 Spark 群集计算的计算类型。
* 内核计数：如果选择默认自动解析集成运行时，则可以选择要应用于 Power Query 执行的 Spark 群集计算的内核数。

## <a name="sink"></a>接收器

选择在 Spark 上执行 Power Query M 脚本后，要用于登陆已转换数据的数据集。 有关配置接收器的详细信息，请访问[数据流接收器](data-flow-sink.md)文档。

## <a name="mapping"></a>映射

在“映射”选项卡中，可以配置从 Power Query 活动输出到所选接收器的目标架构的列映射。 在[数据流接收器映射文档](data-flow-sink.md#field-mapping)中了解有关列映射的详细信息。

## <a name="next-steps"></a>后续步骤

通过 [Azure 数据工厂中的 Power Query](wrangling-tutorial.md) 详细了解数据整理概念
