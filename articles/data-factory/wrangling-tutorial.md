---
title: Azure 数据工厂中的整理数据流入门
description: 关于如何使用整理数据流在 Azure 数据工厂中准备数据的教程
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 43ff1591730f240e1ae3ebcfc4066fdc34835372
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129355598"
---
# <a name="prepare-data-with-data-wrangling"></a>使用数据整理准备数据

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

数据工厂中的数据整理能让你在 ADF 中本地构建交互式 Power Query 混合，然后在 ADF 管道内大规模执行。

## <a name="create-a-power-query-activity"></a>创建 Power Query 活动

可通过两种方法在 Azure 数据工厂中创建 Power Query。 一种方法是单击加号图标，然后在工厂资源窗格中选择“Power Query”。

:::image type="content" source="media/data-flow/power-query-wrangling.png" alt-text="显示工厂资源窗格中 Power Query 的屏幕截图。":::

另一种方法是在管道画布的“活动”窗格中进行。 打开 Power Query 可折叠，将 Power Query 活动拖到画布上。

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="突出显示数据整理选项的屏幕截图。":::

## <a name="author-a-power-query-data-wrangling-activity"></a>创作 Power Query 数据整理活动

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWJd3T]
> 
为 Power Query 混合添加“源数据集”。 可以选择现有数据集，也可以创建新数据集。 保存混合后，可以将 Power Query 数据整理活动添加到管道，并选择一个接收器数据集来指示 ADF 将数据放置到何处。 虽然可以选择一个或多个源数据集，但此时只允许选择一个接收器。 接收器数据集是可选选项，但源数据集至少要选择一个。

:::image type="content" source="media/wrangling-data-flow/tutorial4.png" alt-text="整理":::

单击“创建”以打开 Power Query 在线混合编辑器。

首先，为混合编辑器选择数据集源。

:::image type="content" source="media/wrangling-data-flow/power-query-new-source.png" alt-text="Power Query 源。":::

完成 Power Query 的构建后，可以保存它并将混合作为活动添加到管道中。 此时将设置接收器数据集属性。

:::image type="content" source="media/wrangling-data-flow/power-query-new-sink.png" alt-text="Power Query 接收器。":::

使用无代码数据准备来创作整理 Power Query。 有关可用函数的列表，请参阅[转换函数](wrangling-functions.md)。 ADF 将 M 脚本转换为数据流脚本，以便可以使用 Azure 数据工厂数据流 Spark 环境大规模执行 Power Query。

:::image type="content" source="media/wrangling-data-flow/tutorial6.png" alt-text="显示创作数据整理 Power Query 过程的屏幕截图。":::

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>运行和监视 Power Query 数据整理活动

若要执行 Power Query 活动的管道调试运行，请在管道画布中单击“调试”。 一旦发布管道，“触发器”就会执行最后发布管道的按需运行。 可以通过所有现有的 Azure 数据工厂触发器来安排 Power Query 管道。

:::image type="content" source="media/data-flow/power-query-activity.png" alt-text="显示如何添加 Power Query 数据整理活动的屏幕截图。":::

转到“监视”选项卡，以可视化被触发的 Power Query 活动运行的输出。

:::image type="content" source="media/wrangling-data-flow/tutorial2.png" alt-text="显示被触发的整理 Power Query 活动运行的输出的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

了解如何[创建映射数据流](tutorial-data-flow.md)。
