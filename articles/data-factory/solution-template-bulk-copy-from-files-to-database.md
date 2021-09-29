---
title: 从文件向数据库进行批量复制
description: 了解如何使用解决方案模板将数据从 Azure Data Lake Storage Gen2 批量复制到 Azure Synapse Analytics/Azure SQL 数据库。
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 59189f0c197294ca74e01d331663c51fa0e2bd7d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757888"
---
# <a name="bulk-copy-from-files-to-database"></a>从文件向数据库进行批量复制

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍了一个解决方案模板，你可以使用该模板将数据从 Azure Data Lake Storage Gen2 批量复制到 Azure Synapse Analytics/Azure SQL 数据库。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板从 Azure Data Lake Storage Gen2 源检索文件。 然后，它会循环访问源中的每个文件，并将文件复制到目标数据存储。 

当前，此模板仅支持复制 DelimitedText 格式的数据。 也可以从源数据存储中检索其他数据格式的文件，但不能将这些文件复制到目标数据存储。  

该模板包含三个活动：
- **获取元数据** 活动可从 Azure Data Lake Storage Gen2 中检索文件，然后将其传递给后续的 ForEach 活动。
- **ForEach** 活动可从获取元数据活动获取文件，并以迭代方式将每个文件传递给复制活动。
- **复制** 活动位于 ForEach 活动中，可将源数据存储中的每个文件复制到目标数据存储。

此模板定义以下两个参数：
- *SourceContainer* 是从 Azure Data Lake Storage Gen2 中复制数据的根容器路径。 
- *SourceDirectory* 是从 Azure Data Lake Storage Gen2 中复制数据的根容器下的目录路径。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到“从文件向数据库进行批量复制”模板。 创建与源 Gen2 存储的新连接。 请注意，“GetMetadataDataset”和“SourceDataset”是对源文件存储的同一连接的引用。

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/source-connection.png" alt-text="创建与源数据存储的新连接":::

2. 创建与要将数据复制到其中的接收器数据存储的新连接。

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/destination-connection.png" alt-text="创建与接收器数据存储的新连接":::
    
3. 选择“使用此模板”  。

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/use-template.png" alt-text="使用此模板":::
    
4. 你会看到所创建的管道，如以下示例中所示：

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png" alt-text="查看管道":::

    > [!NOTE]
    > 如果选择 Azure Synapse Analytics 作为上述步骤 2 中的数据目标，必须按 Azure Synapse Analytics Polybase 的要求输入用于暂存的 Azure Blob 存储的连接 。 如以下屏幕截图所示，模板会自动为 Blob 存储生成存储路径。 检查是否在管道运行后创建了容器。
        
    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/staging-account.png" alt-text="Polybase 设置":::

5. 选择“调试”，输入 **参数**，然后选择“完成”。  

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/debug-run.png" alt-text="单击“调试”****":::

6. 管道运行成功完成后，你会看到类似于以下示例的结果：

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png" alt-text="查看结果":::

       
## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)