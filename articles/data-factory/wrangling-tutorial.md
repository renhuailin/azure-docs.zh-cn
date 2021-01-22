---
title: Azure 数据工厂中的整理数据流入门
description: 有关如何使用整理数据流在 Azure 数据工厂中准备数据的教程
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 3d43a532f57feab361c6a3de79269991f46fc55d
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684016"
---
# <a name="prepare-data-with-data-wrangling"></a>用 data 整理准备数据

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

数据工厂中的数据整理使你能够以本机方式在 ADF 中构建交互式 Power Query 混合，然后在 ADF 管道内按比例执行这些交互。

> [!NOTE]
> ADF 中 Power Query 活动当前在公共预览版中可用

## <a name="create-a-power-query-activity"></a>创建 Power Query 活动

可通过两种方法在 Azure 数据工厂中创建 Power Query。 一种方法是单击加号图标，然后在 "工厂资源" **窗格中选择 "数据流"** 。

> [!NOTE]
> 以前，data 整理功能位于数据流工作流中。 现在，你将从整理混合 ```New > Power query```

![显示工厂资源窗格 Power Query 的屏幕截图。](media/data-flow/power-query-wrangling.png)

另一种方法是在管道画布的 "活动" 窗格中。 打开 **Power Query** 的可折叠面板，并将 **Power Query** 活动拖到画布上。

![突出显示 data 整理选项的屏幕截图。](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>创作 Power Query data 整理活动

为 Power Query 混合添加 **源数据集** 。 您可以选择现有数据集，也可以创建一个新数据集。 还可以选择接收器数据集。 你可以选择一个或多个源数据集，但此时只允许一个接收器。 选择接收器数据集是可选的，但至少需要一个源数据集。

![整理](media/wrangling-data-flow/tutorial4.png)

单击 " **创建** " 以打开 "Power Query Online 混合编辑器"。

![屏幕截图，显示用于打开 Power Query Online 混合编辑器的 "创建" 按钮。](media/wrangling-data-flow/tutorial5.png)

使用无代码的数据准备创作整理 Power Query。 有关可用函数的列表，请参阅 [转换函数](wrangling-functions.md)。 ADF 将 M 脚本转换为数据流脚本，以便可以使用 Azure 数据工厂数据流 Spark 环境按比例执行 Power Query。

![屏幕截图，显示用于创作数据整理 Power Query 的过程。](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>运行和监视 Power Query data 整理活动

若要执行 Power Query 活动的管道调试运行，请在管道画布中单击 " **调试** "。 一旦您发布管道， **触发器** 就会执行最后发布的管道的按需运行。 可以通过所有现有的 Azure 数据工厂触发器来计划 Power Query 管道。

![显示如何添加 Power Query data 整理 "活动" 的屏幕截图。](media/wrangling-data-flow/tutorial3.png)

中转到 " **监视** " 选项卡，直观显示触发的 Power Query 活动运行的输出。

![屏幕截图，显示触发的整理 Power Query 活动运行的输出。](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>后续步骤

了解如何 [创建映射数据流](tutorial-data-flow.md)。
