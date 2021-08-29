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
ms.openlocfilehash: 71ca2dd856b18424d79a20db8a4c5552b2e98c53
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822929"
---
# <a name="integrate-with-pipelines"></a>与管道集成

在本教程中，你将了解如何使用 Synapse Studio 集成管道和活动。 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>创建管道并添加笔记本活动

1. 在 Synapse Studio 中，转到“集成”中心。
1. 选择 + > “管道”以创建新管道 。 单击新管道对象以打开管道设计器。
1. 在“活动”下，展开 Synapse 文件夹，然后将笔记本对象拖到设计器中  。
1. 选择笔记本活动属性的“设置”选项卡。 使用下拉列表选择当前 Synapse 工作区中的笔记本。

## <a name="schedule-the-pipeline-to-run-every-hour"></a>计划每小时运行一次管道

1. 在管道中，选择“添加触发器” > “新建/编辑” 。
1. 在“选择触发器”中选择“新建”，并将“重复周期”设置为“每 1 小时”  。
1. 选择“确定”。 
1. 选择“全部发布”。 

## <a name="forcing-a-pipeline-to-run-immediately"></a>强制立即运行管道

管道发布后，你可能希望立即运行它，而不必等待一小时。

1. 打开管道。
1. 单击“添加触发器” > “立即触发”。
1. 选择“确定”。 

## <a name="monitor-pipeline-execution"></a>监视管道执行

1. 转到“监视”选项卡。
1. 选择“管道运行”以监视管道执行进度。
1. 在此视图中，可以在表格“列表”和显示图形“甘特”图表之间进行切换 。 
1. 单击管道名称可查看该管道中活动的状态。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 的可视化数据](get-started-visualize-power-bi.md)
