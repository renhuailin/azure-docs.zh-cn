---
title: 快速入门：将 Azure 数据资源管理器连接到 Azure Synapse Analytics 工作区
description: 使用适用于 Azure Synapse Analytics 的 Apache Spark 将 Azure 数据资源管理器群集连接到 Azure Synapse Analytics 工作区。
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 9f45bc134c656701ff02beca464ac00fdf222f88
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830196"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>使用适用于 Azure Synapse Analytics 的 Apache Spark 连接到 Azure 数据资源管理器

本文介绍如何使用适用于 Azure Synapse Analytics 的 Apache Spark 从 Synapse Studio 访问 Azure 数据资源管理器数据库。

## <a name="prerequisites"></a>先决条件

* [创建 Azure 数据资源管理器群集和数据库](/azure/data-explorer/create-cluster-database-portal)。
* 具有现有的 Azure Synapse Analytics 工作区，或按照[快速入门：创建 Azure Synapse 工作区](./quickstart-create-workspace.md)中的步骤创建新的工作区。
* 具有现有的 Apache Spark 池，或按照[快速入门：使用 Azure 门户创建 Apache Spark 池](./quickstart-create-apache-spark-pool-portal.md)。
* [预配 Azure AD 应用程序以创建 Azure Active Directory (Azure AD) 应用](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)中的步骤创建一个新池。
* 按照[管理 Azure 数据资源管理器数据库权限](/azure/data-explorer/manage-database-permissions)中的步骤授予 Azure AD 应用访问数据库的权限。

## <a name="go-to-synapse-studio"></a>转到 Synapse Studio

在 Azure Synapse 工作区中，选择“启动 Synapse Studio”。 在 Synapse Studio 主页上，选择“数据”，转到“数据对象资源管理器” 。

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>将 Azure 数据资源管理器数据库连接到 Azure Synapse 工作区

将 Azure 数据资源管理器数据库连接到工作区是通过链接服务完成的。 使用 Azure 数据资源管理器链接服务，可以浏览 Azure Synapse 的 Apache Spark 中的数据、读取和写入。 还可以在管道中运行集成作业。

从数据对象资源管理器中，按照以下步骤直接连接 Azure 数据资源管理器群集：

1. 选择“数据”旁边的“+”图标 。
1. 选择“连接”以连接到外部数据。
1. 选择“Azure 数据资源管理器 (Kusto)”。
1. 选择“继续”。
1. 使用易记名称命名链接服务。 该名称将显示在数据对象资源管理器中，并由 Azure Synapse 运行时用于连接到数据库。
1. 从订阅中选择 Azure 数据资源管理器群集或输入 URI。
1. 输入“服务主体 ID”和“服务主体密钥” 。 确保此服务主体对数据库具有查看访问权限并可访问引入器，以便读取和引入数据。
1. 输入 Azure 数据资源管理器数据库名称。
1. 选择“测试连接”以确保你具有正确的权限。
1. 选择“创建”。

    ![显示新的链接服务的屏幕截图。](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > （可选）“测试连接”不会验证写入访问权限。 请确保服务主体 ID 对 Azure 数据资源管理器数据库具有写入访问权限。

1. Azure 数据资源管理器群集和数据库显示在“Azure 数据资源管理器”部分下的“链接”选项卡上 。

    ![显示群集浏览的屏幕截图。](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

1. 必须先将链接服务发布到工作区，然后才能在笔记本中与链接服务交互。 单击工具栏中的“发布”，查看挂起的更改，然后单击“确定” 。

    > [!NOTE]
    > 在当前版本中，将根据你在 Azure 数据资源管理器数据库上的 Azure AD 帐户权限填充数据库对象。 运行 Apache Spark 笔记本或集成作业时，将使用链接服务中的凭据（例如服务主体）。

## <a name="quickly-interact-with-code-generated-actions"></a>与代码生成的操作快速交互

右键单击某个数据库或表时，会显示一个示例 Spark 笔记本列表。 选择一个选项来读取、写入数据或将数据流式传输到 Azure 数据资源管理器。

[![显示新的示例笔记本的屏幕截图。](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

下面是读取数据的示例。 将笔记本附加到 Spark 池，并运行单元格。

[![显示新的读取笔记本的屏幕截图。](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > 首次执行时，可能需要三分多钟才能启动 Spark 会话。 后续执行的速度要快得多。

## <a name="limitations"></a>限制

Azure Synapse 托管的虚拟网络目前不支持 Azure 数据资源管理器连接器。

## <a name="next-steps"></a>后续步骤

* [具有高级选项的示例代码](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Azure 数据资源管理器 (Kusto) Spark 连接器](https://github.com/Azure/azure-kusto-spark)
