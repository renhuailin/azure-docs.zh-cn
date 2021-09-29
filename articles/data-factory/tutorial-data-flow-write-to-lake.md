---
title: 使用数据流将文件写入数据湖的最佳做法
description: 本教程提供使用数据流将文件写入数据湖的最佳做法
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/04/2021
ms.openlocfilehash: 02e67e6521e1f5fa3c29375a15953557613f7d7d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763683"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>使用数据流将文件写入数据湖的最佳做法

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

在本教程中，你将了解在使用数据流将文件写入 ADLS Gen2 或 Azure Blob 存储时可应用的最佳做法。 需要访问 Azure Blob 存储帐户或 Azure Data Lake Store Gen2 帐户，才能读取 parquet 文件，然后将结果存储在文件夹中。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**。 将 ADLS 存储用作“源”和“接收器”数据存储 。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../storage/common/storage-account-create.md)以获取创建步骤。

本教程中的步骤假定你 

## <a name="create-a-data-factory"></a>创建数据工厂

在此步骤中，请先创建数据工厂，然后打开数据工厂 UX，在该数据工厂中创建一个管道。

1. 打开 **Microsoft Edge** 或 **Google Chrome**。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 在左侧菜单中，选择“创建资源” > “集成” > “数据工厂”  
1. 在“新建数据工厂”页面上，在“名称”下输入“ADFTutorialDataFactory” 。
1. 选择要在其中创建数据工厂的 Azure **订阅**。
1. 对于“资源组”，请执行以下步骤之一： 

    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
    
    b. 选择“新建”，并输入资源组的名称。若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。
    
1. 在“版本”下选择“V2”。 
1. 在“位置”下选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如 Azure 存储和 SQL 数据库）和计算资源（例如 Azure HDInsight）可以位于其他区域。
1. 选择“创建”  。
1. 创建完成后，通知中心内会显示通知。 选择“转到资源”导航到“数据工厂”页。
1. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>创建包含数据流活动的管道

在此步骤中，你将创建一个包含数据流活动的管道。

1. 在 Azure 数据工厂的主页上，选择“协调”。

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="显示 ADF 主页的屏幕截图。":::

1. 在管道的“常规”选项卡中，输入“DeltaLake”作为管道的名称。 
1. 在工厂顶部栏中，将“数据流调试”滑块滑动到打开。 调试模式允许针对实时 Spark 群集进行转换逻辑的交互式测试。 数据流群集需要 5-7 分钟才能预热，如果用户计划进行数据流开发，建议先打开调试。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

    :::image type="content" source="media/tutorial-data-flow/dataflow1.png" alt-text="数据流活动":::
1. 在“活动”窗格中，展开“移动和转换”可折叠部分 。 将“数据流”活动从该窗格拖放到管道画布上。

    :::image type="content" source="media/tutorial-data-flow/activity1.png" alt-text="显示管道画布的屏幕截图，你可以在其中放置数据流活动。":::
1. 在“添加数据流”弹出窗口中，选择“创建新数据流”，然后将数据流命名为“DeltaLake”。   完成操作后，请单击“完成”。

    :::image type="content" source="media/tutorial-data-flow/activity2.png" alt-text="显示在创建新数据流时对数据流进行命名的位置的屏幕截图。":::

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在数据流画布中构建转换逻辑

你将获取任何源数据（在本教程中，我们将使用 Parquet 文件源）并使用接收器转换，以便使用最有效的机制登录 Parquet 格式的数据，从而进行数据湖 ETL。

:::image type="content" source="media/data-flow/parts-final.png" alt-text="最终流":::

### <a name="tutorial-objectives"></a>教程目标

1. 在新数据流中选择任何源数据集
1. 使用数据流有效地对接收器数据集进行分区
1. 在 ADLS Gen2 数据湖文件夹中登录分区数据

### <a name="start-from-a-blank-data-flow-canvas"></a>从一个空白的数据流画布开始

首先，让我们为下述每个机制设置数据流环境，以便在 ADLS Gen2 中登录数据

1. 单击源转换。
1. 单击底部面板中数据集旁的新建按钮。
1. 选择数据集或创建新数据集。 对于本演示，我们将使用名为 User Data 的 Parquet 数据集。
1. 添加派生列转换。 我们会将此用作动态设置所需文件夹名称的方式。
1. 添加接收器转换。
   
### <a name="hierarchical-folder-output"></a>分层文件夹输出

使用数据中的唯一值创建文件夹层次结构以对数据湖中的数据进行分区，这种做法十分常见。 这是在数据湖和 Spark（数据流背后的计算引擎）中组织和处理数据的最佳方式。 但是，以这种方式组织输出会产生较小的性能成本。 在接收器中使用此机制时，预计总体管道性能会略有下降。

1. 返回到数据流设计器并编辑上面的数据流创建。 单击接收器转换。
1. 单击“优化”>“设置分区”>“键”
1. 选取要用于设置分层文件夹结构的列。
1. 请注意，下面的示例使用年份和月份作为用于文件夹命名的列。 结果是得到形式为 ```releaseyear=1990/month=8``` 的文件夹。
1. 访问数据流源中的数据分区时，只需指向 ```releaseyear``` 上面的顶层文件夹，并对每个后续文件夹使用通配符模式，例如：```**/**/*.parquet```
1. 若要操作数据值，或者即使需要为文件夹名称生成合成值，请使用派生列转换创建要在文件夹名称中使用的值。

:::image type="content" source="media/data-flow/key-parts.png" alt-text="键分区":::
   
### <a name="name-folder-as-data-values"></a>将文件夹命名为数据值

对于使用 ADLS Gen2 的数据湖数据，一种性能稍好，但没有提供与键/值分区相同的好处的接收器方法是 ```Name folder as column data```。 虽然分层结构的键分区样式使你能够更轻松地处理数据切片，但这种方法是一个可以更快地写入数据的平展文件夹结构。

1. 返回到数据流设计器并编辑上面的数据流创建。 单击接收器转换。
1. 单击“优化”>“设置分区”>“使用当前分区”。
1. 单击“设置”>“将文件夹命名为列数据”。
1. 选择要用于生成文件夹名称的列。
1. 若要操作数据值，或者即使需要为文件夹名称生成合成值，请使用派生列转换创建要在文件夹名称中使用的值。

:::image type="content" source="media/data-flow/folders.png" alt-text="文件夹选项":::

### <a name="name-file-as-data-values"></a>将文件命名为数据值

上面教程中所列的方法是在数据湖中创建文件夹类别的良好用例。 这些方法采用的默认文件命名方案是使用 Spark 执行程序作业 ID。 有时可能想要在数据流文本接收器中设置输出文件的名称。 此方法仅建议用于小文件。 将分区文件合并到单个输出文件中的过程是一个长时间运行的过程。

1. 返回到数据流设计器并编辑上面的数据流创建。 单击接收器转换。
1. 单击“优化”>“设置分区”>“单个分区”。 正是这一单个分区要求在合并文件时造成了执行过程中的瓶颈。 此选项仅建议用于小文件。
1. 单击“设置”>“将文件命名为列数据”。
1. 选择要用于生成文件名的列。
1. 若要操作数据值，或者即使需要为文件名生成合成值，请使用派生列转换创建要在文件名中使用的值。

## <a name="next-steps"></a>后续步骤

了解有关[数据流接收器](data-flow-sink.md)的详细信息。
