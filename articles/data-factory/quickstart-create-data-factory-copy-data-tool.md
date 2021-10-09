---
title: 使用 Azure 复制数据工具复制数据
description: 创建一个 Azure 数据工厂，然后使用“复制数据”工具将数据从 Azure Blob 存储中的一个位置复制到另一个位置。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: quickstart
ms.date: 07/05/2021
ms.openlocfilehash: 2bde0d03c901808e277044db035dc738bb555bb0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750359"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>快速入门：使用“复制数据”工具复制数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [当前版本](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本快速入门中，我们将使用 Azure 门户创建一个数据工厂。 然后，使用“复制数据”工具创建一个管道，用于将数据从 Azure Blob 存储中的某个文件夹复制到另一个文件夹。 

> [!NOTE]
> 如果你对 Azure 数据工厂不太熟悉，请在学习本快速入门之前参阅 [Azure 数据工厂简介](introduction.md)。 

[!INCLUDE [data-factory-quickstart-prerequisites](includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 转到 [Azure 门户](https://portal.azure.com)。 
1. 从 Azure 门户菜单，选择“创建资源” > “集成” > “数据工厂”  ：

    :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="新建数据工厂":::

1. 在“新建数据工厂”页中，输入 **ADFTutorialDataFactory** 作为 **名称**。 
 
   Azure 数据工厂的名称必须全局唯一。 如果出现以下错误，请更改数据工厂的名称（例如改为 **&lt;yourname&gt;ADFTutorialDataFactory**），并重新尝试创建。 有关数据工厂项目的命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="名称不可用时出错":::
1. 对于“订阅”，请选择要在其中创建数据工厂的 Azure 订阅。  
1. 对于“资源组”，请使用以下步骤之一：
     
   - 选择“使用现有”，并从列表中选择现有的资源组。 
   - 选择“新建”，并输入资源组的名称。   
         
   若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。  
1. 对于“版本”，选择“V2”。 
1. 对于“位置”，请选择数据工厂所在的位置。 

   该列表仅显示数据工厂支持的位置，以及 Azure 数据工厂元数据要存储到的位置。 数据工厂使用的关联数据存储（如 Azure 存储和 Azure SQL 数据库）和计算（如 Azure HDInsight）可以在其他区域中运行。

1. 选择“创建”  。

1. 创建完成后，会显示“数据工厂”页。 在“打开 Azure 数据工厂工作室”磁贴上选择“打开”，在单独的选项卡上启动 Azure 数据工厂用户界面 (UI) 应用程序。 
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure 数据工厂主页，其中包含“打开 Azure 数据工厂工作室”磁贴。":::
    
## <a name="start-the-copy-data-tool"></a>启动“复制数据”工具

1. 在 Azure 数据工厂的主页上，选择“引入”磁贴来启动“复制数据”工具。

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="屏幕截图显示 Azure 数据工厂主页。":::

1. 在“复制数据”工具的“属性”页上，选择“任务类型”下的“内置复制任务”，然后选择“下一步”   。

   :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png" alt-text="“属性”页面":::

1. 在“源数据存储”  页上，完成以下步骤：

    1. 单击“+ 创建新连接”，添加一个连接。

    1. 选择要创建的用于源连接的链接服务类型。 在本教程中，我们使用“Azure Blob 存储”。 从库中选择它，然后选择“继续”。
    
       :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png" alt-text="选择 Blob":::

    1. 在“新建连接(Azure Blob 存储)”页上，指定连接的名称。 从“Azure 订阅”列表中选择你的 Azure 订阅，从“存储帐户名称”列表中选择你的存储帐户，测试连接，然后选择“创建”  。 

       :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png" alt-text="配置 Azure Blob 存储帐户":::

    1. 在“连接”块中选择新建的连接。
    1. 在“文件或文件夹”部分，选择“浏览”导航到“adftutorial/input”文件夹，选择“emp.txt”文件，然后单击“确定”    。
    1. 选中“二进制复制”复选框以便按原样复制文件，然后选择“下一步”。

       :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/source-data-store.png" alt-text="显示“源数据存储”页的屏幕截图。":::

1. 在“目标数据存储”页上，完成以下步骤：
    1. 选择在“连接”块中创建的“AzureBlobStorage”连接 。

    1. 在“文件夹路径”部分，输入“adftutorial/output”作为文件夹路径 。

       :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/destination-data-store.png" alt-text="显示“目标数据存储”页的屏幕截图。":::

    1. 将其他设置保留默认值，然后选择“下一步”。

1. 在“设置”页上，指定管道的名称及其说明，然后选择“下一步”以使用其他默认配置 。 

    :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/settings.png" alt-text="显示“设置”页的屏幕截图。":::

1. 在“摘要”页中查看所有设置，然后选择“下一步”。 

1. 在“部署已完成”页中，选择“监视”可监视创建的管道。  

    :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png" alt-text="“部署完成”页面":::

1. 应用程序将切换到“监视”选项卡。可在此选项卡中查看管道的状态。选择“刷新”可刷新列表。 单击“管道名称”下的链接，查看活动运行详细信息或重新运行管道。 
   
    :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png" alt-text="刷新管道":::

1. 在“活动运行”页上，选择“活动名称”列下的“详细信息”链接（眼镜图标），以获取有关复制操作的更多详细信息 。 有关属性的详细信息，请参阅[复制活动概述](copy-activity-overview.md)。 

1. 若要返回到“管道运行”视图，请选择痕迹导航菜单中的“所有管道运行”链接。 若要刷新视图，请选择“刷新”。 

1. 验证 **adftutorial** 容器的 **output** 文件夹中是否创建了 **emp.txt** 文件。 如果 output 文件夹不存在，数据工厂服务会自动创建它。 

1. 切换到左面板的“监视”选项卡上的“创作”选项卡，以便编辑链接服务、数据集和管道。  若要了解如何在数据工厂 UI 中编辑这些实体，请参阅[使用 Azure 门户创建数据工厂](quickstart-create-data-factory-portal.md)。

    :::image type="content" source="./media/quickstart-create-data-factory-copy-data-tool/select-author.png" alt-text="选择“创作”选项卡":::

## <a name="next-steps"></a>后续步骤
此示例中的管道将数据从 Azure Blob 存储中的一个位置复制到另一个位置。 若要了解如何在更多方案中使用数据工厂，请完成相关[教程](tutorial-copy-data-portal.md)。 
