---
title: 使用 Azure 数据工厂 UI 创建 Azure 数据工厂
description: 创建包含管道的数据工厂。该管道可将数据从 Azure Blob 存储中的一个位置复制到另一个位置。
author: jianleishen
ms.service: data-factory
ms.subservice: tutorials
ms.topic: quickstart
ms.date: 07/05/2021
ms.author: jianleishen
ms.openlocfilehash: ad2e2edf916b5b2c4309ed18c5a3349e67adfc33
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749895"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>快速入门：使用 Azure 数据工厂 UI 创建数据工厂 

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [当前版本](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本快速入门介绍如何使用 Azure 数据工厂 UI 创建和监视数据工厂。 在此数据工厂中创建的管道会将数据从 Azure Blob 存储中的一个文件夹复制到另一个文件夹。 若要使用 Azure 数据工厂转换数据，请参阅[映射数据流](concepts-data-flow-overview.md)。

> [!NOTE]
> 如果你对 Azure 数据工厂不太熟悉，请在学习本快速入门之前参阅 [Azure 数据工厂简介](introduction.md)。 

[!INCLUDE [data-factory-quickstart-prerequisites](includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>视频 
观看以下视频，了解数据工厂 UI： 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 转到 [Azure 门户](https://portal.azure.com)。 
1. 在 Azure 门户菜单中，选择“创建资源”。
1. 选择“集成”，然后选择“数据工厂” 。 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="在新建窗格中选择数据工厂。":::

1. 在“创建数据工厂”页上的“基本”选项卡下，选择要在其中创建数据工厂的 Azure 订阅  。
1. 对于“资源组”，请执行以下步骤之一：

    a. 从下拉列表中选择现有资源组。

    b. 选择“新建”，并输入新资源组的名称。
    
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。 

1. 对于“区域”，选择数据工厂所在的位置。

   该列表仅显示数据工厂支持的位置，以及 Azure 数据工厂元数据要存储到的位置。 数据工厂使用的关联数据存储（如 Azure 存储和 Azure SQL 数据库）和计算（如 Azure HDInsight）可以在其他区域中运行。
 
1. 对于“名称”，输入“ADFTutorialDataFactory” 。
   Azure 数据工厂的名称必须 *全局唯一*。 如果出现以下错误，请更改数据工厂的名称（例如改为 **&lt;yourname&gt;ADFTutorialDataFactory**），并重新尝试创建。 有关数据工厂项目的命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="针对重复名称的新数据工厂错误消息。":::

1. 对于“版本”，选择“V2”。 

1. 在完成时选择“下一步:Git 配置”，然后选择“稍后配置 Git”复选框。

1. 选择“查看 + 创建”，然后在通过验证后选择“创建” 。 创建完成后，选择“转到资源”导航到“数据工厂”页。 

1. 在“打开 Azure 数据工厂工作室”磁贴上选择“打开”，在单独的浏览器选项卡上启动 Azure 数据工厂用户界面 (UI) 应用程序。 
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure 数据工厂主页，其中包含“打开 Azure 数据工厂工作室”磁贴。":::
   
   > [!NOTE]
   > 如果看到 Web 浏览器停留在“正在授权”状态，请清除“阻止第三方 Cookie 和站点数据”复选框。 或者使其保持选中状态，为 **login.microsoftonline.com** 创建一个例外，然后再次尝试打开该应用。
   

## <a name="create-a-linked-service"></a>创建链接服务
在此过程中，请创建一个链接服务，将 Azure 存储帐户链接到数据工厂。 链接服务包含的连接信息可供数据工厂服务用来在运行时连接到它。

1. 在“Azure 数据工厂 UI”页上，从左侧窗格中打开[“管理”](./author-management-hub.md)选项卡。

1. 在“链接服务”页上，选择“+ 新建”以创建新的链接服务。

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="新建链接服务。":::
   
1. 在“新建链接服务”页中，选择“Azure Blob 存储”，然后选择“继续”。   

1. 在“新建链接服务(Azure Blob 存储)”页上，完成以下步骤： 

   a. 至于“名称”，请输入 **AzureStorageLinkedService**。

   b. 对于“存储帐户名称”，请选择 Azure 存储帐户的名称。

   c. 选择“测试连接”，确认数据工厂服务可以连接到存储帐户。 

   d. 选择“创建”以保存链接服务。 

      :::image type="content" source="./media/quickstart-create-data-factory-portal/linked-service.png" alt-text="链接服务。":::


## <a name="create-datasets"></a>创建数据集
此过程创建两个数据集：InputDataset 和 OutputDataset。 这两个数据集的类型为 **AzureBlob**。 它们引用在上一部分创建的 Azure 存储链接服务。 

输入数据集表示输入文件夹中的源数据。 在输入数据集定义中，请指定包含源数据的 Blob 容器 (**adftutorial**)、文件夹 (**input**) 和文件 (**emp.txt**)。 

输出数据集表示复制到目标的数据。 在输出数据集定义中，请指定要将数据复制到其中的 Blob 容器 (**adftutorial**)、文件夹 (**output**) 和文件。 管道的每次运行都有与之关联的唯一 ID。 可以使用系统变量 **RunId** 来访问该 ID。 输出文件的名称会根据管道的运行 ID 动态进行赋值。   

在链接服务设置中，已指定包含源数据的 Azure 存储帐户。 在源数据集设置中，请指定源数据的具体驻留位置（Blob 容器、文件夹和文件）。 在接收器数据集设置中，请指定将数据复制到其中的位置（Blob 容器、文件夹和文件）。 

1. 从左侧窗格中选择“创作”选项卡。

1. 选择“+ (加)”按钮，然后选择“数据集”。 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-dataset-menu.png" alt-text="用于创建数据集的菜单。":::

1. 在“新建数据集”页中，选择“Azure Blob 存储”，然后选择“继续”。   

1. 在“选择格式”页上选择数据的格式类型，然后选择“继续”。  在本例中，当按原样复制文件而不分析内容时，请选择“二进制”。

   :::image type="content" source="./media/quickstart-create-data-factory-portal/select-format.png" alt-text="选择格式。":::   
   
1. 在“设置属性”页上，完成以下步骤：

    a. 在“名称”下，输入 **InputDataset**。 

    b. 至于“链接服务”，请选择“AzureStorageLinkedService”。 

    c. 对于“文件路径”，请选择“浏览”按钮。 

    d. 在“选择文件或文件夹”窗口中浏览到 **adftutorial** 容器中的 **input** 文件夹，选择 **emp.txt** 文件，然后选择“确定”。 
    
    e. 选择“确定”。   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png" alt-text="设置 InputDataset 的属性。":::

1. 重复创建输出数据集的步骤：  

    a. 选择“+ (加)”按钮，然后选择“数据集”。 

    b. 在“新建数据集”页中，选择“Azure Blob 存储”，然后选择“继续”。  

    c. 在“选择格式”页上选择数据的格式类型，然后选择“继续”。 

    d. 在“设置属性”页上，指定 **OutputDataset** 作为名称。 选择 **AzureStorageLinkedService** 作为链接服务。

    e. 在“文件路径”下，输入 **adftutorial/output**。 如果 **output** 文件夹不存在，复制活动会在运行时创建它。

    f. 选择“确定”。   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png" alt-text="设置 OutputDataset 的属性。":::    

## <a name="create-a-pipeline"></a>创建管道 
此过程创建和验证一个管道，其中包含的复制活动可使用输入和输出数据集。 复制活动将数据从输入数据集设置中指定的文件复制到输出数据集设置中指定的文件。 如果输入数据集只指定了一个文件夹（不是文件名），则复制活动会将源文件夹中的所有文件复制到目标。 

1. 选择“+ (加)”按钮，然后选择“管道”。  

1. 在“常规”面板的“属性”中，将名称指定为 CopyPipeline  。 然后单击右上角的“属性”图标，折叠面板。

1. 在“活动”工具箱中，展开“移动和转换”。 将“复制数据”活动从“活动”工具箱拖到管道设计器图面。  也可在“活动”工具箱中搜索活动。 指定 **CopyFromBlobToBlob** 作为 **名称**。

    :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-activity.png" alt-text="创建复制数据活动。":::   

1. 切换到复制活动设置中的“源”选项卡，选择 **InputDataset** 作为 **源数据集**。

1. 切换到复制活动设置中的“接收器”选项卡，选择 **OutputDataset** 作为 **接收器数据集**。

1. 在画布上面的管道工具栏中单击“验证”，以便验证管道设置。 确认已成功验证管道。 若要关闭验证输出，请选择右上角的“验证”按钮。 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-validate.png"   alt-text="验证管道。":::

## <a name="debug-the-pipeline"></a>调试管道
此步骤对管道进行调试，然后再将其部署到数据工厂。 

1. 在画布上面的管道工具栏中单击“调试”，以便触发测试性运行。 
    
1. 确认可以在底部的管道设置的“输出”选项卡中看到管道运行的状态。 
 
    :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-output.png" alt-text="管道运行输出":::

1. 确认可以在 **adftutorial** 容器的 **output** 文件夹中看到输出文件。 如果 output 文件夹不存在，数据工厂服务会自动创建它。 

## <a name="trigger-the-pipeline-manually"></a>手动触发管道
在此过程中，请将实体（链接服务、数据集、管道）部署到 Azure 数据工厂， 然后手动触发管道运行。 

1. 在触发管道之前，必须将实体发布到数据工厂。 若要发布，请选择顶部的“全部发布”。 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/publish-all.png" alt-text="全部发布。":::    

1. 若要手动触发管道，请选择管道工具栏中的“添加触发器”，然后选择“立即触发”。  在“管道运行”页上，选择“确定” 。

## <a name="monitor-the-pipeline"></a>监视管道

1. 在左侧切换到“监视”选项卡。 使用“刷新”按钮刷新列表。

    :::image type="content" source="./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png" alt-text="用于监视管道运行的选项卡"::: 
1. 选择“CopyPipeline”链接，此时会在此页中看到复制活动运行的状态。 

1. 若要查看复制操作的详细信息，请选择“详细信息”（眼镜图标）链接。 有关属性的详细信息，请参阅[复制活动概述](copy-activity-overview.md)。 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-operation-details.png" alt-text="复制操作详细信息。":::
1. 确认可以在 **output** 文件夹中看到新文件。 
1. 可以选择“所有管道运行”链接，从“活动运行”视图切换回到“管道运行”视图。   

## <a name="trigger-the-pipeline-on-a-schedule"></a>按计划触发管道
在本教程中，此过程为可选过程。 可以创建 *计划程序触发器*，将管道计划为定期运行（每小时运行一次、每天运行一次，等等）。 此过程创建一个触发器。该触发器每分钟运行一次，直至指定的结束日期和时间。 

1. 切换到“创作”选项卡。 

1. 转到管道，选择管道工具栏中的“添加触发器”，然后选择“新建/编辑”。  

1. 在“添加触发器”页中选择“选择触发器”，然后选择“新建”。   

1. 在“新建触发器”页上的“结束”下，选择“在日期”，指定一个比当前时间晚数分钟的结束时间，然后选择“确定”。    

   每次管道运行都需要支付相关成本，因此请指定适当的结束时间，使之仅比开始时间晚数分钟。 确保两个时间是在同一天。 但是，请确保在发布时间和结束时间之间有足够的时间来运行管道。 只有在将解决方案发布到数据工厂之后，触发器才会生效，而不是在 UI 中保存触发器就会使该触发器生效。 

1. 在“新建触发器”页上选中“已激活”复选框，然后选择“确定”。   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/trigger-settings-next.png" alt-text="“新建触发器”设置。":::   
1. 查看警告消息，然后选择“确定”。

1. 选择“全部发布”，将所做的更改发布到数据工厂。 

1. 在左侧切换到“监视”选项卡。 选择“刷新”可刷新列表。 从发布时间到结束时间这段时间内，可以看到管道每分钟运行一次。 

   请注意“触发者”列中的值。 手动触发器运行是在此前执行的步骤（“立即触发”）中完成的。 

1. 切换到“触发器运行”视图。 

1. 确认每次管道运行时，在 **output** 文件夹中都创建了输出文件，直至指定的结束日期和时间为止。 

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 若要了解如何在更多方案中使用数据工厂，请完成相关[教程](tutorial-copy-data-portal.md)。
