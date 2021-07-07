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
ms.date: 04/04/2021
ms.openlocfilehash: ba6023ccc1582650e74da483aeefc48d2a305bef
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111554745"
---
# <a name="explore-the-synapse-knowledge-center"></a>探索 Synapse 知识中心

在本教程中，你将了解如何使用 Synapse Studio 知识中心。

## <a name="finding-the-knowledge-center"></a>找到知识中心

在 Synapse Studio 中，有两种方法可以找到知识中心：

  1. 在主页中心中，单击页面右上角附近的“了解”。
  2. 在顶部的菜单栏中，单击 **?** 然后单击“知识中心”。

选择任一方法并打开“知识中心”。

## <a name="exploring-the-knowledge-center"></a>探索知识中心

显示后，能看到知识中心允许执行以下三项操作：
* **立即使用示例**。 如果你需要一个快速示例来了解 Synapse 如何工作，请选择此选项。
* **浏览库**。 此选项允许你链接示例数据集，并以 SQL 脚本、笔记本和管道的形式添加示例代码。
* **浏览 Synapse Studio**。 此选项将带你简单了解 Synapse Studio 的基本部分。 如果你以前从未使用过 Synapse Studio，则这很有用。

## <a name="use-samples-immediately-three-samples-to-help-you-get-started-fast"></a>立即使用示例：三个示例可帮助你快速入门

本部分包含三个项：
* 使用 Spark 浏览示例数据
* 使用 SQL 查询数据
* 使用 SQL 创建外部表

1. 在“知识中心”中，单击“立即使用示例” 。
1. 选择“使用 SQL 来查询数据”。
1. 单击“使用示例”。
1. 随即将打开一个新的示例 SQL 脚本。
1. 滚动到第一个查询（第 28 行到第 32 行），然后选择查询文本。
1. 单击“运行”。 它只会运行所选代码。

## <a name="gallery-a-collection-of-sample-datasets-and-sample-code"></a>库：示例数据集和示例代码集合

1. 转到“知识中心”，单击“浏览库”。
1. 选择顶部的“SQL 脚本”选项卡。
1. 选择“加载纽约出租车数据集”数据引入示例，单击“继续” 。
1. 在“SQL 池”下，依次选择“选择现有池”和“SQLPOOL1”，然后选择之前创建的 SQLPOOL1 数据库   。
1. 单击“打开脚本”。
1. 随即将打开一个新的示例 SQL 脚本。
1. 单击“**运行**”
1. 这将为所有 NYC 出租车数据创建多个表，并使用 T-SQL COPY 命令加载它们。 如果已在前面的快速入门步骤中创建了这些表，请选择并仅执行不存在的表的 CREATE 和 COPY 代码。

    > [!NOTE] 
    > 在专用 SQL 池（之前称为 SQL DW）中使用 SQL 脚本的示例库时，只能使用现有的专用 SQL 池（之前称为 SQL DW）。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加管理员](get-started-add-admin.md)

