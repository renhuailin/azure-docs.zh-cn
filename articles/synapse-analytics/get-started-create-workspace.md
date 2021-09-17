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
ms.date: 03/17/2021
ms.openlocfilehash: c99d981c6abf6d7a43a5180360712b665c57f6fe
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324349"
---
# <a name="creating-a-synapse-workspace"></a>创建 Synapse 工作区

在本教程中，你将了解如何创建 Synapse 工作区、专用 SQL 池和无服务器 Apache Spark 池。 

## <a name="prerequisites"></a>先决条件

若要完成本教程的步骤，你需要有权访问针对其分配了“所有者”角色的资源组。 在此资源组中创建 Synapse 工作区。

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>在 Azure 门户中创建 Synapse 工作区

### <a name="start-the-process"></a>启动此过程
1. 打开 [Azure 门户](https://portal.azure.com)，在搜索栏中输入“Synapse”（不要点击 Enter）。
1. 在“服务”下的搜索结果中，选择“Azure Synapse Analytics” 。
1. 选择“添加”以创建工作区。

## <a name="basics-tab--project-details"></a>“基本信息”选项卡 > “项目详细信息”
填写以下字段：

1. **订阅** - 选取任何订阅。
1. **资源组** - 使用任何资源组。
1. 受管理资源组 - 请将此项留空。

## <a name="basics-tab--workspace-details"></a>“基本信息”选项卡 > “工作区详细信息”
填写以下字段：

1. **工作区名称** - 选取任何全局唯一名称。 在本教程中，我们将使用 myworkspace。
1. **区域** - 选择放置客户端应用程序/服务（例如 Azure VM、Power BI、Azure Analysis Service）和包含数据的存储（例如 Azure Data Lake 存储、Azure Cosmos DB 分析存储）的区域。

> [!NOTE]
> 工作区与客户端应用程序或存储没有位于同一位置，可能是许多性能问题的根本原因。 如果将数据或客户端放置在多个区域中，则可以在与数据和客户端位于同一位置的其他区域中创建单独的工作区。

在“选择 Data Lake Storage 第二代”下方：

1. 对于“帐户名称”，选择“新建”，然后将新存储帐户命名为 contosolake 或类似名称，因为它必须是唯一的  。
1. 对于“文件系统名称”，选择“新建”，然后将其命名为 users  。 这将创建一个名为“users”的存储容器。 工作区将使用此存储帐户作为 Spark 表和 Spark 应用程序日志的“主要”存储帐户。
1. 选中“在 Data Lake Storage Gen2 帐户上自行分配存储 Blob 数据参与者角色”框。 

## <a name="completing-the-process"></a>完成此过程
选择“查看 + 创建” > “创建”。 你的工作区将在几分钟内准备就绪。

> [!NOTE]
> 若要从现有的专用 SQL 池（之前称为 SQL DW）启用工作区功能，请参阅[如何为专用 SQL 池（之前称为 SQL DW）启用工作区](./sql-data-warehouse/workspace-connected-create.md)。


## <a name="open-synapse-studio"></a>打开 Synapse Studio

创建 Azure Synapse 工作区后，可以通过两种方式打开 Synapse Studio：

1. 在 [Azure 门户](https://portal.azure.com)中打开 Synapse 工作区，在 Synapse 工作区的“概述”部分中，在“打开 Synapse Studio”框中选择“打开” 。
1. 转到 `https://web.azuresynapse.net`，然后登录到工作区。

    ![登录到工作区](./security/media/common/login-workspace.png)

> [!NOTE]
> 若要登录到工作区，可以使用两种帐户选择方法。 一种是从 Azure 订阅进行选择，另一种是通过手动输入选择帐户 。 如果你有 Synapse Azure 角色或更高级别的 Azure 角色，可以使用这两种方法登录到工作区。 如果没有相关的 Azure 角色，而被授予了 Synapse RBAC 角色，则只有手动输入才能登录到工作区。 若要详细了解 Synapse RBAC，请参阅[什么是 Synapse 基于角色的访问控制 (RBAC)](./security/synapse-workspace-synapse-rbac.md)。


## <a name="place-sample-data-into-the-primary-storage-account"></a>将示例数据放入主存储帐户
在本入门指南中，我们将使用一个纽约市出租车的 10 万行小型示例数据集。 首先，将其放在为工作区创建的主存储帐户中。

* 将此文件下载到计算机： https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* 在 Synapse Studio 中，导航到“数据中心”。 
* 选择“已链接”。
* 在 Azure Data Lake Storage Gen2 类别下，可看到一个名称类似于 myworkspace ( Primary - contosolake ) 的项 。
* 选择名为 users (Primary) 的容器。
* 选择“上传”并选择已下载的 `NYCTripSmall.parquet` 文件。

上传 parquet 文件后，可通过两个等效的 URI 获取它：
* `https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet` 
* `abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet`

在本教程后面的示例中，请确保将 UI 中的 contosolake 替换为你为工作区选择的主存储帐户的名称。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用无服务器 SQL 池进行分析](get-started-analyze-sql-on-demand.md)
