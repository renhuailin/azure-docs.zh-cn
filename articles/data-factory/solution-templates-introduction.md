---
title: 模板概述
description: 了解如何使用预定义的模板来快速开始使用 Azure 数据工厂。
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.author: susabat
author: ssabat
ms.custom: seo-lt-2019
ms.date: 08/24/2021
ms.openlocfilehash: 5d07ecedbc8fe4ff0670c9e8aa96fdf2a92359d3
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098944"
---
# <a name="templates"></a>模板

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

模板是预定义的 Azure 数据工厂管道，可让你快速开始使用数据工厂。 如果你是数据工厂的新手并想要快速入门，则模板很有帮助。 这些模板减少了生成数据集成项目所需的开发时间，从而提高了开发人员的工作效率。

## <a name="create-data-factory-pipelines-from-templates"></a>从模板创建数据工厂管道

可通过以下两种方式，开始从模板创建数据工厂管道：

1.  在数据工厂主页的“发现更多”部分选择“管道模板”，以打开模板库。 

    ![显示如何从“数据工厂”主页打开模板库的屏幕截图。](media/doc-common-process/home-page-pipeline-templates-tile.png)

1.  在资源浏览器中的“创作”选项卡上选择 +，然后选择“模板中的管道”打开模板库 。

    ![显示如何从“创建者”选项卡打开模板库的屏幕截图。](media/solution-templates-introduction/templates-introduction-image-2.png)

## <a name="template-gallery"></a>模板库

![显示“模板库”页面的屏幕截图。](media/solution-templates-introduction/templates-introduction-image-3.png)

### <a name="out-of-the-box-data-factory-templates"></a>现成的数据工厂模板

数据工厂使用 Azure 资源管理器模板来保存数据工厂管道模板。 在[官方 Azure 数据工厂 GitHub 存储库](https://github.com/Azure/Azure-DataFactory/tree/master/templates)中，可以看到所有资源管理器模板，以及现成数据工厂模板使用的清单文件。 Microsoft 提供的预定义模板包括但不限于以下各项：

-   复制模板：

    -   [从数据库进行批量复制](solution-template-bulk-copy-with-control-table.md)
    
    -   [按 LastModifiedDate 复制新文件](solution-template-copy-new-files-lastmodifieddate.md)

    -   [在基于文件的存储之间复制多个文件容器](solution-template-copy-files-multiple-containers.md)

    -   [移动文件](solution-template-move-files.md)

    -   [从数据库进行增量复制](solution-template-delta-copy-with-control-table.md)

    -   从 \<source\> 复制到 \<destination\>

        -   [从 Amazon S3 复制到 Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   从 Google Big Query 复制到 Azure Data Lake Store Gen 2

        -   从 HDF 复制到 Azure Data Lake Store Gen 2

        -   从 Netezza 复制到 Azure Data Lake Store Gen 1

        -   从本地 SQL Server 复制到 Azure SQL 数据库

        -   从本地 SQL Server 复制到 Azure Synapse Analytics

        -   从本地 Oracle 复制到 Azure Synapse Analytics

-   SSIS 模板

    -   将 Azure-SSIS Integration Runtime 计划为执行 SSIS 包

-   转换模板

    -   [使用 Azure Databricks 的 ETL](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>我的模板

还可以通过在“管道”选项卡上选择“另存为模板”，将管道另存为模板。

![显示如何将管道另存为模板的屏幕截图。](media/solution-templates-introduction/templates-introduction-image-4.png)

选中“模板库”页面中的“我的模板”框后，可以在此页右侧窗格中查看另存为模板的管道 。 

![显示“我的模板”窗格的屏幕截图。](media/solution-templates-introduction/templates-introduction-image-5.png)

> [!NOTE]
> 若要使用“我的模板”功能，必须启用 GIT 集成。 支持 Azure DevOps GIT 和 GitHub。
