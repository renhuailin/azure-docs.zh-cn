---
title: Azure 数据工厂中的 Power Query 活动
description: 了解如何将 Power Query 活动用于数据工厂管道中的数据整理功能
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: ab19b62ff4aaca1b3357d586e3bba4252e8a5d9e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738262"
---
# <a name="power-query-activity-in-data-factory"></a>数据工厂中的 Power Query 活动

Power Query 活动可让你生成和执行 Power Query 混合活动，以便在数据工厂管道中大规模执行数据整理操作。 可以通过“新建资源”菜单选项创建新的 Power Query 混合活动，也可以通过将 Power 活动添加到管道来这样做。

![此屏幕截图显示了工厂资源窗格中的 Power Query。](media/data-flow/power-query-wrangling.png)

以前是通过“数据流”菜单选项创作 Azure 数据工厂中的数据整理。 此功能已更改为通过新的 Power Query 活动进行创作。 可以直接在 Power Query 混合活动编辑器中进行操作，以执行交互式数据浏览操作并保存工作。 完成后，可以获取 Power Query 活动并将其添加到管道。 Azure 数据工厂会使用 Azure 数据工厂的数据流 Spark 环境自动将其横向扩展并使数据整理可以操作。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>转换为数据流脚本

为了通过 Power Query 活动实现缩放，Azure 数据工厂会将 ```M``` 脚本转换为数据流脚本，以便你可以使用 Azure 数据工厂数据流 Spark 环境大规模执行 Power Query。 请通过无代码数据准备创作整理数据流。 有关可用函数的列表，请参阅[转换函数](wrangling-functions.md)。

## <a name="next-steps"></a>后续步骤

通过 [Azure 数据工厂中的 Power Query](wrangling-tutorial.md) 详细了解数据整理概念
