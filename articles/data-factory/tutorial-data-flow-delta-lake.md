---
title: 带有数据流的增量 lake ETL
description: 本教程提供有关使用数据流转换和分析 delta lake 数据的分步说明
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 02/09/2021
ms.openlocfilehash: cb8d44353e826df14ed3baab2c4ca66ffed4a569
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417099"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>使用映射数据流转换 delta lake 中的数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

在本教程中，您将使用数据流画布来创建数据流，以便分析和转换 Azure Data Lake Storage (ADLS) Gen2 中的数据，并将其存储在 Delta Lake 中。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**。 将 ADLS 存储用作“源”和“接收器”数据存储 。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../storage/common/storage-account-create.md)以获取创建步骤。

在本教程中，我们要转换的文件是 MoviesDB.csv，可在[此处](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv)找到。 若要从 GitHub 检索该文件，请将内容复制到所选的文本编辑器，在本地保存为 .csv 文件。 若要将文件上传到存储帐户，请参阅[使用 Azure 门户上传 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。 这些示例将引用名为“sample-data”的容器。

## <a name="create-a-data-factory"></a>创建数据工厂

在此步骤中，请先创建数据工厂，然后打开数据工厂 UX，在该数据工厂中创建一个管道。

1. 打开 **Microsoft Edge** 或 **Google Chrome**。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 在左侧菜单中，选择 "**创建资源**  >  **集成**  >  **数据工厂**"
1. 在 " **新建数据工厂** " 页上的 " **名称**" 下，输入 **ADFTutorialDataFactory**
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

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>使用数据流活动创建管道

在此步骤中，你将创建一个包含数据流活动的管道。

1. 在“开始使用”页中，选择“创建管道”。 

   ![创建管道](./media/doc-common-process/get-started-page.png)

1. 在管道的 " **常规** " 选项卡中，输入 **DeltaLake** 作为管道 **名称** 。
1. 在工厂顶部栏中，将“数据流调试”滑块滑动到打开。 调试模式允许针对实时 Spark 群集进行转换逻辑的交互式测试。 数据流群集需要 5-7 分钟才能预热，如果用户计划进行数据流开发，建议先打开调试。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

    ![数据流活动](media/tutorial-data-flow/dataflow1.png)
1. 在“活动”窗格中，展开“移动和转换”可折叠部分 。 将“数据流”活动从该窗格拖放到管道画布上。

    ![显示管道画布的屏幕截图，你可以在其中放置数据流活动。](media/tutorial-data-flow/activity1.png)
1. 在 " **添加** 数据流" 弹出窗口中，选择 " **创建新** 数据流"，然后将数据流命名为 **DeltaLake**。 完成操作后，请单击“完成”。

    ![显示在创建新数据流时对数据流进行命名的位置的屏幕截图。](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在数据流画布中构建转换逻辑

在本教程中，您将生成两个数据流。 前数据流是一个简单的源到接收器，用来自上面的电影 CSV 文件生成新的增量 Lake。 最后，你将在下面创建此流设计以更新 Delta Lake 中的数据。

![最终流](media/data-flow/data-flow-tutorial-6.png "最终流")

### <a name="tutorial-objectives"></a>教程目标

1. 从上面获取 MoviesCSV 数据集源，并从其组成新的增量 Lake Lake
1. 构建逻辑，将1988电影的分级更新为 "1"
1. 从1950删除所有电影
1. 通过从1960复制电影来插入新电影2021

### <a name="start-from-a-blank-data-flow-canvas"></a>从空白数据流画布开始

1. 单击源转换
1. 单击底部面板中 "数据集" 旁边的 "新建"，为 ADLS Gen2 创建新的链接服务
1. 选择数据集类型的分隔文本
1. 将数据集命名为 "MoviesCSV" 
1. 指向上传到此存储的 MoviesCSV 文件
1. 将其设置为逗号分隔并在第一行上包含标题 
1. 中转到 "源投影" 选项卡，然后单击 "检测数据类型"
1. 获得投影集后，可以继续 
1. 添加接收器转换
1. Delta 是内联数据集类型。 需要指向 ADLS Gen2 的存储帐户。
   
   ![内联数据集](media/data-flow/data-flow-tutorial-5.png "内联数据集")

1. 在存储容器中选择要在其中创建增量 Lake 的文件夹名称
1. 返回到管道设计器，然后单击 "调试"，在调试模式下执行管道，其中只包含画布上的此数据流活动。 这会在 ADLS Gen2 中生成新的增量 Lake。
1. 在工厂资源中，单击 "新建" > 数据流 
1. 再次使用 MoviesCSV 作为源，并再次单击 "检测数据类型"
1. 在关系图中向源转换添加筛选器转换
1. 仅允许与要使用的三年时间匹配的电影行，其中将为1950、1988和1960
1. 通过向筛选器转换添加派生列转换，将每个1988电影的评级更新为 "1"
1. 在同一派生列中，通过获取现有年份将电影创建为2021，并将年份更改为2021。 让我们选择1960。
1. 这是三个派生列的外观

   ![派生列](media/data-flow/data-flow-tutorial-2.png "派生列")
   
1. ```Update, insert, delete, and upsert``` 在 alter Row 转换中创建策略。 在派生列后添加 alter row 转换。
1. 更改行策略应如下所示。

   ![更改行](media/data-flow/data-flow-tutorial-3.png "更改行")
   
1. 现在，你已为每个更改行类型设置了正确的策略，请检查是否已对接收器转换设置了正确的更新规则

   ![接收器](media/data-flow/data-flow-tutorial-4.png "接收器")
   
1. 这里，我们使用的是增量 Lake 接收器到 ADLS Gen2 data Lake，并允许插入、更新、删除。 
1. 请注意，键列是由 "电影主键列" 和 "年份" 列组成的组合键。 这是因为我们通过复制1960行创建了假2021电影。 这可以避免在通过提供唯一性来查找现有行时出现冲突。

### <a name="download-completed-sample"></a>下载完成的示例
[下面是一个用于增量管道的示例解决方案，其中包含用于在 lake 中更新/删除行的数据流：](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>后续步骤

详细了解[数据流表达式语言](data-flow-expression-functions.md)。
