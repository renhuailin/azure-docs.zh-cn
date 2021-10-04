---
title: 快速入门：使用映射数据流转换数据
description: 本教程提供有关通过 Azure Synapse Analytics 使用映射数据流转换数据的分步说明。
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: 481d4534f7213d55c795383d6112fd93f7997c9c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596859"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>快速入门：使用映射数据流转换数据

在本快速入门中，你将使用 Azure Synapse Analytics 创建一个管道，该管道使用映射数据流将数据从 Azure Data Lake Storage Gen2 (ADLS Gen2) 源转换为 ADLS Gen2 接收器。 使用映射数据流转换数据时，可以扩展本快速入门中的配置模式

在本快速入门中，你将执行以下步骤：

> [!div class="checklist"]
> * 在 Azure Synapse Analytics 中创建包含数据流活动的管道。
> * 构建具有四个转换的映射数据流。
> * 测试性运行管道。
> * 监视数据流活动

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* Azure Synapse 工作区：按照[快速入门：创建 Synapse 工作区](quickstart-create-workspace.md)中的说明，使用 Azure 门户创建 Synapse 工作区。
* Azure 存储帐户：将 ADLS 存储用作“源”和“接收器”数据存储 。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../storage/common/storage-account-create.md)以获取创建步骤。

    在本教程中，我们要转换的文件是 MoviesDB.csv，可在[此处](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)找到。 若要从 GitHub 检索该文件，请将内容复制到所选的文本编辑器，在本地保存为 .csv 文件。 若要将文件上传到存储帐户，请参阅[使用 Azure 门户上传 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。 这些示例将引用名为“sample-data”的容器。

### <a name="navigate-to-the-synapse-studio"></a>导航到 Synapse Studio

创建 Azure Synapse 工作区后，可以通过两种方式打开 Synapse Studio：

* 在 [Azure 门户](https://ms.portal.azure.com/#home)中打开 Synapse 工作区。 在“开始”下的“打开 Synapse Studio”卡上选择“打开”。
* 打开 [Azure Synapse Analytics](https://web.azuresynapse.net/) 并登录到工作区。

在本快速入门中，我们将使用名为“adftest2020”的工作区作为示例。 它将自动导航到 Synapse Studio 主页。

![Synapse Studio 主页](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>创建包含数据流活动的管道

管道包含用于执行一组活动的逻辑流。 在本部分中，你将创建一个包含数据流活动的管道。

1. 转到“集成”选项卡。选择管道标题旁边的加号图标，然后选择“管道”。

   ![创建新管道](media/doc-common-process/new-pipeline.png)

1. 在管道的“属性”设置页中，输入 TransformMovies 作为管道的名称  。

1. 在“活动”窗格中的“移动和转换”下，将“数据流”拖到管道画布上 。

1. 在“添加数据流”页面弹出菜单中，选择“创建新数据流” -> “数据流”  。 完成后单击“确定”。

   ![创建数据流](media/quickstart-data-flow/new-data-flow.png)

1. 在“属性”页上将数据流命名为“TransformMovies” 。

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在数据流画布中构建转换逻辑

创建数据流后，会自动将其发送到数据流画布。 在此步骤中，你将构建一个数据流，该数据流采用 ADLS 存储中的 MoviesDB.csv，并聚合从 1910 到 2000 年的平均喜剧评分。 然后，将此文件写回到 ADLS 存储。

1. 在数据流画布上方，将“数据流调试”滑块滑动到打开状态。 调试模式允许针对实时 Spark 群集进行转换逻辑的交互式测试。 数据流群集需要 5-7 分钟才能预热，如果用户计划进行数据流开发，建议先打开调试。 有关详细信息，请参阅[调试模式](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)。

    ![将调试滑动到打开状态](media/quickstart-data-flow/debug-on.png)

1. 在数据流画布中，通过单击“添加源”框来添加源。

1. 将源命名为 MoviesDB。 单击“新建”以创建新的源数据集。

    ![创建新的源数据集](media/quickstart-data-flow/new-source-dataset.png)

1. 选择 Azure Data Lake Storage Gen2。 单击“继续”(Continue)。

    ![选择 Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. 选择 DelimitedText。 单击“继续”(Continue)。

1. 将数据集命名为 MoviesDB。 在链接服务下拉列表中，选择“新建”。

1. 在链接服务创建屏幕中，将 ADLS Gen2 链接服务命名为 ADLSGen2，并指定身份验证方法。 然后输入连接凭据。 在本快速入门中，我们将使用帐户密钥连接到存储帐户。 可以单击“测试连接”以验证是否已正确输入凭据。 完成后，单击“创建”。

    ![创建源链接服务](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. 返回数据集创建屏幕后，请在“文件路径”字段下输入文件所在的位置。 在本快速入门中，文件“MoviesDB.csv”位于容器“sample-data”中。 由于文件具有标头，请选择“第一行作为标头”。 选择“从连接/存储”，以直接从存储中的文件导入标头架构。 完成后单击“确定”。

    ![源数据集设置](media/quickstart-data-flow/source-dataset-properties.png)

1. 如果调试群集已启动，请转到源转换的“数据预览”选项卡，然后单击“刷新”以获取数据的快照 。 可以使用数据预览来验证是否已正确配置转换。

    ![数据预览](media/quickstart-data-flow/data-preview.png)

1. 在数据流画布上的源节点旁边，单击加号图标以添加新转换。 要添加的第一个转换是“筛选器”。

    ![添加筛选器](media/quickstart-data-flow/add-filter.png)

1. 将筛选器转换命名为 FilterYears。 单击“筛选依据”旁的表达式框以打开表达式生成器。 可在此处指定筛选条件。

1. 数据流表达式生成器允许你以交互方式生成要用于各种转换的表达式。 表达式可以包含内置函数、输入架构中的列和用户定义的参数。 有关如何生成表达式的详细信息，请参阅[数据流表达式生成器](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)。

    在本快速入门中，你要筛选在 1910 到 2000 年之间上映的喜剧流派电影。 由于年份目前是一个字符串，因此需要使用 ```toInteger()``` 函数将其转换为整数。 使用大于或等于 (>=) 和小于或等于 (<=) 运算符来与文本年份值 1910 和 2000 进行比较。 将这些表达式与 and (&&) 运算符结合在一起。 表达式如下所示：

    `toInteger(year) >= 1910 && toInteger(year) <= 2000`

    若要找出哪些电影属于喜剧，可以使用 `rlike()` 函数查找 genres 列中的“Comedy”。 将 `rlike` 表达式与年份比较结合可得到：

    `toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')`

    ![指定筛选条件](media/quickstart-data-flow/visual-expression-builder.png)

    如果调试群集处于活动状态，则可以通过单击“刷新”来查看表达式输出与所使用的输入之间的比较，以验证逻辑。 关于如何使用数据流表达式语言实现这个逻辑，有多个正确答案。

    在完成表达式后，单击“保存并完成”。

1. 提取“数据预览”以验证筛选器是否正常工作。

1. 要添加的下一个转换是“架构修饰符”下的“聚合”转换 。

    ![添加聚合](media/quickstart-data-flow/add-aggregate.png)

1. 将聚合转换命名为 AggregateComedyRatings。 在“分组依据”选项卡中，从下拉列表中选择“年份”，按电影上映的年份对聚合进行分组 。

    ![聚合设置 1](media/quickstart-data-flow/aggregate-settings.png)

1. 转到“聚合”选项卡。在左侧文本框中，将聚合列命名为 AverageComedyRating。 单击右侧的表达式框，通过表达式生成器输入聚合表达式。

    ![聚合设置 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. 若要获取 Rating 列的平均值，请使用 ```avg()``` 聚合函数。 由于 Rating 为字符串并且 ```avg()``` 采用数字输入，因此必须通过 ```toInteger()``` 函数将该值转换为数字。 该表达式如下所示：

    `avg(toInteger(Rating))`

    完成后，单击“保存并完成”

    ![平均喜剧评分](media/quickstart-data-flow/average-comedy-rating.png)

1. 转到“数据预览”选项卡以查看转换输出。 请注意，这里只有两个列，year 和 AverageComedyRating 。

    ![聚合数据预览](media/quickstart-data-flow/transformation-output.png)

1. 接下来，你需要在“目标”下添加“接收器”转换 。

    ![添加接收器](media/quickstart-data-flow/add-sink.png)

1. 将接收器命名为 Sink。 单击“新建”以创建接收器数据集。

1. 选择 Azure Data Lake Storage Gen2。 单击“继续”(Continue)。

1. 选择 DelimitedText。 单击“继续”(Continue)。

1. 将接收器数据集命名为 MoviesSink。 对于链接服务，请选择在步骤 7 中创建的 ADLS Gen2 链接服务。 输入要向其写入数据的输出文件夹。 在本快速入门中，我们将写入容器“sample-data”中的文件夹“output”。 该文件夹不需要事先存在，可以动态创建。 将“第一行作为标头”设置为 true，并为“导入架构”选择“无”  。 完成后单击“确定”。

    ![接收器数据集属性](media/quickstart-data-flow/sink-dataset-properties.png)

现在，你已经完成了数据流的构建。 你已准备好在管道中运行它。

## <a name="running-and-monitoring-the-data-flow"></a>运行和监视数据流

可以在发布管道之前对其进行调试。 在此步骤中，将触发数据流管道的调试运行。 当数据预览不写入数据时，调试运行会将数据写入接收器目标。

1. 转到管道画布。 单击“调试”以触发调试运行。

    ![调试管道](media/quickstart-data-flow/debug-pipeline.png)

1. 数据流活动的管道调试使用活动调试群集，但仍至少需要一分钟的时间来初始化。 可以通过“输出”选项卡跟踪进度。运行成功后，单击眼镜图标打开监视窗格。

    ![调试输出](media/quickstart-data-flow/debugging-output.png)

1. 在监视窗格中，你可以看到每个转换步骤中的行数和所用时间。

    ![转换监视](media/quickstart-data-flow/4-transformations.png)

1. 单击转换可获取有关列和数据分区的详细信息。

    ![转换详细信息](media/quickstart-data-flow/transformation-details.png)

如果已正确遵循本快速入门，则应在接收器文件夹中写入 83 个行和 2 个列。 可以通过检查 Blob 存储来验证数据。


## <a name="next-steps"></a>后续步骤

请转至下列文章，了解 Azure Synapse Analytics 支持：

> [!div class="nextstepaction"]
> [管道和活动](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [映射数据流概述](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [数据流表达式语言](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
