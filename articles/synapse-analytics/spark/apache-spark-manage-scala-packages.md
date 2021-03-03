---
title: 管理用于 Apache Spark 的 Scala & Java 库
description: 了解如何在 Azure Synapse Analytics 中添加和管理 Scala 和 Java 库。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 8d5c18eaaa4065eac515f38557664ceb44262adf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695909"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中管理 Apache Spark 的 Scala 和 Java 包

库提供你可能想要包括在程序或项目中的可重用代码。 

出于多种原因，你可能需要更新无服务器 Apache Spark 池环境。 例如，你可能会发现：
- 核心依赖关系之一发布了新版本。
- 需要额外的包来训练机器学习模型或准备数据。
- 你找到了一个更好的包，并且不再需要较早的包。

要使第三方或本地生成的代码可用于你的应用程序，你可以将库安装到你的无服务器 Apache Spark 池或笔记本会话之一。 在本文中，我们将介绍如何管理 Scala 和 Java 包。

## <a name="default-installation"></a>默认安装
Azure Synapse Analytics 中的 Apache Spark 包含一整套用于常见数据工程、数据准备、机器学习和数据可视化任务的库。 可以在 [Apache Spark 版本支持](apache-spark-version-support.md)中找到 "完整库" 列表。 

当 Spark 实例启动时，将自动包含这些库。 可以将额外的 Scala/Java 包添加到 Spark 池和会话级别。

## <a name="workspace-packages"></a>工作区包
工作区包可以是自定义或专用 jar 文件。 你可以将这些包上传到你的工作区，然后将它们分配给特定的 Spark 池。

添加工作区包：
1. 导航到 "**管理**  >  **工作区包**" 选项卡。
2. 使用文件选择器上传轮文件。
3. 将文件上传到 Azure Synapse 工作区后，可以将这些滚轮文件添加到给定 Apache Spark 池。

![突出显示工作区包的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "查看工作区包")

> [!IMPORTANT]
> 目前尚不支持在数据渗透 (DEP) 工作区中安装工作区包。

## <a name="pool-libraries"></a>池库
确定要用于 Spark 应用程序的 Scala 和 Java 包后，可以将它们安装到 Spark 池中。 池级库可用于在池中运行的所有笔记本和作业。

可以通过导航到 Azure Synapse Studio 或 Azure 门户来更新 Spark 池库。 在此处，你可以选择要安装的工作区库。 

保存更改后，Spark 作业将运行安装并缓存生成的环境，供以后重复使用。 作业完成后，新的 Spark 作业或笔记本会话将使用更新的池库。 

> [!IMPORTANT]
> - 如果要安装的包很大或需要很长时间才能安装，这会影响 Spark 实例的启动时间。
> - 不支持更改 PySpark、Python、Scala/Java、.NET 或 Spark 版本。

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>从 Azure Synapse Studio 或 Azure 门户管理包
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
   
2. 若要添加 Jar 文件，请导航到 **工作区包** 部分以添加到你的池。 
3. 保存更改后，将触发系统作业以安装并缓存指定的库。 此过程有助于缩短总体会话启动时间。 
4. 作业成功完成后，所有新会话都将选取更新的池库。

> [!IMPORTANT]
> 通过选择 **强制新设置** 的选项，你将结束所选 Spark 池的所有当前会话。 会话结束后，需要等待池重新启动。 
>
> 如果未选中此设置，则需要等待当前 Spark 会话手动结束或停止。 会话结束后，需要重新启动池。

#### <a name="track-installation-progress-preview"></a> (预览跟踪安装进度) 
每次使用一组新的库更新池时，都会启动系统保留的 Spark 作业。 此 Spark 作业有助于监视库安装的状态。 如果由于库冲突或其他问题而导致安装失败，Spark 池会恢复为其以前的状态或默认状态。 

此外，用户还可以检查安装日志以确定依赖关系冲突，或查看在更新池的过程中安装了哪些库。

查看这些日志：
1. 导航到 " **监视** " 选项卡中的 "Spark 应用程序" 列表。 
2. 选择与池更新相对应的系统 Spark 应用程序作业。 这些系统作业在 *SystemReservedJob-LibraryManagement* 标题下运行。
   ![突出显示系统保留库作业的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "查看系统库作业")
3. 切换以查看 **驱动程序** 和 **stdout** 日志。 
4. 在结果中，你将看到与安装包相关的日志。
    ![突出显示系统保留库作业结果的屏幕截图。](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "查看系统库作业进度")

## <a name="session-scoped-libraries"></a>会话作用域的库 
除了池级别库以外，还可以在笔记本会话开始时指定会话范围的库。  使用会话范围的库可以在笔记本会话中专门指定和使用 jar 包。 

使用会话范围的库时，请务必牢记以下几点：
   - 安装会话范围的库时，只有当前笔记本可以访问指定的库。 
   - 这些库不会影响使用同一 Spark 池的其他会话或作业。 
   - 这些库安装在基本运行时和池级别库的顶层。 
   - 笔记本库将采用最高的优先级。

若要指定会话范围的 Java 或 Scala 包，可以使用 ```%%configure``` 选项：

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

建议你在笔记本开头运行%% 配置。 若要查看有效参数的完整列表，请参阅此 [文档](https://github.com/cloudera/livy#request-body) 。

## <a name="next-steps"></a>后续步骤
- 查看默认库： [Apache Spark 版本支持](apache-spark-version-support.md)
- 库安装错误疑难解答： [库错误疑难解答](apache-spark-troubleshoot-library-errors.md)
