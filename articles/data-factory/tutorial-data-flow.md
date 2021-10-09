---
title: 使用映射数据流转换数据
description: 本教程提供有关通过 Azure 数据工厂使用映射数据流转换数据的分步说明
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/04/2021
ms.openlocfilehash: 5ff1ea92056b4fe5f090442df72e7c39d53368b9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626220"
---
# <a name="transform-data-using-mapping-data-flows"></a>使用映射数据流转换数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

在本教程中，你将使用 Azure 数据工厂用户界面 (UX) 创建一个管道，该管道使用映射数据流将数据从 Azure Data Lake Storage (ADLS) Gen2 源复制到 ADLS Gen2 接收器并对其进行转换。 使用映射数据流转换数据时，可以扩展本教程中的配置模式

 >[!NOTE]
   >本教程主要介绍数据流映射。 数据流在 Azure 数据工厂和 Synapse 管道中均可用。 如果不熟悉 Azure Synapse 管道中的数据流，请按照[使用 Azure Synapse 管道的数据流](../synapse-analytics/concepts-data-flow-overview.md)进行操作 
   
在本教程中，将执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建包含数据流活动的管道。
> * 构建具有四个转换的映射数据流。
> * 测试性运行管道。
> * 监视数据流活动

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**。 将 ADLS 存储用作“源”和“接收器”数据存储 。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../storage/common/storage-account-create.md)以获取创建步骤。

在本教程中，我们要转换的文件是 MoviesDB.csv，可在[此处](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)找到。 若要从 GitHub 检索该文件，请将内容复制到所选的文本编辑器，在本地保存为 .csv 文件。 若要将文件上传到存储帐户，请参阅[使用 Azure 门户上传 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。 这些示例将引用名为“sample-data”的容器。

## <a name="create-a-data-factory"></a>创建数据工厂

在此步骤中，请先创建数据工厂，然后打开数据工厂 UX，在该数据工厂中创建一个管道。

1. 打开 **Microsoft Edge** 或 **Google Chrome**。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
2. 在左侧菜单中，选择“创建资源” > “集成” > “数据工厂”  ：

   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="&quot;新建&quot;窗格中的“数据工厂”选项":::

3. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory** 。

   Azure 数据工厂的名称必须 *全局唯一*。 如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 （例如 yournameADFTutorialDataFactory）。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="针对重复名称的新数据工厂错误消息。":::
4. 选择要在其中创建数据工厂的 Azure **订阅**。
5. 对于“资源组”，请执行以下步骤之一： 

    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 

    b. 选择“新建”，并输入资源组的名称。 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。 
6. 在“版本”下选择“V2”。 
7. 在“位置”下选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如 Azure 存储和 SQL 数据库）和计算资源（例如 Azure HDInsight）可以位于其他区域。
8. 选择“创建”  。
9. 创建完成后，通知中心内会显示通知。 选择“转到资源”导航到“数据工厂”页。
10. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>创建包含数据流活动的管道

在此步骤中，你将创建一个包含数据流活动的管道。

1. 在 Azure 数据工厂的主页上，选择“协调”。

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="显示 ADF 主页的屏幕截图。":::

1. 在管道的“常规”选项卡中，输入 TransformMovies 作为管道的名称  。
1. 在“活动”窗格中，展开“移动和转换”可折叠部分 。 将“数据流”活动从该窗格拖放到管道画布上。

    :::image type="content" source="media/tutorial-data-flow/activity1.png" alt-text="显示管道画布的屏幕截图，你可以在其中放置数据流活动。":::
1. 在“添加数据流”弹出窗口中，选择“创建新数据流”，然后将数据流命名为 TransformMovies  。 完成操作后，请单击“完成”。

    :::image type="content" source="media/tutorial-data-flow/activity2.png" alt-text="显示在创建新数据流时对数据流进行命名的位置的屏幕截图。":::
1. 在管道画布的顶部栏中，将“数据流调试”滑块滑动到打开。 调试模式允许针对实时 Spark 群集进行转换逻辑的交互式测试。 数据流群集需要 5-7 分钟才能预热，如果用户计划进行数据流开发，建议先打开调试。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

    :::image type="content" source="media/tutorial-data-flow/dataflow1.png" alt-text="数据流活动":::

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在数据流画布中构建转换逻辑

创建数据流后，会自动将其发送到数据流画布。 在此步骤中，你将构建一个数据流，该数据流采用 ADLS 存储中的 moviesDB.csv，并聚合从 1910 到 2000 年的平均喜剧评分。 然后，将此文件写回到 ADLS 存储。

1. 在数据流画布中，通过单击“添加源”框来添加源。

    :::image type="content" source="media/tutorial-data-flow/dataflow2.png" alt-text="显示“添加源”框的屏幕截图。":::
1. 将源命名为 MoviesDB。 单击“新建”以创建新的源数据集。

    :::image type="content" source="media/tutorial-data-flow/dataflow3.png" alt-text="显示在命名源后选择“新建”的位置的屏幕截图。":::
1. 选择 Azure Data Lake Storage Gen2。 单击“继续”(Continue)。

    :::image type="content" source="media/tutorial-data-flow/dataset1.png" alt-text="显示 Azure Data Lake Storage Gen2 磁贴位置的屏幕截图。":::
1. 选择 DelimitedText。 单击“继续”(Continue)。

    :::image type="content" source="media/tutorial-data-flow/dataset2.png" alt-text="显示 DelimitedText 磁贴的屏幕截图。":::
1. 将数据集命名为 MoviesDB。 在链接服务下拉列表中，选择“新建”。

    :::image type="content" source="media/tutorial-data-flow/dataset3.png" alt-text="显示“链接服务”下拉列表的屏幕截图。":::
1. 在链接服务创建屏幕中，将 ADLS Gen2 链接服务命名为 ADLSGen2，并指定身份验证方法。 然后输入连接凭据。 在本教程中，我们将使用帐户密钥连接到存储帐户。 可以单击“测试连接”以验证是否已正确输入凭据。 完成后，单击“创建”。

    :::image type="content" source="media/tutorial-data-flow/ls1.png" alt-text="链接服务":::
1. 返回数据集创建屏幕后，请在“文件路径”字段下输入文件所在的位置。 在本教程中，文件 moviesDB.csv 位于容器 sample-data 中。 由于文件具有标头，请选择“第一行作为标头”。 选择“从连接/存储”，以直接从存储中的文件导入标头架构。 完成后单击“确定”。

    :::image type="content" source="media/tutorial-data-flow/dataset4.png" alt-text="数据集":::
1. 如果调试群集已启动，请转到源转换的“数据预览”选项卡，然后单击“刷新”以获取数据的快照 。 可以使用数据预览来验证是否已正确配置转换。

    :::image type="content" source="media/tutorial-data-flow/dataflow4.png" alt-text="显示你可以在何处预览数据以验证是否已正确配置转换的屏幕截图。":::
1. 在数据流画布上的源节点旁边，单击加号图标以添加新转换。 要添加的第一个转换是“筛选器”。

    :::image type="content" source="media/tutorial-data-flow/dataflow5.png" alt-text="数据流画布":::
1. 将筛选器转换命名为 FilterYears。 单击“筛选依据”旁的表达式框以打开表达式生成器。 可在此处指定筛选条件。

    :::image type="content" source="media/tutorial-data-flow/filter1.png" alt-text="显示“筛选依据”表达式框的屏幕截图。":::
1. 数据流表达式生成器允许你以交互方式生成要用于各种转换的表达式。 表达式可以包含内置函数、输入架构中的列和用户定义的参数。 有关如何生成表达式的详细信息，请参阅[数据流表达式生成器](concepts-data-flow-expression-builder.md)。

    在本教程中，你要筛选在 1910 到 2000 年之间上映的喜剧流派电影。 由于年份目前是一个字符串，因此需要使用 `toInteger()` 函数将其转换为整数。 使用大于或等于 (>=) 和小于或等于 (<=) 运算符来与文本年份值 1910 和 2000 进行比较。 将这些表达式与 and (&&) 运算符结合在一起。 表达式如下所示：

    `toInteger(year) >= 1910 && toInteger(year) <= 2000`

    若要找出哪些电影属于喜剧，可以使用 `rlike()` 函数查找 genres 列中的“Comedy”。 将 `rlike` 表达式与年份比较结合得到：

    `toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')`

    如果调试群集处于活动状态，则可以通过单击“刷新”来查看表达式输出与所使用的输入之间的比较，以验证逻辑。 关于如何使用数据流表达式语言实现这个逻辑，有多个正确答案。

    :::image type="content" source="media/tutorial-data-flow/filter2.png" alt-text="Filter":::

    在完成表达式后，单击“保存并完成”。

1. 提取“数据预览”以验证筛选器是否正常工作。

    :::image type="content" source="media/tutorial-data-flow/filter3.png" alt-text="显示你提取的数据预览的屏幕截图。":::
1. 要添加的下一个转换是“架构修饰符”下的“聚合”转换 。

    :::image type="content" source="media/tutorial-data-flow/agg1.png" alt-text="显示聚合架构修饰符的屏幕截图。":::
1. 将聚合转换命名为 AggregateComedyRatings。 在“分组依据”选项卡中，从下拉列表中选择“年份”，按电影上映的年份对聚合进行分组 。

    :::image type="content" source="media/tutorial-data-flow/agg2.png" alt-text="显示聚合设置下“分组依据”选项卡中年份选项的屏幕截图。":::
1. 转到“聚合”选项卡。在左侧文本框中，将聚合列命名为 AverageComedyRating。 单击右侧的表达式框，通过表达式生成器输入聚合表达式。

    :::image type="content" source="media/tutorial-data-flow/agg3.png" alt-text="显示聚合设置下“聚合”选项卡中年份选项的屏幕截图。":::
1. 若要获取 Rating 列的平均值，请使用 ```avg()``` 聚合函数。 由于 Rating 为字符串并且 ```avg()``` 采用数字输入，因此必须通过 ```toInteger()``` 函数将该值转换为数字。 该表达式如下所示：

    `avg(toInteger(Rating))`

    完成后，单击“保存并完成”

    :::image type="content" source="media/tutorial-data-flow/agg4.png" alt-text="显示保存的表达式的屏幕截图。":::
1. 转到“数据预览”选项卡以查看转换输出。 请注意，这里只有两个列，year 和 AverageComedyRating 。

    :::image type="content" source="media/tutorial-data-flow/agg3.png" alt-text="聚合":::
1. 接下来，你需要在“目标”下添加“接收器”转换 。

    :::image type="content" source="media/tutorial-data-flow/sink1.png" alt-text="显示在目标下添加接收器转换的位置的屏幕截图。":::
1. 将接收器命名为 Sink。 单击“新建”以创建接收器数据集。

    :::image type="content" source="media/tutorial-data-flow/sink2.png" alt-text="显示你可以在何处命名接收器并创建新接收器数据集的屏幕截图。":::
1. 选择 Azure Data Lake Storage Gen2。 单击“继续”(Continue)。

    :::image type="content" source="media/tutorial-data-flow/dataset1.png" alt-text="显示你可以选择的 Azure Data Lake Storage Gen2 磁贴的屏幕截图。":::
1. 选择 DelimitedText。 单击“继续”(Continue)。

    数据集
1. 将接收器数据集命名为 MoviesSink。 对于链接服务，请选择在步骤 6 中创建的 ADLS gen2 链接服务。 输入要向其写入数据的输出文件夹。 在本教程中，我们将写入容器“sample-data”中的文件夹“output”。 该文件夹不需要事先存在，可以动态创建。 将“第一行作为标头”设置为 true，并为“导入架构”选择“无”  。 单击“完成”。

    :::image type="content" source="media/tutorial-data-flow/sink3.png" alt-text="接收器":::

现在，你已经完成了数据流的构建。 你已准备好在管道中运行它。

## <a name="running-and-monitoring-the-data-flow"></a>运行和监视数据流

可以在发布管道之前对其进行调试。 在此步骤中，将触发数据流管道的调试运行。 当数据预览不写入数据时，调试运行会将数据写入接收器目标。

1. 转到管道画布。 单击“调试”以触发调试运行。

    :::image type="content" source="media/tutorial-data-flow/pipeline1.png" alt-text="显示管道画布的屏幕截图，起哄突出显示了“调试”。":::
1. 数据流活动的管道调试使用活动调试群集，但仍至少需要一分钟的时间来初始化。 可以通过“输出”选项卡跟踪进度。运行成功后，单击眼镜图标打开监视窗格。

    管道
1. 在监视窗格中，你可以看到每个转换步骤中的行数和所用时间。

    :::image type="content" source="media/tutorial-data-flow/pipeline3.png" alt-text="显示监视窗格的屏幕截图，可在其中看到每个转换步骤中的行数和所用时间。":::
1. 单击转换可获取有关列和数据分区的详细信息。

    :::image type="content" source="media/tutorial-data-flow/pipeline4.png" alt-text="Monitoring":::

如果已正确遵循本教程，则应在接收器文件夹中写入 83 个行和 2 个列。 可以通过检查 Blob 存储来验证数据是否正确。

## <a name="next-steps"></a>后续步骤

本教程中的管道运行一个数据流，该数据流聚合从 1910 到 2000 年的平均喜剧评分，并将数据写入 ADLS。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建包含数据流活动的管道。
> * 构建具有四个转换的映射数据流。
> * 测试性运行管道。
> * 监视数据流活动

详细了解[数据流表达式语言](data-flow-expression-functions.md)。
