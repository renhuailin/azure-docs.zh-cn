---
title: 管理用于 Apache Spark 的 Scala 和 Java 库
description: 了解如何在 Azure Synapse Analytics 中添加和管理 Scala 和 Java 库。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: bed5c6b1b1cf21de0105e0eeb2096d359177c115
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814993"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中管理用于 Apache Spark 的 Scala 和 Java 包

这些库提供了可重用的代码，你可能想要在程序或项目中包含这些代码。 

由于各种原因，可能需要更新无服务器 Apache Spark 池环境。 例如，你可能发现：
- 其中一个核心依赖项发布了新版本。
- 需要额外的包来训练机器学习模型或准备数据。
- 你找到了一个更好的包，并且不再需要较早的包。

为使第三方或本地生成的代码可用于自己的应用程序，可向其中一个无服务器 Apache Spark 池或笔记本会话中安装一个库。 本文将介绍如何管理 Scala 和 Java 包。

## <a name="default-installation"></a>默认安装
Azure Synapse Analytics 中的 Apache Spark 包含一整套用于常见数据工程、数据准备、机器学习和数据可视化任务的库。 你可在 [Apache Spark 版本支持](apache-spark-version-support.md)中找到完整的库列表。 

当 Spark 实例启动时，将自动包含这些库。 在 Spark 池和会话级别可以添加额外的 Scala/Java 包。

## <a name="workspace-packages"></a>工作区包
工作区包可以是自定义或专用 jar 文件。 可以先将这些包上传到自己的工作区，以后再将其分配到特定的 Spark 池。

添加工作区包：
1. 导航到“管理” > “工作区包”选项卡。
2. 使用文件选择器上传 jar 文件。
3. 将这些 jar 文件上传到 Azure Synapse 工作区后，就可将它们添加到指定的 Apache Spark 池。

![突出显示工作区包的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "查看工作区包")

## <a name="pool-libraries"></a>池库
在确定要用于 Spark 应用程序的 Scala 和 Java 包后，就可将它们安装到 Spark 池中。 池级别的库可用于在池中运行的所有笔记本和作业。

可以导航到 Synapse Studio 或 Azure 门户来更新 Spark 池库。 在此，可选择要安装的工作区库。 

保存更改后，Spark 作业将运行安装并缓存生成的环境，供以后再次使用。 作业完成后，新的 Spark 作业或笔记本会话将使用更新的池库。 

> [!IMPORTANT]
> - 如果要安装的包很大，或者需要很长时间才能完成安装，则会影响 Spark 实例的启动时间。
> - 不支持更改 PySpark、Python、Scala/Java、.NET 或 Spark 版本。

#### <a name="manage-packages-from-synapse-studio-or-azure-portal"></a>通过 Synapse Studio 或 Azure 门户管理包
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
    ![突出显示从 Studio 上传环境配置选项的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "从 Studio 添加 Python 库")
   
2. 若要添加 Jar 文件，请导航到“工作区包”部分，将它们添加到池。 
3. 保存更改后，将触发系统作业来安装和缓存指定的库。 此过程有助于缩短总体会话启动时间。 
4. 成功完成作业后，所有新会话都将选取更新的池库。

> [!IMPORTANT]
> 通过选择“强制使用新设置”选项，将结束所选 Spark 池的所有当前会话。 会话结束后，需要等待池重启。 
>
> 如果未选中此设置，则需要等待当前 Spark 会话结束或手动将其停止。 会话结束后，需要重启池。

#### <a name="track-installation-progress-preview"></a>跟踪安装进度（预览版）
每次为池更新一组新的库时，都会启动系统预留的 Spark 作业。 此 Spark 作业有助于监视库的安装状态。 如果由于库冲突或其他问题而导致安装失败，Spark 池将恢复为以前的状态或默认状态。 

此外，用户还可以检查安装日志来识别依赖项冲突，或查看在池更新过程中安装了哪些库。

查看安装日志：
1. 导航到“监视”选项卡中的 Spark 应用程序列表。 
2. 选择与池更新相对应的系统 Spark 应用程序作业。 这些系统作业在 SystemReservedJob-LibraryManagement 标题下运行。
   ![突出显示系统预留的库作业的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "查看系统库作业")
3. 切换查看“驱动程序”和 stdout 日志。 
4. 在结果中，你将看到与自己的包安装相关的日志。
    ![突出显示系统预留的库作业结果的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "查看系统库作业进度")

## <a name="session-scoped-libraries"></a>限制会话范围的库 
除池级别的库之外，还可在笔记本会话开始时指定限制会话范围的库。  使用限制会话范围的库，可仅在某个笔记本会话中指定和使用 jar 包。 

使用限制会话范围的库时，请务必牢记以下几点：
   - 安装限制会话范围的库时，只有当前笔记本可以访问指定的库。 
   - 这些库不会影响使用同一 Spark 池的其他会话或作业。 
   - 这些库将安装在基本运行时和池级别库之上。 
   - 笔记本库的优先级最高。

若要指定限制会话范围的 Java 或 Scala 包，可使用 ```%%configure``` 选项：

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

建议你在笔记本开头运行 %%configure。 若要查看有效参数的完整列表，请参阅此[文档](https://github.com/cloudera/livy#request-body)。

## <a name="next-steps"></a>后续步骤
- 查看默认库：[Apache Spark 版本支持](apache-spark-version-support.md)
- 排查库安装错误：[排查库错误](apache-spark-troubleshoot-library-errors.md)
