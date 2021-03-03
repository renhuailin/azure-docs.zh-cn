---
title: 管理 Apache Spark 的 Python 库
description: 了解如何添加和管理 Azure Synapse 分析中 Apache Spark 使用的 Python 库。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 296bd3a4a75cdd7f5dab3b6eb5fdcb00a889703d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695910"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse 分析中管理用于 Apache Spark 的 Python 库

库提供你可能想要包括在程序或项目中的可重用代码。 

由于各种原因，可能需要更新无服务器 Apache Spark 池环境。 例如，你可能会发现：
- 你的核心依赖关系之一刚刚发布了新版本。
- 需要额外的包来训练机器学习模型或准备数据。
- 你找到了一个更好的包，并且不再需要较早的包。

要使第三方或本地生成的代码可用于你的应用程序，你可以将库安装到你的无服务器 Apache Spark 池或笔记本会话之一。 本文介绍如何跨无服务器 Apache Spark 池管理 Python 库。

## <a name="default-installation"></a>默认安装
Azure Synapse Analytics 中的 Apache Spark 包含一整套用于常见数据工程、数据准备、机器学习和数据可视化任务的库。 可以在 [Apache Spark 版本支持](apache-spark-version-support.md)中找到 "完整库" 列表。 

当 Spark 实例启动时，将自动包含这些库。 可以在 Spark 池和会话级别添加额外的 Python 和自定义构建的包。

## <a name="pool-libraries"></a>池库
确定要用于 Spark 应用程序的 Python 库后，可以将它们安装到 Spark 池中。 池级库可用于在池中运行的所有笔记本和作业。

可以通过两种主要方式在群集上安装库：
-  安装已作为工作区包上传的工作区库。
-  提供 *requirements.txt* 或 *Conda 环境。 docker-compose.override.yml* 环境规范，用于从 PyPI、Conda 和其他存储库安装包。

> [!IMPORTANT]
> - 如果要安装的包很大或需要很长时间才能安装，这会影响 Spark 实例的启动时间。
> - 不支持更改 PySpark、Python、Scala/Java、.NET 或 Spark 版本。
> - 启用 DEP 的工作区中不支持从 PyPI 安装包。

### <a name="install-python-packages"></a>安装 Python 包
可以通过提供环境规范文件，从 PyPI 和 Conda-Forge 等存储库安装 Python 包。 

#### <a name="environment-specification-formats"></a>环境规范格式

##### <a name="pip-requirementstxt"></a>PIP requirements.txt
可以使用命令) 的 *requirements.txt* 文件 (输出来 `pip freeze` 升级环境。 更新池时，将从 PyPI 下载此文件中列出的包。 然后，将缓存并保存全部依赖项，以便以后再次使用池。 

以下代码片段显示了要求文件的格式。 PyPI 包名称与精确版本一起列出。 此文件遵循 [pip 冻结](https://pip.pypa.io/en/stable/reference/pip_freeze/) 参考文档中所述的格式。 

此示例固定特定版本。 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>DOCKER-COMPOSE.OVERRIDE.YML 格式 (预览) 
此外，还可以提供 *docker-compose.override.yml* 文件来更新池环境。 此文件中列出的包是从默认的 Conda 通道、Conda 和 PyPI 下载的。 您可以使用配置选项指定其他通道或删除默认通道。

此示例指定了通道和 Conda/PyPI 依赖关系。 

```
name: stats2
channels:
  - defaults
dependencies:
  - bokeh=0.9.2
  - numpy=1.9.*
  - flask
  - pip:
    - matplotlib
```
有关基于此 docker-compose.override.yml 文件创建环境的详细信息，请参阅 [从 docker-compose.override.yml 文件创建环境](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually)。

#### <a name="update-python-packages"></a>更新 Python 包
确定要在 Spark 池上安装的环境规范文件或库集后，可以通过导航到 Azure Synapse Studio 或 Azure 门户来更新 Spark 池库。 在此处，你可以提供环境规范并选择要安装的工作区库。 

保存更改后，Spark 作业将运行安装并缓存生成的环境，供以后重复使用。 作业完成后，新的 Spark 作业或笔记本会话将使用更新的池库。 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>从 Azure Synapse Studio 或 Azure 门户管理包
可以通过 Azure Synapse Studio 或 Azure 门户来管理 Spark 池库。 

更新或将库添加到 Spark 池：
1. 从 Azure 门户导航到 Azure Synapse Analytics 工作区。

    如果要从 **Azure 门户** 更新：

    - 在 " **Synapse 资源** " 部分下，选择 " **Apache Spark 池** " 选项卡，然后从列表中选择一个 Spark 池。
     
    - 从 Spark 池的 "**设置**" 部分中选择 **包**。
  
    ![突出显示 "上传环境配置文件" 按钮的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "添加 Python 库")
   
    如果从 **Synapse Studio** 更新：
    - 从主导航面板中选择 " **管理** "，然后选择 " **Apache Spark 池**"。

    - 选择特定 Spark 池的 " **包** " 部分。
    ![突出显示 studio 中的上传环境配置选项的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "从工作室添加 Python 库")
   
2. 使用页面的 "  **包** " 部分中的文件选择器上载环境配置文件。
3. 你还可以选择要添加到池的其他 **工作区包** 。 
4. 保存更改后，将触发系统作业以安装并缓存指定的库。 此过程有助于缩短总体会话启动时间。 
5. 作业成功完成后，所有新会话都将选取更新的池库。

> [!IMPORTANT]
> 通过选择 **强制新设置** 的选项，你将结束所选 Spark 池的所有当前会话。 会话结束后，需要等待池重新启动。 
>
> 如果未选中此设置，则需要等待当前 Spark 会话手动结束或停止。 会话结束后，需要重新启动池。


##### <a name="track-installation-progress-preview"></a> (预览跟踪安装进度) 
每次使用一组新的库更新池时，都会启动系统保留的 Spark 作业。 此 Spark 作业有助于监视库安装的状态。 如果由于库冲突或其他问题而导致安装失败，Spark 池会恢复为其以前的状态或默认状态。 

此外，用户还可以检查安装日志以确定依赖关系冲突，或查看在更新池的过程中安装了哪些库。

查看这些日志：
1. 导航到 " **监视** " 选项卡中的 "Spark 应用程序" 列表。 
2. 选择与池更新相对应的系统 Spark 应用程序作业。 这些系统作业在 *SystemReservedJob-LibraryManagement* 标题下运行。
   ![突出显示系统保留库作业的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "查看系统库作业")
3. 切换以查看 **驱动程序** 和 **stdout** 日志。 
4. 在结果中，你将看到与你的依赖项安装相关的日志。
    ![突出显示系统保留库作业结果的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "查看系统库作业进度")

## <a name="install-wheel-files"></a>安装滚轮文件
Python 轮文件是打包 Python 库的常用方法。 在 Azure Synapse Analytics 中，用户可以将其滑轮文件上传到 Azure Data Lake Storage 帐户的已知位置，或使用 Azure Synapse 工作区包接口上传。

### <a name="workspace-packages-preview"></a> (预览的工作区包) 
工作区包可以是自定义或专用滚轮文件。 你可以将这些包上传到你的工作区，然后将它们分配给特定的 Spark 池。

添加工作区包：
1. 导航到 "**管理**  >  **工作区包**" 选项卡。
2. 使用文件选择器上传轮文件。
3. 文件上传到 Azure Synapse 工作区后，可以将这些包添加到给定 Apache Spark 池。

![突出显示工作区包的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "查看工作区包")

> [!IMPORTANT]
> 目前尚不支持在数据渗透 (DEP) 工作区中安装工作区包。

### <a name="storage-account"></a>存储帐户
可以通过将所有轮文件上传到与 Synapse 工作区链接的 Azure Data Lake Storage (Gen2) 帐户，将自定义的轮包安装到 Apache Spark 池。 

应将这些文件上传到存储帐户的默认容器中的以下路径： 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

如果文件夹不存在，您可能需要在 ```python``` 文件夹中添加文件夹 ```libraries``` 。

> [!IMPORTANT]
> 若要使用 Azure DataLake 存储方法安装自定义库，必须在链接到 Azure Synapse 分析工作区的主要 Gen2 存储帐户上具有 **存储 Blob 数据参与者** 或 **存储 blob 数据所有者** 权限。

>[!WARNING]
> 提供自定义滚轮文件时，用户无法在存储帐户和工作区库界面中提供滑轮文件。 如果同时提供两者，则只会安装在 "工作区包" 列表中指定的轮文件。 

## <a name="session-scoped-libraries-preview"></a> (预览的会话作用域的库) 
除了池级别库以外，还可以在笔记本会话开始时指定会话范围的库。  使用会话范围的库可以在笔记本会话中指定和使用自定义 Python 环境。 

使用会话范围的库时，请务必牢记以下几点：
   - 安装会话范围的库时，只有当前笔记本可以访问指定的库。 
   - 这些库不会影响使用同一 Spark 池的其他会话或作业。 
   - 这些库安装在基本运行时和池级别库的顶层。 
   - 笔记本库将采用最高的优先级。

指定会话范围的包：
1.  导航到所选 Spark 池，并确保已启用会话级库。  可以通过导航到 "**管理**  >  **Apache Spark 池**  >  **包**" 选项卡来启用此设置。![启用会话包。](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "启用会话包")
2.  应用设置后，可以打开笔记本，然后选择 "**配置会话** >  **包**"。
  ![指定会话包。](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "更新会话配置")
3.  在这里，你可以上传 Conda *docker-compose.override.yml* 文件以在会话中安装或升级包。 启动会话后，将安装指定的库。 会话结束后，这些库将不再可用，因为它们特定于你的会话。

## <a name="verify-installed-libraries"></a>验证安装的库
若要验证是否从 PyPI 安装了正确的库版本，请运行以下代码：
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
在某些情况下，若要查看 Conda 中的包版本，你可能需要单独检查包版本。

## <a name="next-steps"></a>后续步骤
- 查看默认库： [Apache Spark 版本支持](apache-spark-version-support.md)
- 库安装错误疑难解答： [库错误疑难解答](apache-spark-troubleshoot-library-errors.md)
