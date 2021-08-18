---
title: 快速入门：使用 Apache Spark 作业定义转换数据
description: 本教程提供有关使用 Azure Synapse Analytics 通过 Apache Spark 作业定义转换数据的分步说明。
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2021
ms.openlocfilehash: 2b7392af7a00ec68ad263b7c930842714c94fe1d
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112967703"
---
# <a name="quickstart-transform-data-using-apache-spark-job-definition"></a>快速入门：使用 Apache Spark 作业定义转换数据。

在本快速入门中，你将使用 Azure Synapse Analytics 通过 Apache Spark 作业定义创建管道。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* Azure Synapse 工作区：按照[快速入门：创建 Synapse 工作区](quickstart-create-workspace.md)中的说明，使用 Azure 门户创建 Synapse 工作区。
* **Apache Spark 作业定义**：按照 [教程：在 Synapse Studio 中创建 Apache Spark 作业定义](spark/apache-spark-job-definitions.md)中的说明操作，在 Synapse 工作区中创建 Apache Spark 作业定义。


### <a name="navigate-to-the-synapse-studio"></a>导航到 Synapse Studio

创建 Azure Synapse 工作区后，可以通过两种方式打开 Synapse Studio：

* 在 [Azure 门户](https://ms.portal.azure.com/#home)中打开 Synapse 工作区。 在“开始”下的“打开 Synapse Studio”卡上选择“打开”。
* 打开 [Azure Synapse Analytics](https://web.azuresynapse.net/) 并登录到工作区。

在本快速入门中，我们将以名为“sampletest”的工作区为例。 它将自动导航到 Synapse Studio 主页。

![Synapse Studio 主页](media/quickstart-transform-data-using-spark-job-definition/synapse-studio-home.png)

## <a name="create-a-pipeline-with-an-apache-spark-job-definition"></a>使用 Apache Spark 作业定义创建管道

管道包含用于执行一组活动的逻辑流。 在本部分中，你将创建一个包含 Apache Spark 作业定义活动的管道。

1. 转到“集成”选项卡。选择管道标题旁边的加号图标，然后选择“管道” 。

     ![创建新管道](media/doc-common-process/new-pipeline.png)

2. 在管道的“属性”设置页中，输入 demo 作为管道的名称  。

3. 在“活动”窗格中的“Synapse”下，将“Spark 作业定义”拖到管道画布上 。

     ![拖动作业定义](media/quickstart-transform-data-using-spark-job-definition/drag-spark-job-definition.png)


## <a name="set-apache-spark-job-definition-canvas"></a>设置 Apache Spark 作业定义画布

创建 Apache Spark 作业定义后，该作业会自动发送到 Spark 作业定义画布。

### <a name="general-settings"></a>常规设置

1. 在画布上选择 Spark 作业定义模块。

2. 在“常规”选项卡中，输入 sample 作为名称  。

3. （选项）也可以输入说明。

4. 超时：活动可以运行的最长时间。 默认值为 7 天，这也是允许的最长时间。 格式为 D.HH:MM:SS。

5. 重试：最大重试尝试次数。

6. 重试间隔：两次重试尝试之间的时间，以秒为单位。

7. 安全输出：选中后，将不会在日志记录中捕获活动的输出。

8. 安全输入：选中后，将不会在日志记录中捕获活动的输入。

     ![常规 Spark 作业定义](media/quickstart-transform-data-using-spark-job-definition/spark-job-definition-general.png)

### <a name="settings-tab"></a>“设置”选项卡 

在此面板上，可以引用要运行的 Spark 作业定义。

* 展开 Spark 作业定义列表，可以选择现有 Apache Spark 作业定义。 也可通过选择“新建”按钮创建新的 Apache Spark 作业定义，引用要运行的 Spark 作业定义。

* 可以通过单击“新建”按钮来添加命令行参数。 应当注意的是，添加命令行参数将替代由 Spark 作业定义定义的命令行参数。 <br> *示例：`abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result`* <br>

     ![Spark 作业定义管道设置](media/quickstart-transform-data-using-spark-job-definition/spark-job-definition-pipline-settings.png)

* 可以通过单击“添加动态内容”按钮或按快捷键 <kbd>Alt</kbd>+<kbd>Shift</kbd>+<kbd>D</kbd> 来添加动态内容。 在“添加动态内容”页面，可以使用表达式、函数和系统变量的任意组合来添加动态内容。

     ![添加动态内容](media/quickstart-transform-data-using-spark-job-definition/add-dynamic-content.png)

### <a name="user-properties-tab"></a>“用户属性”选项卡

可以在此面板中为 Apache Spark 作业定义活动添加属性。

![用户属性](media/quickstart-transform-data-using-spark-job-definition/user-properties.png)

## <a name="next-steps"></a>后续步骤

请转至下列文章，了解 Azure Synapse Analytics 支持：

> [!div class="nextstepaction"]
> [管道和活动](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [映射数据流概述](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [数据流表达式语言](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
