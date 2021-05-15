---
title: 管理 Apache Spark 使用的 Python 库
description: 了解如何在 Azure Synapse Analytics 中添加和管理 Apache Spark 使用的 Python 库。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 2d6ac02402414f096a46fec0340c3074d8e1784a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586635"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中管理 Apache Spark 使用的 Python 库

这些库提供了可重用的代码，你可能想要在程序或项目中包含这些代码。 

由于各种原因，可能需要更新无服务器 Apache Spark 池环境。 例如，你可能会发现：
- 其中一个核心依赖项刚刚发布了新版本。
- 你需要额外的包来训练机器学习模型或准备数据。
- 你找到了一个更好的包，并且不再需要较早的包。

为使第三方或本地生成的代码可用于自己的应用程序，可向其中一个无服务器 Apache Spark 池或笔记本会话中安装一个库。 本文将介绍如何跨无服务器 Apache Spark 池管理 Python 库。

## <a name="default-installation"></a>默认安装
Azure Synapse Analytics 中的 Apache Spark 包含一整套用于常见数据工程、数据准备、机器学习和数据可视化任务的库。 你可在 [Apache Spark 版本支持](apache-spark-version-support.md)中找到完整的库列表。 

当 Spark 实例启动时，将自动包含这些库。 在 Spark 池和会话级别可以添加额外的 Python 和自定义包。

## <a name="pool-libraries"></a>池库
在确定要用于 Spark 应用程序的 Python 库后，就可以将它们安装到 Spark 池中。 池级别的库可用于在池中运行的所有笔记本和作业。

可以通过两种主要方式在群集上安装库：
-  安装已作为工作区包上传的工作区库。
-  提供 requirements.txt 或 Conda environment.yml 环境规范，以从 PyPI、Conda-Forge 等存储库中安装包。

> [!IMPORTANT]
> - 如果要安装的包很大，或者需要很长时间才能完成安装，则会影响 Spark 实例的启动时间。
> - 不支持更改 PySpark、Python、Scala/Java、.NET 或 Spark 版本。
> - 在已启用 DEP 的工作区中不支持从外部存储库（如 PyPI、Conda-Forge 或默认 Conda 通道）安装包。

### <a name="install-python-packages"></a>安装 Python 包
可以通过提供环境规范文件，从 PyPI 和 Conda-Forge 等存储库安装 Python 包。 

#### <a name="environment-specification-formats"></a>环境规范格式

##### <a name="pip-requirementstxt"></a>PIP requirements.txt
可使用 requirements.txt 文件（`pip freeze` 命令的输出）来升级环境。 更新池时将从 PyPI 下载此文件中列出的包。 然后，将缓存并保存全部依赖项，以便以后再次使用该池。 

以下代码片段显示了要求文件的格式。 PyPI 包名称将与具体的版本一起列出。 此文件遵循 [pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) 参考文档中所述的格式。 

此示例固定使用一个特定版本。 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YML 格式（预览版）
此外，还可以提供 environment.yml 文件来更新池环境。 此文件中列出的包将从默认 Conda 通道、Conda-Forge 和 PyPI 下载。 可以使用配置选项指定其他通道或删除默认通道。

以下示例指定了通道和 Conda/PyPI 依赖项。 

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
有关根据此 environment.yml 文件创建环境的详细信息，请参阅[根据 environment.yml 文件创建环境](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually)。

#### <a name="update-python-packages"></a>更新 Python 包
确定要在 Spark 池上安装的环境规范文件或库集后，可以通过导航到 Azure Synapse Studio 或 Azure 门户来更新 Spark 池库。 在此，可以提供环境规范和选择要安装的工作区库。 

保存更改后，Spark 作业将运行安装并缓存生成的环境，供以后再次使用。 作业完成后，新的 Spark 作业或笔记本会话将使用更新的池库。 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>通过 Azure Synapse Studio 或 Azure 门户管理包
通过 Azure Synapse Studio 或 Azure 门户可以管理 Spark 池库。 

在 Spark 池中更新或添加库：
1. 从 Azure 门户导航到 Azure Synapse Analytics 工作区。

    如果要通过“Azure 门户”更新：

    - 在“Synapse 资源”部分下，选择“Apache Spark 池”选项卡，然后从列表中选择一个 Spark 池 。
     
    - 从 Spark 池的“设置”部分选择“包” 。
  
    ![突出显示“上传环境配置文件”按钮的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "添加 Python 库")
   
    如果要通过 Synapse Studio 更新：
    - 从主导航面板中选择“管理”，然后选择“Apache Spark 池” 。

    - 选择特定 Spark 池的“包”部分。
    ![突出显示从工作室中上传环境配置选项的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "从工作室添加 Python 库")
   
2. 使用该页面“包”部分的文件选择器上传环境配置文件。
3. 此外，还可以选择其他工作区包添加到自己的池中。 
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
2. 选择与池更新相对应的系统 Spark 应用程序作业。 这些系统作业在 *SystemReservedJob-LibraryManagement* 标题下运行。
   ![突出显示系统预留的库作业的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "查看系统库作业")
3. 切换查看“驱动程序”和 stdout 日志。 
4. 在结果中，你将看到与自己的依赖项安装相关的日志。
    ![突出显示系统预留的库作业结果的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "查看系统库作业进度")

## <a name="install-wheel-files"></a>安装 wheel 文件
Python wheel 文件是打包 Python 库的常用方法。 在 Azure Synapse Analytics 中，用户可以将其 wheel 文件上传到 Azure Data Lake Storage 帐户的某个已知位置，或使用 Azure Synapse 工作区包接口上传。

### <a name="workspace-packages-preview"></a>工作区包（预览版）
工作区包可以是自定义或专用 wheel 文件。 可以先将这些包上传到自己的工作区，以后再将它们分配给特定的 Spark 池。

添加工作区包：
1. 导航到“管理” > “工作区包”选项卡。
2. 使用文件选择器上传 wheel 文件。
3. 将这些文件上传到 Azure Synapse 工作区后，则可将这些包添加到指定的 Apache Spark 池。

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
> 某些情况下，如果文件路径不存在，则可能需要根据上面的结构创建文件路径。 例如，可能需要 ```libraries``` 文件夹中添加 ```python``` 文件夹（如果该文件夹不存在）。

> [!IMPORTANT]
> 要使用 Azure DataLake Storage 方法安装自定义库，必须对链接到 Azure Synapse Analytics 工作区的主 Gen2 存储帐户拥有“存储 Blob 数据参与者”或“存储 Blob 数据所有者”权限。


## <a name="session-scoped-packages-preview"></a>限制会话范围的包（预览版）
除池级别的包之外，还可以在笔记本会话开始时指定限制会话范围的库。  使用限制会话范围的库，可以在笔记本会话中指定和使用自定义 Python 环境。 

使用限制会话范围的库时，请务必牢记以下几点：
   - 安装限制会话范围的库时，只有当前笔记本可以访问指定的库。 
   - 这些库不会影响使用同一 Spark 池的其他会话或作业。 
   - 这些库将安装在基本运行时和池级别库的上方。 
   - 笔记本库的优先级最高。

指定限制会话范围的包：
1.  导航到所选的 Spark 池，并确保已启用会话级别的库。  通过导航到“管理” > “Apache Spark 池” > “包”选项卡可以启用此设置。![启用会话包。](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "启用会话包")
2.  应用设置后，可以打开笔记本，然后选择“配置会话”> “包”。
  ![指定会话包。](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "更新会话配置")
3.  在此，可以上传 Conda environment.yml 文件以在会话中安装或升级包。 启动会话后，将安装指定的库。 会话结束后，这些库将不再可用，因为它们专用于你的会话。

## <a name="verify-installed-libraries"></a>验证已安装的库
要验证从 PyPI 安装的库和版本是否正确，请运行以下代码：
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
在某些情况下，若要查看 Conda 中的包版本，可能需要单独检查包版本。

## <a name="next-steps"></a>后续步骤
- 查看默认库：[Apache Spark 版本支持](apache-spark-version-support.md)
- 排查库安装错误：[排查库错误](apache-spark-troubleshoot-library-errors.md)
- 使用 Azure Data Lake Storage 帐户创建专用 Conda 通道：[Conda 专用通道](./spark/../apache-spark-custom-conda-channel.md)
