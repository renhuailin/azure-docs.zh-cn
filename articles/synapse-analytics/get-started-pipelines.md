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
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744923"
---
# <a name="integrate-with-pipelines"></a>与管道集成

在本教程中，你将了解如何使用 Synapse Studio 集成管道和活动。 

## <a name="overview"></a>概述

可以在 Azure Synapse 中集成各种任务。

1. 在 Synapse Studio 中，转到“集成”中心。
1. 选择 + > “管道”以创建新管道 。
1. 转到“开发”中心并选择之前创建的其中一个笔记本。
1. 将该笔记本拖动到管道（注意：在笔记本中按照[文档](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace)中所述添加导入模块步骤，这是从管道运行时必需的操作）
1. 在管道中，选择“添加触发器” > “新建/编辑” 。
1. 在“选择触发器”中选择“新建”，并将“重复周期”设置为“每 1 小时”  。
1. 选择“确定”。 
1. 选择“全部发布”。
1. 若要管道立即运行而不用等待一个小时，请选择“添加触发器” > “立即触发” 。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Power BI 的可视化数据](get-started-visualize-power-bi.md)
                                 
