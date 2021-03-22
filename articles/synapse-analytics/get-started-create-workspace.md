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
ms.date: 12/31/2020
ms.openlocfilehash: 94d069a283249f2880743ba911c32bf3821d28c8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171477"
---
# <a name="creating-a-synapse-workspace"></a>创建 Synapse 工作区

在本教程中，你将了解如何创建 Synapse 工作区、专用 SQL 池和无服务器 Apache Spark 池。 

## <a name="prerequisites"></a>先决条件

若要完成本教程的步骤，你需要有权访问针对其分配了“所有者”角色的资源组。 在此资源组中创建 Synapse 工作区。

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>在 Azure 门户中创建 Synapse 工作区

1. 打开 [Azure 门户](https://portal.azure.com)，在搜索栏中输入“Synapse”（不要点击 Enter）。
1. 在“服务”下的搜索结果中，选择“Azure Synapse Analytics” 。
1. 选择“添加”以创建工作区。
1. 在“基本信息”选项卡的“项目详细信息”下，填写以下字段：
      1. **订阅** - 选取任何订阅。
      2. **资源组** - 使用任何资源组。
      3. **资源组** - 将此项留空。
1. 在“基本信息”选项卡的“工作区详细信息”下，填写以下字段：
      1. **工作区名称** - 选取任何全局唯一名称。 在本教程中，我们将使用 myworkspace。
      1. **区域** - 选取任何区域。
      1. **选择 Data Lake Storage Gen2**
        1. 单击“来自订阅”对应的按钮。
        1. 对于“帐户名称”，单击“新建”，然后将新存储帐户命名为 contosolake 或类似名称，因为此名称必须是唯一的  。
        1. 对于“文件系统名称”，单击“新建”，然后将其命名为 users  。 这将创建一个名为“users”的存储容器。 工作区将使用此存储帐户作为 Spark 表和 Spark 应用程序日志的“主要”存储帐户。
        1. 选中“在 Data Lake Storage Gen2 帐户上自行分配存储 Blob 数据参与者角色”框。 
1. 选择“查看 + 创建” > “创建”。 你的工作区将在几分钟内准备就绪。

> [!NOTE]
> 若要从现有的专用 SQL 池（之前称为 SQL DW）启用工作区功能，请参阅[如何为专用 SQL 池（之前称为 SQL DW）启用工作区](./sql-data-warehouse/workspace-connected-create.md)。


## <a name="open-synapse-studio"></a>打开 Synapse Studio

创建 Azure Synapse 工作区后，可以通过两种方式打开 Synapse Studio：

* 在 [Azure 门户](https://portal.azure.com)中打开 Synapse 工作区，在 Synapse 工作区的“概述”部分中，在“打开 Synapse Studio”框中选择“打开” 。
* 转到 `https://web.azuresynapse.net`，然后登录到工作区。


## <a name="the-built-in-serverless-sql-pool"></a>内置无服务器 SQL 池

每个工作区都具有名为“内置”的预构建无服务器 SQL 池。 无法删除该池。 通过无服务器 SQL 池，你可以使用 SQL，而无需预留专用 SQL 池的容量。 与专用 SQL 池不同，无服务器 SQL 池的计费基于运行查询所扫描的数据量，而不是分配到池的容量。


## <a name="create-a-dedicated-sql-pool"></a>创建专用 SQL 池

1. 在 Synapse Studio 的左窗格中，选择“管理” > “SQL 池” 。
1. 选择“新建”
1. 对于“SQL 池名称”，请选择“SQLPOOL1” 
1. 对于“性能级别”，请选择“DW100C” 
1. 选择“查看 + 创建” > “创建”。 你的专用 SQL 池将在几分钟内准备就绪。 你的专用 SQL 池与另一个名称也为 SQLPOOL1 的专用 SQL 池数据库相关联。

只要专用 SQL 池处于活动状态，就会使用计费资源。 你可以在稍后暂停池以降低成本。

> [!NOTE] 
> 在工作区中新建专用 SQL 池（之前称为 SQL DW）时，将打开专用 SQL 池预配页面。 预配将在逻辑 SQL 服务器上进行。


## <a name="create-a-serverless-apache-spark-pool"></a>创建无服务器 Apache Spark 池

1. 在 Synapse Studio 的左侧窗格中，选择“管理” > “Apache Spark 池” 。
1. 选择“新建” 
1. 对于“Apache Spark 池名称”，请输入“Spark1” 。
1. 对于“节点大小”，请输入“小” 。
1. 对于“节点数”，请将最小值设置为 3，将最大值设置为 3
1. 选择“查看 + 创建” > “创建”。 你的 Apache Spark 池将在几秒钟内准备就绪。

Spark 池告知 Azure Synapse 要使用的 Spark 资源数量。 只需支付所用资源的费用。 主动停止使用该池时，资源将自动超时并进行回收。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用专用 SQL 池进行分析](get-started-analyze-sql-pool.md)
