---
title: 教程：开始与管道集成
description: 在本教程中，你将了解如何使用 Synapse Studio 集成管道和活动。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 2ea7c3c440fcf95e4512464333efe8461788bceb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219396"
---
# <a name="integrate-with-pipelines"></a>与管道集成

在本教程中，你将了解如何使用 Synapse Studio 集成管道和活动。 

## <a name="overview"></a>概述

可以在 Azure Synapse 中集成各种任务。

1. 在 Synapse Studio 中，转到“集成”中心。
1. 选择 + > “管道”以创建新管道 。 单击新管道对象以打开管道设计器。
1. 在“活动”下，展开 Synapse 文件夹，然后将笔记本对象拖到设计器中  。
1. 选择笔记本活动属性的“设置”选项卡。 使用下拉列表选择当前 Synapse 工作区中的任意笔记本。 
1. 在管道中，选择“添加触发器” > “新建/编辑” 。
1. 在“选择触发器”中选择“新建”，并将“重复周期”设置为“每 1 小时”  。
1. 选择“确定”。 
1. 选择“全部发布”。 


## <a name="monitor-pipeline"></a>监视管道

1. 发布管道后，若要使管道立即运行而不用等待一个小时，请选择“添加触发器” > “立即触发” 。
1. 在 Synapse Studio 中，转到“监视器”中心，然后选择“管道运行”以监视管道执行进度 。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 的可视化数据](get-started-visualize-power-bi.md)
