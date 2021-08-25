---
title: 管理适用于 Apache Spark 的 Python 库
description: 了解如何在 Azure Synapse Analytics 中添加和管理 Apache Spark 使用的 Python 库。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.custom: has-adal-ref
ms.openlocfilehash: 1b69aed711100eead6e7669c7d57e2f8faac25ad
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429181"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中管理适用于 Apache Spark 的 Python 库

这些库提供了可重用的代码，你可能想要在程序或项目中包含这些代码。 

由于各种原因，可能需要更新无服务器 Apache Spark 池环境。 例如，你可能发现：
- 某个核心依赖项刚刚发布了新版本。
- 你需要使用额外的包来训练机器学习模型或准备数据。
- 你找到了一个更好的包，并且不再需要较早的包。

为使第三方或本地生成的代码可用于自己的应用程序，可向其中一个无服务器 Apache Spark 池或笔记本会话中安装一个库。 本文介绍如何管理整个无服务器 Apache Spark 池中的 Python 库。

## <a name="default-installation"></a>默认安装
Azure Synapse Analytics 中的 Apache Spark 包含一整套用于常见数据工程、数据准备、机器学习和数据可视化任务的库。 你可在 [Apache Spark 版本支持](apache-spark-version-support.md)中找到完整的库列表。 

当 Spark 实例启动时，将自动包含这些库。 可在 Spark 池和会话级别添加额外的 Python 包与定制包。

## <a name="pool-libraries"></a>池库
确定要用于 Spark 应用程序的 Python 库后，可将其安装到 Spark 池中。 池级别的库可用于在池中运行的所有笔记本和作业。

可以通过两种主要方式在群集上安装库：
-  安装已作为工作区包上传的工作区库。
-  提供 *requirements.txt* 或 *Conda environment.yml* 环境规范，以用于安装 PyPI、Conda-Forge 等存储库中的包。

> [!IMPORTANT]
> - 如果要安装的包很大，或者需要很长时间才能完成安装，则会影响 Spark 实例的启动时间。
> - 不支持更改 PySpark、Python、Scala/Java、.NET 或 Spark 版本。
> - 不支持在已启用“数据外泄防护”的工作区中安装来自 PyPI、Conda-Forge 等外部存储库或默认 Conda 通道的包。

### <a name="install-python-packages"></a>安装 Python 包
可提供环境规范文件来安装 PyPI 和 Conda-Forge 等外部存储库中的 Python 包。 

#### <a name="environment-specification-formats"></a>环境规范格式

##### <a name="pip-requirementstxt"></a>PIP requirements.txt
可以使用 *requirements.txt* 文件（`pip freeze` 命令的输出）来升级环境。 更新池时，将从 PyPI 下载此文件中列出的包。 然后，将缓存并保存全部依赖项，以便以后可重复使用池。 

以下代码片段显示了要求文件的格式。 PyPI 包名称将与确切的版本一起列出。 此文件遵循 [pip freeze](https://pip.pypa.io/en/stable/cli/pip_freeze/) 参考文档中所述的格式。 

此示例固定使用一个特定版本。 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YML 格式（预览）
此外，还可以提供 *environment.yml* 文件来更新池环境。 此文件中列出的包是从默认 Conda 通道、Conda-Forge 和 PyPI 下载的。 可以使用配置选项指定其他通道或删除默认通道。

此示例指定通道和 Conda/PyPI 依赖项。 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
有关从此 environment.yml 文件创建环境的详细信息，请参阅[从 environment.yml 文件创建环境](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment
)。

#### <a name="update-python-packages"></a>更新 Python 包
确定要在 Spark 池上安装的环境规范文件或库集后，可以通过导航到 Synapse Studio 或 Azure 门户来更新 Spark 池库。 在 Azure Synapse Studio 或 Azure 门户中可以提供环境规范并选择要安装的工作区库。 

保存更改后，某个 Spark 作业将运行安装并缓存生成的环境供以后重复使用。 作业完成后，新的 Spark 作业或笔记本会话将使用更新的池库。 

##### <a name="manage-packages-from-synapse-studio-or-azure-portal"></a>通过 Synapse Studio 或 Azure 门户管理包
通过 Synapse Studio 或 Azure 门户可以管理 Spark 池库。 

在 Spark 池中更新或添加库：
1. 从 Azure 门户导航到 Azure Synapse Analytics 工作区。

    如果要通过“Azure 门户”更新：

    - 在“Synapse 资源”部分下，选择“Apache Spark 池”选项卡，然后从列表中选择一个 Spark 池 。
     
    - 从 Spark 池的“设置”部分选择“包” 。
  
    ![突出显示“上传环境配置文件”按钮的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "添加 Python 库")
   
    如果要通过 Synapse Studio 更新：
    - 从主导航面板中选择“管理”，然后选择“Apache Spark 池” 。

    - 选择特定 Spark 池的“包”部分。
    ![突出显示 Studio 中“上传环境配置”选项的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "从 Studio 添加 Python 库")
   
2. 使用该页的“包”部分中的文件选择器上传环境配置文件。
3. 还可以选择要添加到池的其他 **工作区包**。 
4. 保存更改后，将触发系统作业来安装和缓存指定的库。 此过程有助于缩短总体会话启动时间。 
5. 成功完成作业后，所有新会话都将选取更新的池库。

> [!IMPORTANT]
> 通过选择“强制使用新设置”选项，将结束所选 Spark 池的所有当前会话。 会话结束后，需要等待池重启。 
>
> 如果未选中此设置，则需要等待当前 Spark 会话结束或手动将其停止。 会话结束后，需要重启池。


##### <a name="track-installation-progress-preview"></a>跟踪安装进度（预览版）
每次为池更新一组新的库时，都会启动系统预留的 Spark 作业。 此 Spark 作业有助于监视库的安装状态。 如果由于库冲突或其他问题而导致安装失败，Spark 池将恢复为以前的状态或默认状态。 

此外，用户还可以检查安装日志来识别依赖项冲突，或查看在池更新过程中安装了哪些库。

查看安装日志：
1. 导航到“监视”选项卡中的 Spark 应用程序列表。 
2. 选择与池更新相对应的系统 Spark 应用程序作业。 这些系统作业在 SystemReservedJob-LibraryManagement 标题下运行。
   ![突出显示系统预留的库作业的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "查看系统库作业")
3. 切换查看“驱动程序”和 stdout 日志。 
4. 在结果中，将会看到与依赖项安装相关的日志。
    ![突出显示系统预留的库作业结果的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "查看系统库作业进度")

## <a name="install-wheel-files"></a>安装 wheel 文件
Python wheel 文件是用于打包 Python 库的一种常用方式。 在 Azure Synapse Analytics 中，用户可将其 wheel 文件上传到 Azure Data Lake Storage 帐户中的已知位置，或使用 Azure Synapse 工作区包界面来上传。

### <a name="workspace-packages-preview"></a>工作区包（预览）
工作区包可以是自定义的或专用的 wheel 文件。 可将这些包上传到工作区，然后再将其分配到特定的 Spark 池。

添加工作区包：
1. 导航到“管理” > “工作区包”选项卡。
2. 使用文件选择器上传 wheel 文件。
3. 将文件上传到 Azure Synapse 工作区后，可将这些包添加到给定的 Apache Spark 池。

![突出显示工作区包的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "查看工作区包")

>[!WARNING]
>- 在 Azure Synapse 中，Apache Spark 池可以利用自定义库，自定义库可以作为工作区包上传，也可以上传到已知的 Azure Data Lake Storage 路径内。 但在同一 Apache Spark 池内不能同时使用这两种选项。 如果使用两种方法提供包，则只会安装在工作区包列表中指定的 wheel 文件。 
>
>- 在指定 Apache Spark 池中使用工作区包（预览版）安装包后，就不能再在同一池中使用存储帐户路径来指定包。  

### <a name="storage-account"></a>存储帐户
通过将所有 wheel 文件上传到与 Synapse 工作区关联的 Azure Data Lake Storage (Gen2) 帐户，可以在 Apache Spark 池上安装自定义 wheel 包。 

应将这些文件上传到存储帐户默认容器中的以下路径： 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> - 某些情况下，如果文件路径不存在，则可能需要根据上面的结构创建文件路径。 例如，可能需要 ```libraries``` 文件夹中添加 ```python``` 文件夹（如果该文件夹不存在）。
> - 用于 Apache Spark 3.0 的 Azure Synapse 运行时上不支持这种自定义 wheel 文件的管理方法。 请参阅工作区包功能以管理自定义 wheel 文件。

> [!IMPORTANT]
> 若要使用 Azure DataLake Storage 方法安装自定义库，必须在已链接到 Azure Synapse Analytics 工作区的主 Gen2 存储帐户中拥有“存储 Blob 数据参与者”或“存储 Blob 数据所有者”权限。 


## <a name="session-scoped-packages-preview"></a>会话范围的包（预览）
除了池级别的包以外，还可以在笔记本会话开始时指定会话范围的库。  会话范围的库可让你在笔记本会话中指定并使用自定义的 Python 环境。 

使用限制会话范围的库时，请务必牢记以下几点：
   - 安装限制会话范围的库时，只有当前笔记本可以访问指定的库。 
   - 这些库不会影响使用同一 Spark 池的其他会话或作业。 
   - 这些库将安装在基本运行时和池级别库之上。 
   - 笔记本库的优先级最高。

若要指定会话范围的包：
1.  导航到所选的 Spark 池，确保已启用会话级别的库。  可以导航到“管理” > “Apache Spark 池” > “包”选项卡来启用此设置。  ![启用会话包。](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "启用会话包")
2.  应用设置后，可以打开笔记本并选择“配置会话”> “包”。 
  ![指定会话包。](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "更新会话配置")
3.  在此处可以上传 Conda *environment.yml* 文件，以在会话中安装或升级包。 启动会话后，将安装指定的库。 会话结束后，这些库将不再可用，因为它们特定于你的会话。

## <a name="verify-installed-libraries"></a>验证已安装的库
若要验证是否安装了 PyPI 中的正确库版本，请运行以下代码：
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
在某些情况下，若要查看 Conda 中的包版本，可能需要单独检查包版本。

## <a name="next-steps"></a>后续步骤
- 查看默认库：[Apache Spark 版本支持](apache-spark-version-support.md)
- 排查库安装错误：[排查库错误](apache-spark-troubleshoot-library-errors.md)
- 使用 Azure Data Lake Storage 帐户创建专用的 Conda 通道：[Conda 专用通道](./spark/../apache-spark-custom-conda-channel.md)
