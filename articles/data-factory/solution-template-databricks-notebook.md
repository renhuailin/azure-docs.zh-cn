---
title: 使用 Azure Databricks 进行转换
description: 了解如何使用解决方案模板，通过在 Azure 数据工厂中使用 Databricks 笔记本转换数据。
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: dc01ba10922901e5c3558e26b0f2ab2718ea34e3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743602"
---
# <a name="transformation-with-azure-databricks"></a>使用 Azure Databricks 进行转换

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教程中，你将在数据工厂中创建包含“验证”、“复制数据”和“笔记本”活动的端到端管道。  

- “验证”确保你的源数据集在你触发复制和分析作业之前已准备好供下游消耗。

- “复制数据”将源数据集复制到接收器存储，该存储在 Azure Databricks 笔记本中装载为 DBFS。 这样，Spark 就可以直接使用该数据集。

- “笔记本”触发对数据集进行转换的 Databricks 笔记本。 它还将数据集添加到已处理的文件夹或 Azure Synapse Analytics。

为简单起见，本教程中的模板没有创建计划的触发器。 如有必要，你可以添加计划的触发器。

:::image type="content" source="media/solution-template-Databricks-notebook/pipeline-example.png" alt-text="管道的图示":::

## <a name="prerequisites"></a>先决条件

- 一个 Azure Blob 存储帐户，其中包含用作接收器的名为 `sinkdata` 的容器。

  记下存储帐户名称、容器名称和访问密钥。 稍后在模板中需要使用这些值。

- 一个 Azure Databricks 工作区。

## <a name="import-a-notebook-for-transformation"></a>导入用于转换的笔记本

若要将 **转换** 笔记本导入到 Databricks 工作区，请执行以下操作：

1. 登录到你的 Azure Databricks 工作区，然后选择“导入”。
       :::image type="content" source="media/solution-template-Databricks-notebook/import-notebook.png" alt-text="用于导入工作区的菜单命令":::
   你的工作区路径可能不同于所示的路径，但请记住它备用。
1. 选择“导入自:URL”。 在文本框中，输入 `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`。

   :::image type="content" source="media/solution-template-Databricks-notebook/import-from-url.png" alt-text="用于导入笔记本的选择":::

1. 现在，使用你的存储连接信息更新“转换”笔记本。

   在导入的笔记本中，转到 **command 5**，如下面的代码片段所示。

   - 将 `<storage name>` 和 `<access key>` 替换为你自己的存储连接信息。
   - 使用具有 `sinkdata` 容器的存储帐户。

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. 为数据工厂生成“Databricks 访问令牌”以访问 Databricks。
   1. 在你的 Databricks 工作区中，选择右上角的用户配置文件图标。
   1. 选择“用户设置”。
    :::image type="content" source="media/solution-template-Databricks-notebook/user-setting.png" alt-text="用于用户设置的菜单命令":::
   1. 在“访问令牌”选项卡下选择“生成新令牌”。
   1. 然后选择“生成”  。

    :::image type="content" source="media/solution-template-Databricks-notebook/generate-new-token.png" alt-text="&quot;&quot;“生成”按钮":::

   保存访问令牌，以便稍后将其用于创建 Databricks 链接服务。 访问令牌类似于 `dapi32db32cbb4w6eee18b7d87e45exxxxxx`。

## <a name="how-to-use-this-template"></a>如何使用此模板

1. 转到“使用 Azure Databricks 进行转换”模板，为以下连接创建新的链接服务。

   :::image type="content" source="media/solution-template-Databricks-notebook/connections-preview.png" alt-text="连接设置":::

    - **源 Blob 连接** - 用于访问源数据。

       对于此练习，你可以使用包含源文件的公共 Blob 存储。 有关配置，请参考下面的屏幕截图。 使用以下 **SAS URL** 连接到源存储（只读访问）：

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        :::image type="content" source="media/solution-template-Databricks-notebook/source-blob-connection.png" alt-text="针对身份验证方法和 SAS URL 的选择":::

    - **目标 Blob 连接** - 用于存储复制的数据。

       在“新建链接服务”窗口中，选择你的接收器存储 blob。

       :::image type="content" source="media/solution-template-Databricks-notebook/destination-blob-connection.png" alt-text="将接收器存储 blob 用作新的链接服务":::

    - **Azure Databricks** - 用于连接到 Databricks 群集。

        使用之前生成的访问密钥创建 Databricks 链接服务。 你还可以选择一个“交互式群集”（如果有）。 此示例使用“新建作业群集”选项。

        :::image type="content" source="media/solution-template-Databricks-notebook/databricks-connection.png" alt-text="用于连接到群集的选择":::

1. 选择“使用此模板”  。 你将看到一个创建的管道。

    :::image type="content" source="media/solution-template-Databricks-notebook/new-pipeline.png" alt-text="创建管道":::

## <a name="pipeline-introduction-and-configuration"></a>管道简介和配置

在新管道中，大多数设置都自动配置为默认值。 请查看管道的配置，并进行任何必要的更改。

1. 在“验证”活动的“可用性”标志中，验证源“数据集”值是否设置为你之前创建的 `SourceAvailabilityDataset`。

   :::image type="content" source="media/solution-template-Databricks-notebook/validation-settings.png" alt-text="源数据集值":::

1. 在“复制数据”活动的“文件到 blob”中，检查“源”和“接收器”选项卡。 如有必要，请更改设置。

   - “源”选项卡 :::image type="content" source="media/solution-template-Databricks-notebook/copy-source-settings.png" alt-text="“源”选项卡":::

   - “接收器”选项卡 :::image type="content" source="media/solution-template-Databricks-notebook/copy-sink-settings.png" alt-text="“接收器”选项卡":::

1. 在“笔记本”活动的“转换”中，查看并根据需要更新路径和设置。

   “Databricks 链接服务”应当预先填充前面步骤中的值，如下所示：:::image type="content" source="media/solution-template-Databricks-notebook/notebook-activity.png" alt-text="Databricks 链接服务的填充值":::

   若要检查“笔记本”设置，请执行以下操作：
  
    1. 选择“设置”选项卡。对于“笔记本路径”，请验证默认路径是否正确。 你可能需要浏览并选择正确的笔记本路径。

       :::image type="content" source="media/solution-template-Databricks-notebook/notebook-settings.png" alt-text="笔记本路径":::

    1. 展开“基参数”选择器，验证参数是否与以下屏幕截图中显示的内容匹配。 这些参数将从数据工厂传递到 Databricks 笔记本。

       :::image type="content" source="media/solution-template-Databricks-notebook/base-parameters.png" alt-text="基参数":::

1. 验证“管道参数”是否与以下屏幕截图中显示的内容匹配：:::image type="content" source="media/solution-template-Databricks-notebook/pipeline-parameters.png" alt-text="管道参数":::

1. 连接到你的数据集。

    >[!NOTE]
    >在下面的数据集内，已在模板中自动指定了文件路径。 当出现任何连接错误时，如果需要进行任何更改，请确保同时为“容器”和“目录”指定路径。

   - **SourceAvailabilityDataset** - 用于检查源数据是否可用。

     :::image type="content" source="media/solution-template-Databricks-notebook/source-availability-dataset.png" alt-text="针对 SourceAvailabilityDataset 的链接服务和文件路径的选择":::

   - **SourceFilesDataset** - 用于访问源数据。

       :::image type="content" source="media/solution-template-Databricks-notebook/source-file-dataset.png" alt-text="针对 SourceFilesDataset 的链接服务和文件路径的选择":::

   - **DestinationFilesDataset** - 用于将数据复制到接收器目标位置。 使用以下值：

     - **链接服务** - `sinkBlob_LS`，在前面的步骤中创建。

     - **文件路径** - `sinkdata/staged_sink`。

       :::image type="content" source="media/solution-template-Databricks-notebook/destination-dataset.png" alt-text="针对 DestinationFilesDataset 的链接服务和文件路径的选择":::

1. 选择“调试”以运行管道。 可以找到 Databricks 日志的链接以获取更详细的 Spark 日志。

    :::image type="content" source="media/solution-template-Databricks-notebook/pipeline-run-output.png" alt-text="从输出链接到 Databricks 日志":::

    还可以使用 Azure 存储资源管理器验证数据文件。

    > [!NOTE]
    > 为了与数据工厂管道运行相关联，此示例将管道运行 ID 从数据工厂附加到输出文件夹。 这有助于跟踪每次运行生成的文件。
    > :::image type="content" source="media/solution-template-Databricks-notebook/verify-data-files.png" alt-text="追加的管道运行 ID":::

## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
