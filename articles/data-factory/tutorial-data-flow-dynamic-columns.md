---
title: 在数据流中动态设置列名
description: 本教程提供在数据流中动态设置列名的步骤
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/17/2021
ms.openlocfilehash: 96143f39811658c2794b46f3504a1a604264ab13
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112542766"
---
# <a name="dynamically-set-column-names-in-data-flows"></a>在数据流中动态设置列名

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在处理 ETL 作业的数据时，你经常需要在写入结果之前更改列名。 有时，只有更改列名才能使列名与已知的目标架构相符。 此外，在运行时，你可能需要根据不断变化的架构设置列名。 本教程介绍如何使用数据流通过外部配置文件和参数动态设置目标文件与数据库表的列名。

如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**。 将 ADLS 存储用作“源”和“接收器”数据存储 。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../storage/common/storage-account-create.md)以获取创建步骤。

## <a name="create-a-data-factory"></a>创建数据工厂

在此步骤中，请先创建数据工厂，然后打开数据工厂 UX，在该数据工厂中创建一个管道。

1. 打开 **Microsoft Edge** 或 **Google Chrome**。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 在左侧菜单中，选择“创建资源” > “集成” > “数据工厂”  
1. 在“新建数据工厂”页面上，在“名称”下输入“ADFTutorialDataFactory” 。
1. 选择要在其中创建数据工厂的 Azure **订阅**。
1. 对于“资源组”，请执行以下步骤之一： 
    * 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
    * 选择“新建”，并输入资源组的名称。若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。    
1. 在“版本”下选择“V2”。 
1. 在“位置”下选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如 Azure 存储和 SQL 数据库）和计算资源（例如 Azure HDInsight）可以位于其他区域。
1. 选择“创建”  。
1. 创建完成后，通知中心内会显示通知。 选择“转到资源”导航到“数据工厂”页。
1. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>创建包含数据流活动的管道

在此步骤中，你将创建一个包含数据流活动的管道。

1. 在 ADF 主页中，选择“创建管道”。
1. 在管道的“常规”选项卡中，输入“DeltaLake”作为管道的名称。 
1. 在工厂顶部栏中，将“数据流调试”滑块滑动到打开。 调试模式允许针对实时 Spark 群集进行转换逻辑的交互式测试。 数据流群集需要 5-7 分钟才能预热，如果用户计划进行数据流开发，建议先打开调试。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

    ![数据流活动](media/tutorial-data-flow/dataflow1.png)
1. 在“活动”窗格中，展开“移动和转换”可折叠部分 。 将“数据流”活动从该窗格拖放到管道画布上。

    ![显示管道画布的屏幕截图，你可以在其中放置数据流活动。](media/tutorial-data-flow/activity1.png)
1. 在“添加数据流”弹出窗口中选择“创建新数据流”，然后将数据流命名为“DynaCols”  。 完成操作后，请单击“完成”。    

## <a name="build-dynamic-column-mapping-in-data-flows"></a>在数据流中生成动态列映射

本教程将使用一个示例电影分级文件，并将源中的几个字段重命名为一组可随时更改的新目标列。 下面创建的数据集应指向你的 Blob 存储帐户或 ADLS Gen2 存储帐户中的此电影分级 CSV 文件。 [在此处下载电影分级文件](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB.csv)，并将其存储在你的 Azure 存储帐户中。

![最终流](media/data-flow/dynacols-1.png "最终流")

### <a name="tutorial-objectives"></a>教程目标

你将了解如何使用数据流动态设置列名

1. 为电影分级 CSV 文件创建源数据集。
1. 为字段映射 JSON 配置文件创建查找数据集。
1. 将源中的列转换为目标列名称。

### <a name="start-from-a-blank-data-flow-canvas"></a>从一个空白的数据流画布开始

首先，让我们为下述每个机制设置数据流环境，使数据能够进入 ADLS Gen2。

1. 单击源转换并将其命名为 ```movies1```。
1. 单击底部面板中数据集旁的新建按钮。
1. 选择“Blob”或“ADLS Gen2”，具体取决于前面你将 moviesDB.csv 文件存储到的位置。
1. 添加第二个源，我们将使用它来寻找配置 JSON 文件，以查找字段映射。
1. 将此源命名为 ```columnmappings```。
1. 对于数据集，请指向用于存储列映射配置的新 JSON 文件。 对于此教程示例，可将以下代码粘贴到 JSON 文件中：
    ```
    [
    {"prevcolumn":"title","newcolumn":"movietitle"},
    {"prevcolumn":"year","newcolumn":"releaseyear"}
    ]
    ```

1. 将此源设置指定为 ```array of documents```。
1. 添加第三个源并将其命名为 ```movies2```。 像配置 ```movies1``` 一样配置此源。
   
### <a name="parameterized-column-mapping"></a>参数化列映射

在这第一个方案中，你将使用一个参数（列的字符串数组）基于匹配的传入字段设置列映射，以此在数据流中设置输出列名，然后使每个数组索引与传入列序号位置匹配。 从管道执行此数据流时，可以通过向数据流活动发送此字符串数组参数，在每个管道执行上设置不同的列名。

![Parameters](media/data-flow/dynacols-3.png "参数")

1. 返回到数据流设计器，编辑前面创建的数据流。
1. 单击“参数”选项卡
1. 创建新参数并选择字符串数组数据类型
1. 输入 ```['a','b','c']``` 作为默认值
1. 使用顶部的 ```movies1``` 源修改列名，以映射到这些数组值
1. 添加 Select 转换。 该 Select 转换用于将传入列映射到要输出的新列名。
1. 我们要将前 3 个列名更改为参数中定义的新名称
1. 为此，请在底部窗格中添加 3 个基于规则的映射条目
1. 对于第一个列，匹配规则为 ```position==1```，名称为 ```$parameter1[1]```
1. 对于第 2 和第 3 个列，请遵循相同的模式
 
    ![选择转换](media/data-flow/dynacols-4.png "选择转换")

1. 单击 Select 转换的“检查”和“数据预览”选项卡，可以看到，新列名值 ```(a,b,c)``` 替换了原始的 movie、title 和 genres 列名
   
### <a name="create-a-cached-lookup-of-external-column-mappings"></a>创建外部列映射的缓存查找

接下来，我们将创建一个缓存接收器用于后续查找。 缓存将读取一个外部 JSON 配置文件，该文件可用于在数据流的每个管道执行上动态地重命名列。

1. 返回到数据流设计器，编辑前面创建的数据流。 将 Sink 转换添加到 ```columnmappings``` 源。
1. 将接收器类型设置为 ```Cache```。
1. 在“设置”下，选择 ```prevcolumn``` 作为键列。

### <a name="lookup-columns-names-from-cached-sink"></a>查找缓存接收器中的列名

将配置文件内容存储到内存中后，可将传入列名动态映射到新的传出列名。

1. 返回到数据流设计器并编辑上面的数据流创建。 单击 ```movies2``` 源转换。
1. 添加 Select 转换。 这一次，我们将使用 Select 转换来基于存储在缓存接收器中的 JSON 配置文件中的目标名称重命名列名。
1. 添加基于规则的映射。 对于“匹配条件”，请使用以下公式：```!isNull(cachedSink#lookup(name).prevcolumn)```。
1. 对于输出列名，请使用以下公式：```cachedSink#lookup($$).newcolumn```。
1. 我们要做的是在外部 JSON 配置文件中查找与 ```prevcolumn``` 属性匹配的所有列名，并将每个匹配项重命名为新的 ```newcolumn``` 名称。
1. 单击 Select 转换中的“数据预览”和“检查”选项卡，现在你应会看到外部映射文件中的新列名。

![源 2](media/data-flow/dynacols-2.png "源 2")

## <a name="next-steps"></a>后续步骤

* 可从[此处](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DynaColsPipe.zip)下载本教程中已完成的管道
* 了解有关[数据流接收器](data-flow-sink.md)的详细信息。
