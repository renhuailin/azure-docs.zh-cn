---
title: 使用 Azure 数据工厂托管虚拟网络映射数据流来转换数据
description: 本教程分步介绍了如何使用 Azure 数据工厂通过映射数据流转换数据。
author: ssabat
ms.author: susabat
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/04/2021
ms.openlocfilehash: fb7dfc93048d888eb83088ca2a3f458219fdc393
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617880"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>使用映射数据流安全地转换数据

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](./introduction.md)。

在本教程中，你将使用数据工厂用户界面 (UI) 创建一个管道，此管道使用[数据工厂托管虚拟网络](managed-virtual-network-private-endpoint.md)中的映射数据流将数据从 Azure Data Lake Storage Gen2 源复制并转换到 Data Lake Storage Gen2 接收器（两者都只允许访问选定的网络）。 当你使用映射数据流来转换数据时，可以扩展本教程中的配置模式。

在本教程中，将执行以下步骤：

> [!div class="checklist"]
>
> * 创建数据工厂。
> * 创建包含数据流活动的管道。
> * 构建具有四个转换的映射数据流。
> * 测试性运行管道。
> * 监视数据流活动。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**。 使用 Data Lake Storage 作为源和接收器数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)以获取创建步骤。 *确保存储帐户仅允许来自选定的网络的访问。* 

我们将在本教程中转换的文件是 moviesDB.csv，可以在此 [GitHub 内容站点](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)中找到它。 若要从 GitHub 中检索此文件，请将内容复制到你选择的文本编辑器中，并将其保存为本地的 .csv 文件。 若要将文件上传到存储帐户，请参阅[使用 Azure 门户上传 Blob](../storage/blobs/storage-quickstart-blobs-portal.md)。 这些示例将引用名为 sample-data 的容器。

## <a name="create-a-data-factory"></a>创建数据工厂

在此步骤中，你将创建数据工厂，并打开数据工厂 UI，以在此数据工厂中创建管道。

1. 打开 Microsoft Edge 或 Google Chrome。 目前，只有 Microsoft Edge 和 Google Chrome web 浏览器支持数据工厂 UI。
1. 在左侧菜单中，选择“创建资源” > “Analytics” > “数据工厂”。
1. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory** 。

   数据工厂的名称必须全局唯一。 如果收到有关名称值的错误消息，请为数据工厂输入其他名称（例如 yournameADFTutorialDataFactory）。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。

1. 选择要在其中创建数据工厂的 Azure **订阅**。
1. 对于“资源组”，请执行以下步骤之一： 

    * 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
    * 选择“新建”，并输入资源组的名称。 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。 
1. 在“版本”下选择“V2”。 
1. 在“位置”下选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如 Azure 存储和 Azure SQL 数据库）和计算（例如 Azure HDInsight）可以位于其他区域中。

1. 选择“创建”  。
1. 创建完成后，通知中心内会显示通知。 选择“转到资源”，转到“数据工厂”页 。
1. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>在数据工厂托管虚拟网络中创建 Azure IR

在此步骤中，你将创建 Azure IR，并启用数据工厂托管虚拟网络。

1. 在数据工厂门户中，转到“管理”，然后选择“新建”来新建 Azure IR。

   :::image type="content" source="./media/tutorial-copy-data-portal-private/create-new-azure-ir.png" alt-text="展示了如何新建 Azure IR 的屏幕截图。":::
1. 在“集成运行时安装”页上，根据所需的功能选择要创建的集成运行时。 在本教程中，选择“Azure”、“自承载”，然后单击“继续” 。 
1. 选择“Azure”，然后单击“继续”以创建 Azure 集成运行时 。

   :::image type="content" source="./media/tutorial-copy-data-portal-private/azure-ir.png" alt-text="展示了新 Azure IR 的屏幕截图。":::

1. 在“虚拟网络配置(预览)”下，选择“启用” 。

   :::image type="content" source="./media/tutorial-copy-data-portal-private/enable-managed-vnet.png" alt-text="展示了如何启用新 Azure IR 的屏幕截图。":::

1. 选择“创建”  。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>创建包含数据流活动的管道

在此步骤中，你将创建一个包含数据流活动的管道。

1. 在 Azure 数据工厂的主页上，选择“协调”。

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="屏幕截图显示正在创建管道。":::

1. 在管道的“属性”窗格中，输入“TransformMovies”作为管道名称。
1. 在“活动”窗格中，展开“移动和转换”。 将“数据流”活动从窗格中拖动到管道画布上。

1. 在“添加数据流”弹出窗口中，选中“新建数据流”，然后选择“映射数据流”。 完成后，选择“确定”。

    :::image type="content" source="media/tutorial-data-flow-private/mapping-dataflow.png" alt-text="展示了“映射数据流”的屏幕截图。":::

1. 在“属性”窗格中，将数据流命名为“TransformMovies”。
1. 在管道画布的顶部栏中，将“数据流调试”滑块滑动到打开。 调试模式允许针对实时 Spark 群集进行转换逻辑的交互式测试。 数据流群集需要 5-7 分钟才能预热，如果用户计划进行数据流开发，建议先打开调试。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

    :::image type="content" source="media/tutorial-data-flow-private/dataflow-debug.png" alt-text="展示了“数据流调试”滑块的屏幕截图。":::

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在数据流画布中构建转换逻辑

在创建数据流后，你会自动转到数据流画布。 在此步骤中，你将生成一个数据流，此数据流使用 Data Lake Storage 中的 moviesDB.csv 文件，并聚合从 1910 年到 2000 年的喜剧平均评分。 然后，你将此文件写回到 Data Lake Storage 中。

### <a name="add-the-source-transformation"></a>添加源转换

在此步骤中，你将创建 Data Lake Storage Gen2 作为源。

1. 在数据流画布中，通过选中“添加源”框来添加源。

1. 将源命名为 MoviesDB。 选择“新建”以创建新的源数据集。

1. 选中“Azure Data Lake Storage Gen2”，然后选择“继续”。

1. 选中“DelimitedText”，然后选择“继续”。

1. 将数据集命名为 MoviesDB。 在“链接服务”下拉列表中，选择“新建”。

1. 在“创建链接服务”屏幕中，将 Data Lake Storage Gen2 链接服务命名为“ADLSGen2”，并指定身份验证方法。 然后输入连接凭据。 在本教程中，我们将使用帐户密钥连接到存储帐户。 

1. 请确保启用“交互式创作”。 它可能需要 1 分钟才能启用。

    :::image type="content" source="./media/tutorial-data-flow-private/interactive-authoring.png" alt-text="显示交互式创作的屏幕截图。":::

1. 选择“测试连接”。 它应该会失败，因为存储帐户不允许在没有创建和批准专用终结点的情况下访问它。 在错误消息中，应会看到一个用于创建专用终结点的链接，可以通过该链接创建托管的专用终结点。 替代方法是直接转到“管理”选项卡，然后按照[此部分](#create-a-managed-private-endpoint)中的说明操作来创建托管专用终结点。

1. 让对话框保持打开状态，然后转到存储帐户。

1. 按照[此部分](#approval-of-a-private-link-in-a-storage-account)中的说明批准专用链接。

1. 返回对话框。 再次选择“测试连接”，然后选择“创建”以部署链接服务 。

1. 在“创建数据集”屏幕上的“文件路径”字段下，输入文件所在的位置。 在本教程中，文件 moviesDB.csv 位于容器 sample-data 中。 由于此文件有标题，因此请选中“第一行作为标题”复选框。 选择“从连接/存储”，以直接从存储中的文件导入标头架构。 完成后，选择“确定”。

    :::image type="content" source="media/tutorial-data-flow-private/source-file-path.png" alt-text="展示了源路径的屏幕截图。":::

1. 如果调试群集已启动，请转到源转换的“数据预览”选项卡，然后选择“刷新”来获取数据快照。 可以使用数据预览来验证是否已正确配置转换。

    :::image type="content" source="media/tutorial-data-flow-private/data-preview.png" alt-text="展示了“数据预览”选项卡的屏幕截图。":::

#### <a name="create-a-managed-private-endpoint"></a>创建托管专用终结点

如果在测试前面的连接时没有使用超链接，请遵循路径。 现在，需要创建一个托管的专用终结点，并将该终结点连接到你创建的链接服务。

1. 转到“管理”选项卡。

   > [!NOTE]
   > “管理”选项卡可能并非适用于所有数据工厂实例。 如果看不到该选项卡，可以通过选择“作者” > “连接” > “专用终结点”来访问专用终结点  。

1. 转到“托管专用终结点”部分。
1. 选择“托管专用终结点”下的“+ 新建” 。

    :::image type="content" source="./media/tutorial-data-flow-private/new-managed-private-endpoint.png" alt-text="显示托管专用终结点“新建”按钮的屏幕截图。"::: 

1. 从列表中选择“Azure Data Lake Storage Gen2”磁贴，然后选择“继续”。
1. 输入创建的存储帐户的名称。
1. 选择“创建”。
1. 几秒钟后，应会显示创建的专用链接需要审批。
1. 选择创建的专用终结点。 可以看到一个超链接，该超链接将引导你在存储帐户级别批准专用终结点。

    :::image type="content" source="./media/tutorial-data-flow-private/manage-private-endpoint.png" alt-text="展示了“管理专用终结点”窗格的屏幕截图。"::: 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>在存储帐户中批准专用链接

1. 在存储帐户中，转到“设置”部分下的“专用终结点连接” 。

1. 选中你创建的专用终结点旁边的复选框，然后选择“批准”。

    :::image type="content" source="./media/tutorial-data-flow-private/approve-private-endpoint.png" alt-text="展示了专用终结点“批准”按钮的屏幕截图。":::

1. 添加描述，然后选择“是”。
1. 返回到数据工厂中“管理”选项卡的“托管专用终结点”部分 。
1. 大约一分钟后，应会出现对专用终结点的批准。

### <a name="add-the-filter-transformation"></a>添加筛选转换

1. 在数据流画布上的源节点旁边，选择加号图标来添加新转换。 要添加的第一个转换是“筛选”。

    :::image type="content" source="media/tutorial-data-flow-private/add-filter.png" alt-text="展示了如何添加筛选的屏幕截图。":::
1. 将筛选器转换命名为 FilterYears。 选择“筛选依据”旁边的表达式框，以打开表达式生成器。 可在此处指定筛选条件。

    :::image type="content" source="media/tutorial-data-flow-private/filter-years.png" alt-text="展示了 FilterYears 的屏幕截图。":::
1. 数据流表达式生成器允许你以交互方式生成要用于各种转换的表达式。 表达式可以包含内置函数、输入架构中的列和用户定义的参数。 若要详细了解如何生成表达式，请参阅[数据流表达式生成器](./concepts-data-flow-expression-builder.md)。

    * 在本教程中，你要筛选出在 1910 年到 2000 年之间上映的喜剧流派电影。 由于年份目前是字符串，因此你需要使用 `toInteger()` 函数将其转换为整数。 使用大于或等于 (>=) 和小于或等于 (<=) 运算符与字面量年份值 1910 和 2000 进行比较。 将这些表达式与 and (&&) 运算符结合在一起。 表达式如下所示：

        `toInteger(year) >= 1910 && toInteger(year) <= 2000`

    * 若要找出哪些电影是喜剧，则可以使用 `rlike()` 函数在“流派”列中查找“喜剧”模式。 将 `rlike` 表达式与年份比较结合可得到：

        `toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')`

    * 如果有处于活动状态的调试群集，则可以通过选择“刷新”来查看表达式输出与所使用的输入之间的比较，以验证逻辑。 关于如何使用数据流表达式语言来实现此逻辑，有多个正确答案。

        :::image type="content" source="media/tutorial-data-flow-private/filter-expression.png" alt-text="展示了筛选表达式的屏幕截图。":::

    * 完成表达式后，选择“保存并完成”。

1. 提取“数据预览”以验证筛选器是否正常工作。

    :::image type="content" source="media/tutorial-data-flow-private/filter-data.png" alt-text="展示了筛选后的“数据预览”的屏幕截图。":::

### <a name="add-the-aggregate-transformation"></a>添加聚合转换

1. 要添加的下一个转换是“架构修饰符”下的“聚合”转换 。

    :::image type="content" source="media/tutorial-data-flow-private/add-aggregate.png" alt-text="展示了如何添加聚合的屏幕截图。":::
1. 将聚合转换命名为“AggregateComedyRating”。 在“分组依据”选项卡中，从下拉框中选择“年份”，以按电影上映年份对聚合进行分组。

    :::image type="content" source="media/tutorial-data-flow-private/group-by-year.png" alt-text="展示了聚合组的屏幕截图。":::
1. 转到“聚合”选项卡。在左侧文本框中，将聚合列命名为 AverageComedyRating。 选择右侧的表达式框，以通过表达式生成器输入聚合表达式。

    :::image type="content" source="media/tutorial-data-flow-private/name-column.png" alt-text="展示了聚合列名称的屏幕截图。":::
1. 若要获取 Rating 列的平均值，请使用 ```avg()``` 聚合函数。 由于“评分”是字符串，但 ```avg()``` 接受数字输入，因此必须通过 ```toInteger()``` 函数将值转换为数字。 该表达式如下所示：

    `avg(toInteger(Rating))`

1. 完成后，选择“保存并完成”。

    :::image type="content" source="media/tutorial-data-flow-private/save-aggregate.png" alt-text="展示了如何保存聚合的屏幕截图。":::
1. 转到“数据预览”选项卡以查看转换输出。 请注意，这里只有两个列，year 和 AverageComedyRating 。

### <a name="add-the-sink-transformation"></a>添加接收器转换

1. 接下来，你需要在“目标”下添加“接收器”转换 。

    :::image type="content" source="media/tutorial-data-flow-private/add-sink.png" alt-text="展示了如何添加接收器的屏幕截图。":::
1. 将接收器命名为 Sink。 选择“新建”，以创建接收器数据集。

    :::image type="content" source="media/tutorial-data-flow-private/create-sink.png" alt-text="展示了如何创建接收器的屏幕截图。":::
1. 在“新建数据集”页中，选中“Azure Data Lake Storage Gen2”，然后选择“继续”。

1. 在“选择格式”页上，选中“DelimitedText”，然后选择“继续”。

1. 将接收器数据集命名为 MoviesSink。 对于链接服务，选择你为源转换创建的同一 ADLSGen2 链接服务。 输入要向其写入数据的输出文件夹。 在本教程中，我们将写入到容器 sample-data 中的文件夹 output。 该文件夹不需要事先存在，可以动态创建。 选中“第一行作为标题”复选框，并为“导入架构”选择“无”。 选择“确定”。

    :::image type="content" source="media/tutorial-data-flow-private/sink-file-path.png" alt-text="展示了接收器路径的屏幕截图。":::

现在，你已经完成了数据流的构建。 你已准备好在管道中运行它。

## <a name="run-and-monitor-the-data-flow"></a>运行和监视数据流

可以在发布管道之前对其进行调试。 在此步骤中，你将触发数据流管道的调试运行。 虽然数据预览不写入数据，但调试运行会将数据写入到接收器目标中。

1. 转到管道画布。 选择“调试”，以触发调试运行。

1. 数据流活动的管道调试使用活动的调试群集，但初始化仍然需要至少一分钟。 可以通过“输出”选项卡跟踪进度。运行成功后，选择眼镜图标查看“运行详细信息”。

1. 在“详细信息”页上，可以看到行数和每个转换步骤所花费的时间。

    :::image type="content" source="media/tutorial-data-flow-private/run-details.png" alt-text="展示了监视运行的屏幕截图。":::
1. 选择转换来获取数据的列和分区的详细信息。

如果已正确遵循本教程，则应在接收器文件夹中写入 83 个行和 2 个列。 可以通过检查 Blob 存储来验证数据是否正确。

## <a name="summary"></a>总结

在本教程中，你使用数据工厂 UI 创建了一个管道，此管道使用[数据工厂托管虚拟网络](managed-virtual-network-private-endpoint.md)中的映射数据流将数据从 Data Lake Storage Gen2 源复制并转换到 Data Lake Storage Gen2 接收器（两者都只允许访问选定的网络）。
