---
title: ML 工作室（经典）：迁移到 Azure 机器学习 - 重新生成数据集
description: 在 Azure 机器学习设计器中重新生成工作室（经典）数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: f6ad2a2120d998a89ebb59fc6f70779487f7c275
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429502"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>将工作室（经典）数据集迁移到 Azure 机器学习

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

本文介绍了如何将工作室（经典）数据集迁移到 Azure 机器学习。 有关从工作室（经典）迁移的详细信息，请参阅[迁移概述一文](migrate-overview.md)。

可通过三个选项将数据集迁移到 Azure 机器学习。 阅读每个部分以确定最适合你方案的选项。


|数据在哪个哪里？ | 迁移选项  |
|---------|---------|
|在工作室（经典）中     |  选项 1：[从工作室（经典）下载数据集，并将其上传到 Azure 机器学习](#download-the-dataset-from-studio-classic)。      |
|云存储     | 选项 2：[从云源注册数据集](#import-data-from-cloud-sources)。 <br><br>  选项 3：[使用“导入数据”模块从云源中获取数据](#import-data-from-cloud-sources)。        |

> [!NOTE]
> Azure 机器学习还支持[代码优先工作集](how-to-create-register-datasets.md)，用于创建和迁移数据集。 

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure 机器学习工作区。 [创建 Azure 机器学习工作区](how-to-manage-workspace.md#create-a-workspace)。
- 要迁移的工作室（经典）数据集。


## <a name="download-the-dataset-from-studio-classic"></a>从工作室（经典）下载数据集

将工作室（经典）数据集迁移到 Azure 机器学习的最简单方法是下载数据集，并将其注册到 Azure 机器学习中。 这会创建数据集的新副本，并将其上传到 Azure 机器学习数据存储。

可以直接下载以下工作室（经典）数据集类型。

* 纯文本 (.txt)
* 逗号分隔值 (CSV)，带有标头 (.csv) 或不带标头 (.nh.csv)
* 制表符分隔值 (TSV)，带有标头 (.tsv) 或不带标头 (.nh.tsv)
* Excel 文件
* Zip 文件 (.zip)

要直接下载数据集，请执行以下操作：
1. 转到工作室（经典）工作区 ([https://studio.azureml.net](https://studio.azureml.net))。
1. 在左侧导航栏中，选择“数据集”选项卡。
1. 选择要下载的数据集。
1. 在底部操作栏中，选择“下载”。

    ![屏幕截图，显示如何在工作室（经典）中下载数据集](./media/migrate-register-dataset/download-dataset.png)

对于以下数据类型，必须使用“转换为 CSV”模块来下载数据集。

* SVMLight 数据 (.svmlight) 
* 属性关系文件格式 (ARFF) 数据 (.arff) 
* R 对象或工作区文件 (.RData)
* 数据集类型 (.data)。 数据集类型为工作室（经典）模块输出的内部数据类型。

若要将数据集转换为 CSV 并下载结果，请执行以下操作：

1. 转到工作室（经典）工作区 ([https://studio.azureml.net](https://studio.azureml.net))。
1. 创建新实验。
1. 将要下载的数据集拖放到画布上。
1. 添加“转换为 CSV’模块。
1. 将“转换为 CSV”输入端口连接到数据集的输出端口。
1. 运行试验。
1. 右键单击“转换为 CSV”模块。
1. 选择“结果数据集” > “下载”。

    ![屏幕截图，显示如何设置转换为 CSV 管道](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>将数据集上传到 Azure 机器学习

下载数据文件后，可以在 Azure 机器学习中注册数据集：

1. 转到 Azure 机器学习工作室 ([ml.azure.com](https://ml.azure.com))。
1. 在左侧导航窗格中，选择“数据集”选项卡。
1. 选择“创建数据集” > “从本地文件”。
    ![屏幕截图，显示数据集选项卡和创建本地文件的按钮](./media/migrate-register-dataset/register-dataset.png)
1. 输入名称和说明。
1. 对于“数据集类型”，选择“表格”。

    > [!NOTE]
    > 还可以上传 ZIP 文件作为数据集。 若要上传 ZIP 文件，请选择“文件”作为“数据集类型”。

1. 对于数据存储和文件选择，选择要将数据集文件上传到的数据存储。

    默认情况下，Azure 机器学习将数据集存储到默认工作区 blobstore。 有关数据存储的详细信息，请参阅[连接到存储服务](how-to-access-data.md)。

1. 为数据集设置数据解析设置和架构。 然后确认设置。

## <a name="import-data-from-cloud-sources"></a>从云源导入数据

如果数据已在云存储服务中，并且你想要将数据保存在其本机位置。 可以使用两个选项中的一个：

|引入方法|说明|
|---| --- |
|注册 Azure 机器学习数据集|从本地和联机数据源（Blob、ADLS Gen1、ADLS Gen2、文件共享、SQL DB）中引入数据。 <br><br>创建对数据源的引用，该数据源在运行时延迟计算。 如果重复访问此数据集，并希望启用数据版本控制和监视等高级数据功能，请使用此选项。
|“导入数据”模块|从联机数据源（Blob、ADLS Gen1、ADLS Gen2、文件共享、SQL DB）中引入数据。 <br><br> 数据集仅导入到当前设计器管道运行中。


>[!Note]
> 工作室（经典）用户应注意，在 Azure 机器学习中本机不支持以下云源：
> - Hive 查询
> - Azure 表
> - Azure Cosmos DB
> - 本地 SQL 数据库
>
> 建议用户使用 Azure 数据工厂将其数据迁移到支持的存储服务。  

### <a name="register-an-azure-machine-learning-dataset"></a>注册 Azure 机器学习数据集

使用以下步骤将数据集从云服务注册到 Azure 机器学习： 

1. [创建一个数据存储](how-to-connect-data-ui.md#create-datastores)，用于将云存储服务链接到 Azure 机器学习工作区。 

1. [注册数据集](how-to-connect-data-ui.md#create-datasets)。 如果要迁移工作室（经典）数据集，请选择“表格”数据集设置。

在 Azure 机器学习中注册数据集后，可以在设计器中使用它：
 
1. 创建新的设计器管道草稿。
1. 在左侧模块面板中，展开“数据集”部分。
1. 将已注册的数据集拖动到画布上。 

### <a name="use-the-import-data-module"></a>使用“导入数据”模块

使用以下步骤将数据直接导入设计器管道：

1. [创建一个数据存储](how-to-connect-data-ui.md#create-datastores)，用于将云存储服务链接到 Azure 机器学习工作区。 

创建数据存储后，可以使用设计器中的[“导入数据”](algorithm-module-reference/import-data.md)模块来从中引入数据：

1. 创建新的设计器管道草稿。
1. 在左侧模块面板中，找到“导入数据”模块并将其拖动到画布上。
1. 选择“导入数据”模块，并使用右侧面板中的“设置”来配置数据源。

## <a name="next-steps"></a>后续步骤

本文介绍了如何将工作室（经典）数据集迁移到 Azure 机器学习。 下一步是[重新生成工作室（经典）训练管道](migrate-rebuild-experiment.md)。


请参阅工作室（经典）迁移系列中的其他文章：

1. [迁移概述](migrate-overview.md)。
1. **迁移数据集**。
1. [重新生成工作室（经典）训练管道](migrate-rebuild-experiment.md)。
1. [重新生成工作室（经典）Web 服务](migrate-rebuild-web-service.md)。
1. [将 Azure 机器学习 Web 服务与客户端应用集成](migrate-rebuild-integrate-with-client-app.md)。
1. [迁移执行 R 脚本](migrate-execute-r-script.md)。
