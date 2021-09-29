---
title: 数据流中的 Delta lake ETL
description: 本教程提供了有关使用数据流来转换和分析 delta lake 中的数据的分步说明
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/04/2021
ms.openlocfilehash: 246b1988a7a632e9c3b332f7adbdf908f1084457
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798249"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>使用映射数据流转换 delta lake 中的数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

在本教程中，你将使用数据流画布来创建数据流，以便分析和转换 Azure Data Lake Storage (ADLS) Gen2 中的数据，并将其存储在 Delta Lake 中。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**。 将 ADLS 存储用作“源”和“接收器”数据存储 。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../storage/common/storage-account-create.md)以获取创建步骤。

在本教程中，我们要转换的文件是 MoviesDB.csv，可在[此处](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv)找到。 若要从 GitHub 检索该文件，请将内容复制到所选的文本编辑器，在本地保存为 .csv 文件。 若要将文件上传到存储帐户，请参阅[使用 Azure 门户上传 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。 这些示例将引用名为“sample-data”的容器。

## <a name="create-a-data-factory"></a>创建数据工厂

在此步骤中，请先创建数据工厂，然后打开数据工厂 UX，在该数据工厂中创建一个管道。

1. 打开 **Microsoft Edge** 或 **Google Chrome**。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 在左侧菜单中，选择“创建资源” > “集成” > “数据工厂”  
1. 在“新建数据工厂”页面上，在“名称”下输入“ADFTutorialDataFactory” 。
1. 选择要在其中创建数据工厂的 Azure **订阅**。
1. 对于“资源组”，请执行以下步骤之一： 

    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 

    b. 选择“新建”，并输入资源组的名称。 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。 
1. 在“版本”下选择“V2”。 
1. 在“位置”下选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如 Azure 存储和 SQL 数据库）和计算资源（例如 Azure HDInsight）可以位于其他区域。
1. 选择“创建”  。
1. 创建完成后，通知中心内会显示通知。 选择“转到资源”导航到“数据工厂”页。
1. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>创建包含数据流活动的管道

在此步骤中，你将创建一个包含数据流活动的管道。

1. 在主页上，选择“协调”。

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="显示 ADF 主页的屏幕截图。":::

1. 在管道的“常规”选项卡中，输入“DeltaLake”作为管道的名称。 
1. 在“活动”窗格中，展开“移动和转换”可折叠部分 。 将“数据流”活动从该窗格拖放到管道画布上。

    :::image type="content" source="media/tutorial-data-flow/activity1.png" alt-text="显示管道画布的屏幕截图，你可以在其中放置数据流活动。":::
1. 在“添加数据流”弹出窗口中，选择“创建新数据流”，然后将数据流命名为“DeltaLake”。   完成操作后，请单击“完成”。

    :::image type="content" source="media/tutorial-data-flow/activity2.png" alt-text="显示在创建新数据流时对数据流进行命名的位置的屏幕截图。":::
1. 在管道画布的顶部栏中，将“数据流调试”滑块滑动到打开。 调试模式允许针对实时 Spark 群集进行转换逻辑的交互式测试。 数据流群集需要 5-7 分钟才能预热，如果用户计划进行数据流开发，建议先打开调试。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

    :::image type="content" source="media/tutorial-data-flow/dataflow1.png" alt-text="屏幕截图显示了“数据流调试”滑块的位置。":::

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在数据流画布中构建转换逻辑

在本教程中，你将生成两个数据流。 第一个数据流是一个简单的源到接收器数据流，用来基于上面的电影 CSV 文件生成新的 Delta Lake。 最后，你将创建下面的流设计来更新 Delta Lake 中的数据。

:::image type="content" source="media/data-flow/data-flow-tutorial-6.png" alt-text="最终流":::

### <a name="tutorial-objectives"></a>教程目标

1. 获取上面的 MoviesCSV 数据集源，并基于它构成一个新的 Delta Lake
1. 构建相应的逻辑，用以将 1988 年电影的评级更新为 1
1. 删除 1950 年的所有电影
1. 通过复制 1960 年的电影为 2021 年插入新电影

### <a name="start-from-a-blank-data-flow-canvas"></a>从一个空白的数据流画布开始

1. 单击源转换
1. 单击底部的面板 1 中数据集旁边的“新建”，为 ADLS Gen2 创建一个新的链接服务
1. 对于数据集类型，选择带分隔符的文本
1. 将数据集命名为“MoviesCSV” 
1. 指向你在上面已上传到存储的 MoviesCSV 文件
1. 将其设置为逗号分隔的并包含第一行上的标题 
1. 转到“源投影”选项卡，然后单击“检测数据类型”
1. 设置投影后，你可以继续 
1. 添加接收器转换
1. Delta 是一个内联数据集类型。 需要指向你的 ADLS Gen2 存储帐户。
   
   :::image type="content" source="media/data-flow/data-flow-tutorial-5.png" alt-text="内联数据集":::

1. 在你的存储容器中选择你希望 ADF 在其中创建 Delta Lake 的文件夹名称
1. 返回到管道设计器，然后单击“调试”以在画布上仅有此数据流活动的情况在调试模式下执行管道。 这会在 ADLS Gen2 中生成新的 Delta Lake。
1. 在“工厂资源”中，单击“新建”>“数据流” 
1. 再次使用 MoviesCSV 作为源，并再次单击“检测数据类型”
1. 在图形中向源转换添加一个筛选器转换
1. 仅允许与你要处理的三个年份匹配的电影行，这三个年份是 1950、1988 和 1960
1. 现在，通过将派生的列转换添加到筛选器转换中，将 1988 年的每部电影的评级更新为 1
1. 在同一派生列中，通过获取现有年份为 2021 年创建电影，并将年份更改为 2021。 让我们选择 1960。
1. 这是三个派生列的外观

   :::image type="content" source="media/data-flow/data-flow-tutorial-2.png" alt-text="派生列":::
   
1. ```Update, insert, delete, and upsert``` 策略是在“更改行”转换中创建的。 在你的派生列后添加一个“更改行”转换。
1. 你的“更改行”策略应如下所示。

   :::image type="content" source="media/data-flow/data-flow-tutorial-3.png" alt-text="更改行":::
   
1. 现在，你已为每个“更改行”类型设置了正确的策略，请检查是否已在接收器转换上设置了正确的更新规则

   :::image type="content" source="media/data-flow/data-flow-tutorial-4.png" alt-text="接收器":::
   
1. 此处，我们使用你的 ADLS Gen2 Data Lake 的 Delta Lake 接收器，并允许插入、更新和删除。 
1. 请注意，键列是由“电影”主键列和“年份”列构成的一个组合键。 这是因为我们通过复制 1960 年的行创建了虚构的 2021 年电影。 此组合键提供了唯一性，从而避免了在查找现有行时出现冲突。

### <a name="download-completed-sample"></a>下载完整的示例
[下面是一个用于 Delta 管道的示例解决方案，其中包含用于更新/删除 Lake 中的行的数据流：](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>后续步骤

详细了解[数据流表达式语言](data-flow-expression-functions.md)。
