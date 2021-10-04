---
title: 基于时间分区文件名增量复制新文件
description: 创建一个 Azure 数据工厂，然后使用复制数据工具仅根据时间分区文件名以增量方式加载新文件。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2021
ms.openlocfilehash: f2fe1e854027cb6fd15b6d0e2e659cc62528a2e2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757850"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>使用复制数据工具仅根据时间分区文件名以增量方式复制新文件

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在本教程中，我们将使用 Azure 门户创建数据工厂。 然后，使用复制数据工具创建一个管道，该管道根据时间分区文件名以增量方式将新文件从 Azure Blob 存储复制到 Azure Blob 存储。

> [!NOTE]
> 如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

将在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 使用“复制数据”工具创建管道。
> * 监视管道和活动运行。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**：将 Blob 存储用作源  和接收器  数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-account-create.md)中的说明。

### <a name="create-two-containers-in-blob-storage"></a>在 Blob 存储中创建两个容器

执行以下步骤，准备本教程所需的 Blob 存储。

1. 创建名为 **source** 的容器。  在容器中将文件夹路径创建为“2021/07/15/06”。 创建空的文本文件，并将其命名为 **file1.txt**。 将 file1.txt 上传到存储帐户中的文件夹路径“source/2021/07/15/06”。  可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png" alt-text="上传文件":::

    > [!NOTE]
    > 请根据 UTC 时间调整文件夹名称。  例如，如果当前的 UTC 时间为 2021 年 7 月 15 日清晨 6:10，则可按规则“source/{年}/{月}/{日}/{小时}/”创建文件夹路径“source/2021/07/15/06/” 。

2. 创建名为 **destination** 的容器。 可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“创建资源” > “集成” > “数据工厂”  ：

   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="在“新建”窗格中选择“数据工厂”":::&quot;&quot;

2. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory** 。

    数据工厂的名称必须全局唯一。 可能会收到以下错误消息：

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="针对重复名称的新的数据工厂错误消息。":::

   如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 例如，使用名称 _**yourname**_**ADFTutorialDataFactory**。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
3. 选择要在其中创建新数据工厂的 Azure **订阅**。
4. 对于“资源组”，请执行以下步骤之一： 

    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 

    b. 选择“新建”，并输入资源组的名称。 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。

5. 在“版本”下选择“V2”作为版本。 
6. 在“位置”下选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算资源（例如，Azure HDInsight）可以位于其他位置和区域。
7. 选择“创建”  。
8. 创建完以后，会显示“数据工厂”  主页。
9. 若要在单独的标签页中启动 Azure 数据工厂用户界面 (UI)，请在“打开 Azure 数据工厂工作室”磁贴上选择“打开” 。

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure 数据工厂主页，其中包含“打开 Azure 数据工厂工作室”磁贴。":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>使用“复制数据”工具创建管道

1. 在 Azure 数据工厂主页上，选择“引入”磁贴来启动“复制数据”工具。

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="显示 ADF 主页的屏幕截图。":::

2. 在“属性”页上执行以下步骤： 
    1. 在“任务类型”下，选择“内置复制任务” 。

    1. 在“任务节奏或任务计划”下，选择“翻滚窗口”。 

    1. 在“重复周期”下输入“1 小时”。  

    1. 选择“**下一页**”。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png" alt-text="“属性”页":::
3. 在“源数据存储”  页上，完成以下步骤：

    a. 选择“+ 新建连接”添加一个连接。
    
    b. 从库中选择“Azure Blob 存储” ，然后选择“继续”。
    
    c. 在“新建连接（Azure Blob 存储）”页上，输入连接的名称。 选择你的 Azure 订阅，从“存储帐户名称”列表中选择你的存储帐户。  测试连接，然后选择“创建”。 

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-connection.png" alt-text="“源数据存储”页":::

    d. 在“源数据存储”上的“连接”部分中选择新创建的连接 。

    e. 在“文件或文件夹”部分中，浏览并选择源容器，然后选择“确定”。  

    f. 在“文件加载行为”下选择“增量加载: 时间分区文件夹/文件名”。  

    g. 将动态文件夹路径编写为“source/{年}/{月}/{日}/{小时}/”，并按下面的屏幕截图所示更改格式。 
    
    h. 勾选“二进制副本”，然后选择“下一步”。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png" alt-text="显示“源数据存储”页配置的屏幕截图。":::


4. 在“目标数据存储”页上，完成以下步骤：
    1. 选择 AzureBlobStorage，这是与数据源存储相同的存储帐户。

    1. 浏览并选择目标文件夹，然后选择“确定”。 

    1. 将动态文件夹路径编写为“destination/{年}/{月}/{日}/{小时}/”，并按下面的屏幕截图所示更改格式。

    1. 选择“**下一页**”。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store.png" alt-text="显示“目标数据存储”页配置的屏幕截图。":::

5. 在“设置”页上的“任务名称”下，输入 DeltaCopyFromBlobPipeline，然后选择“下一步”。    数据工厂 UI 将使用指定的任务名称创建一个管道。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png" alt-text="显示设置页的配置的屏幕截图。":::

6. 在“摘要”  页中检查设置，然后选择“下一步”  。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png" alt-text="摘要页面":::

7. 在“部署”页中，选择“监视”以监视管道（任务） 。
    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png" alt-text="“部署”页":::

8. 请注意，界面中已自动选择左侧的“监视”选项卡。   在自动触发管道后，需等待管道运行（约等待一小时）。 在它运行时，选择管道名称链接“DeltaCopyFromBlobPipeline”，以查看活动运行详细信息或重新运行管道。 选择“刷新”可刷新列表。 

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png" alt-text="屏幕截图显示了“管道运行”窗格。":::
9. 该管道只包含一个活动（复制活动），因此只显示了一个条目。 调整“源”和“目标”列的列宽（如果需要）以显示更多详细信息，可以看到源文件 (file1.txt) 已从 *source/2021/07/15/06/* 复制到 *destination/2021/07/15/06/* ，文件名相同。 

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png" alt-text="屏幕截图显示了管道运行详细信息。":::

    也可使用 Azure 存储资源管理器进行相同的验证 (https://storageexplorer.com/) 以扫描这些文件。

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png" alt-text="屏幕截图显示了目标的管道运行详细信息。":::

10. 将另一个使用新名称的空文本文件创建为 **file2.txt**。 将 file2.txt 上传到存储帐户中的文件夹路径“source/2021/07/15/07”。 可以使用各种工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)）来执行这些任务。

    > [!NOTE]
    > 你可能知道，必须创建新的文件夹路径。 请根据 UTC 时间调整文件夹名称。  例如，如果当前 UTC 时间是 2021 年 7 月 15 日清晨 7:30， 可以按照 **{年}/{月}/{日}/{小时}/** 规则，将文件夹路径创建为“source/2021/07/15/07/”。

11. 若要回到“管道运行”视图，请选择“所有管道运行”，然后等待同一管道在一小时后再次自动触发 。  

    :::image type="content" source="./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png" alt-text="屏幕截图显示了用于返回到该页的“所有管道运行”链接。":::

12. 当第二个管道运行到来时，选择新的“DeltaCopyFromBlobPipeline”链接，然后执行相同的操作以查看详细信息。  你将看到，源文件 (file2.txt) 从 **source/2021/07/15/07/** 复制到了 **destination/2021/07/15/07/** ，且文件名相同。 也可在 destination  容器中使用 Azure 存储资源管理器进行相同的验证 (https://storageexplorer.com/) 以扫描这些文件。


## <a name="next-steps"></a>后续步骤
请转到下一篇教程，了解如何在 Azure 上使用 Spark 群集转换数据：

> [!div class="nextstepaction"]
>[在云中使用 Spark 群集转换数据](tutorial-transform-data-spark-portal.md)
