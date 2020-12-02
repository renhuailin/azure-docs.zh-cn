---
title: 快速入门：入门 - 创建 Synapse 工作区
description: 在本教程中，你将了解如何创建 Synapse 工作区、专用 SQL 池和无服务器 Apache Spark 池。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/21/2020
ms.openlocfilehash: 42b586062378c59b66ca945880bf723418264071
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241924"
---
# <a name="creating-a-synapse-workspace"></a>创建 Synapse 工作区

在本教程中，你将了解如何创建 Synapse 工作区、专用 SQL 池和无服务器 Apache Spark 池。 

## <a name="prerequisites"></a>先决条件

若要完成本教程的步骤，你需要有权访问针对其分配了“所有者”角色的资源组。 在此资源组中创建 Synapse 工作区。

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>在 Azure 门户中创建 Synapse 工作区

1. 打开 [Azure 门户](https://portal.azure.com)，然后在顶部搜索“Synapse”。
1. 在“服务”下的搜索结果中，选择“Azure Synapse Analytics (工作区预览版)” 。
1. 选择“添加”以创建工作区。
1. 在“基本信息”中，输入首选的“订阅”、“资源组”和“区域”，然后选择工作区名称   。 在本教程中，我们将使用 myworkspace。
1. 导航到“选择 Data Lake Storage Gen 2”。 
1. 单击“新建”进行创建，并将其命名为 contosolake 。
1. 单击“文件系统”并将其命名为 users 。 这将创建一个名为 users 的容器
1. 工作区将使用此存储帐户作为 Spark 表和 Spark 应用程序日志的“主要”存储帐户。
1. 选择“查看 + 创建” > “创建”。 你的工作区将在几分钟内准备就绪。

## <a name="open-synapse-studio"></a>打开 Synapse Studio

创建 Azure Synapse 工作区后，可以通过两种方式打开 Synapse Studio：

* 在 [Azure 门户](https://portal.azure.com)中打开 Synapse 工作区。 在“概述”部分的顶部，选择“启动 Synapse Studio” 。
* 转到 `https://web.azuresynapse.net`，然后登录到工作区。

## <a name="create-a-dedicated-sql-pool"></a>创建专用 SQL 池

1. 在 Synapse Studio 的左窗格中，选择“管理” > “SQL 池” 。
1. 选择“新建”
1. 对于“SQL 池名称”，请选择“SQLPOOL1” 
1. 对于“性能级别”，请选择“DW100C” 
1. 选择“查看 + 创建” > “创建”。 你的专用 SQL 池将在几分钟内准备就绪。 你的专用 SQL 池与另一个名称也为 SQLPOOL1 的专用 SQL 池数据库相关联。

只要专用 SQL 池处于活动状态，就会使用计费资源。 你可以在稍后暂停池以降低成本。

## <a name="create-a-serverless-apache-spark-pool"></a>创建无服务器 Apache Spark 池

1. 在 Synapse Studio 的左侧窗格中，选择“管理” > “Apache Spark 池” 。
1. 选择“新建” 
1. 对于“Apache Spark 池名称”，请输入“Spark1” 。
1. 对于“节点大小”，请输入“小” 。
1. 对于“节点数”，请将最小值设置为 3，将最大值设置为 3
1. 选择“查看 + 创建” > “创建”。 你的 Apache Spark 池将在几秒钟内准备就绪。

Spark 池告知 Azure Synapse 要使用的 Spark 资源数量。 只需支付所用资源的费用。 主动停止使用该池时，资源将自动超时并进行回收。

## <a name="the-built-in-serverless-sql-pool"></a>内置无服务器 SQL 池

每个工作区都具有名为“内置”的预构建无服务器 SQL 池。 无法删除该池。 通过无服务器 SQL 池，你可以使用 SQL，而无需预留专用 SQL 池的容量。 与专用 SQL 池不同，无服务器 SQL 池的计费基于运行查询所扫描的数据量，而不是分配到池的容量。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用专用 SQL 池进行分析](get-started-analyze-sql-pool.md)
