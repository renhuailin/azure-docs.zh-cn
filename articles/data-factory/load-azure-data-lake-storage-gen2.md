---
title: 将数据加载到 Azure Data Lake Storage Gen2 中
description: 使用 Azure 数据工厂将数据复制到 Azure Data Lake Storage Gen2 中
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/22/2021
ms.openlocfilehash: edc3005a462777ce7cf873b4098a17af215e308b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637863"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>使用 Azure 数据工厂将数据加载到 Azure Data Lake Storage Gen2 中

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 是一组专用于大数据分析的功能，内置于 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中。 它可使用文件系统和对象存储范例与数据进行交互。

Azure 数据工厂 (ADF) 是一个完全托管的基于云的数据集成服务。 通过该服务，可使用丰富的本地数据存储和基于云的数据存储中的数据填充数据湖，并更快速地生成分析解决方案。 若要查看受支持的连接器的详细列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供可横向扩展的托管数据移动解决方案。 得益于 ADF 的横向扩展体系结构，它能以较高的吞吐量引入数据。 有关详细信息，请参阅[复制活动性能](copy-activity-performance.md)。

本文介绍如何使用数据工厂复制数据工具将数据从 Amazon Web Services S3 服务加载至 Azure Data Lake Storage Gen2 。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

>[!TIP]
>要将 Azure Data Lake Storage Gen1 中的数据复制到 Gen2，请参阅[此特定演练](load-azure-data-lake-storage-gen2-from-gen1.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Data Lake Storage Gen2 的 Azure 存储帐户：如果没有存储帐户，请[创建一个帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。
* AWS 帐户与一个包含数据的 S3 存储桶：本文介绍如何从 Amazon S3 复制数据。 可以按类似步骤使用其他数据存储。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中，选择“创建资源” > “集成” > “数据工厂”  ：
   
   ![在“新建”窗格中选择“数据工厂”](./media/doc-common-process/new-azure-data-factory-menu.png)

2. 在“新建数据工厂”页中，为以下字段提供值：
 
    * **名称**：输入 Azure 数据工厂的全局唯一名称。 如果收到错误“数据工厂名称 YourDataFactoryName 不可用”，请为数据工厂输入其他名称。 例如，可以使用名称 _**yourname**_**ADFTutorialDataFactory**。 请重试创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有资源组，或选择“新建”选项并输入资源组的名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。  
    * **版本**：选择“V2”。
    * **位置**：选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 

3. 选择“创建” 。

4. 创建操作完成后，请转到数据工厂。 此时会看到“数据工厂”主页，如下图所示： 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure 数据工厂主页，其中包含“打开 Azure 数据工厂工作室”磁贴。":::

   在“打开 Azure 数据工厂工作室”磁贴上选择“打开”，以便在单独的选项卡中启动“数据集成应用程序”。 

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>将数据加载到 Azure Data Lake Storage Gen2 中

1. 在 Azure 数据工厂的主页中，选择“引入”磁贴来启动“复制数据”工具。

2. 在“属性”页中，在“任务类型”下选择“内置复制任务”，在“任务节奏或任务计划”下选择“现在运行一次”，然后选择“下一页”。

    ![“属性”页](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. 在“源数据存储”页上，完成以下步骤：
    1. 选择“+ 新建连接”。 从连接器库中选择“Amazon S3”，然后选择“继续” 。
    
        ![“源数据存储 S3”页](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
    1. 在“新建连接 (Amazon S3)”页中，执行以下步骤：

        1. 指定“访问密钥 ID”值。
        1. 指定“机密访问密钥”值。
        1. 选择“测试连接”以验证设置，然后选择“创建” 。
    
          ![指定 Amazon S3 帐户](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)

    1. 在“源数据存储”页上，确保新创建的 Amazon S3 连接在“连接”块中处于选中状态。 
    1. 在“文件或文件夹”部分中，浏览到要复制的文件夹和文件。 选中文件夹/文件，然后选择“确定”。
    1. 通过选中“以递归方式”和“以二进制方式复制”选项，指定复制行为 。 选择“**下一步**”。

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2/source-data-store.png" alt-text="显示“源数据存储”页的屏幕截图。":::
    
4. 在“目标数据存储”页上，完成以下步骤。
    1. 选择“+ 新建连接”，然后选择“Azure Data Lake Storage Gen2”，再选择“继续”  。

        ![“目标数据存储”页](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)
    
    1. 在“新建连接 (Azure Data Lake Storage Gen2)”页上，从“存储帐户名称”下拉列表中选择你的支持 Data Lake Storage Gen2 的帐户，然后选择“创建”以创建连接。  

        ![指定 Azure Data Lake Storage Gen2 帐户](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

    1. 在“目标数据存储”上的“连接”块中选择新创建的连接 。 然后在“文件夹路径”下，输入“copyfroms3”作为输出文件夹名称，然后选择“下一步”。 ADF 将在复制过程中创建相应的 ADLS Gen2 文件系统和子文件夹（如果不存在）。

        :::image type="content" source="./media/load-azure-data-lake-storage-gen2/destiantion-data-store.png" alt-text="显示“目标数据存储”页的屏幕截图。":::   
    
5. 在“设置”页中，为“任务名称”字段指定“CopyFromAmazonS3ToADLS”，然后选择“下一步”，以使用默认设置。

    ![“设置”页](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

6. 在“摘要”页中检查设置，然后选择“下一步”。

    ![“摘要”页](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

7. 在“部署”页中，选择“监视”可以监视管道（任务） 。 
 
8. 管道运行成功完成后，你会看到由手动触发器触发的管道运行。 可以使用“管道名称”列下的链接来查看活动详细信息以及重新运行该管道。

    ![监视管道运行](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

9. 若要查看与管道运行关联的活动运行，请选择“管道名称”列下的“CopyFromAmazonS3ToADLS”链接。  有关复制操作的详细信息，请选择“活动名称”列下的“详细信息”链接（眼镜图标） 。 可以监视详细信息，例如，从源复制到接收器的数据量、数据吞吐量、执行步骤以及相应的持续时间和使用的配置。
 
    ![监视活动运行](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![监视活动运行详细信息](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

10. 若要刷新视图，请选择“刷新”。 选择顶部的“所有管道运行”，回到“管道运行”视图。

11. 验证数据是否已复制到 Data Lake Storage Gen2 帐户。

## <a name="next-steps"></a>后续步骤

* [复制活动概述](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2 连接器](connector-azure-data-lake-storage.md)
