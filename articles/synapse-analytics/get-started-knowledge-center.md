---
title: 教程：开始探索 Synapse 知识中心
description: 在本教程中，你将了解如何使用 Synapse 知识中心。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 611d2163e242d7851398821344c3ed595df364cb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460255"
---
# <a name="explore-the-synapse-knowledge-center"></a>探索 Synapse 知识中心

在本教程中，你将了解如何使用 Synapse Studio 知识中心。

## <a name="getting-to-the-knowledge-center"></a>访问知识中心

在 Synapse Studio 中，有两种方法可以找到知识中心：

  1. 在主页中心中，单击页面右上角附近的“了解”。
  2. 在顶部的菜单栏中，单击 **?** 然后单击“知识中心”。

选择任一方法并打开“知识中心”。

## <a name="overview"></a>概述

使用“知识中心”可以执行三项操作：
* **立即使用示例**。 如果你需要一个快速示例来了解 Synapse 如何工作，请选择此选项。
* **浏览库**。 此选项允许你链接示例数据集，并以 SQL 脚本、笔记本和管道的形式添加示例代码。
* **浏览 Synapse Studio**。 此选项将带你简单了解 Synapse Studio 的基本部分。 如果你以前从未使用过 Synapse Studio，则这很有用。

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>使用无服务器 SQL 池浏览 blob 存储

1. 转到“知识中心”，单击“立即使用示例” 
1. 选择“使用 SQL 来查询数据” 
1. 单击“立即使用示例”
1. 它将创建一个新的 SQL 脚本。
1. 滚动到第一个查询（第 28 行到第 32 行），然后选择查询文本
1. 单击“运行”。 它将运行你选择的文本。

## <a name="loading-more-nyc-taxi-data"></a>加载更多 NYC 出租车数据
1. 转到“知识中心”，单击“浏览库”  
1. 选择顶部的“SQL 脚本”选项卡
1. 选择“加载纽约出租车数据集”
1. 在“输入”下选择“选择现有池”，接着选择“SQLDB1”
1. 单击“打开脚本”
1. 此时会显示一个新的 SQL 脚本。
1. 单击“**运行**”
1. 这将为所有 NYC 出租车数据创建多个表，并使用 T-SQL COPY 命令加载它们。

    > [!NOTE] 
    > 在专用 SQL 池（之前称为 SQL DW）中使用 SQL 脚本的示例库时，只能使用现有的专用 SQL 池（之前称为 SQL DW）。

## <a name="next-steps"></a>后续步骤

* [Azure Synapse Analytics 入门](get-started.md)
* [创建工作区](quickstart-create-workspace.md)
* [使用无服务器 SQL 池](quickstart-sql-on-demand.md)
