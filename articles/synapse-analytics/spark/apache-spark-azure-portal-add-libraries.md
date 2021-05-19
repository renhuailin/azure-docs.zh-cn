---
title: 包管理
description: 了解如何在 Azure Synapse Analytics 中添加和管理 Apache Spark 使用的库。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: e8ad6d072af6979eb8509068c1dcd239e7840950
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598008"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中管理 Apache Spark 使用的库
这些库提供了可重用的代码，你可能想要在程序或项目中包含这些代码。 

由于各种原因，可能需要更新无服务器 Apache Spark 池环境。 例如，可能发现：
- 其中一个核心依赖项发布了新版本。
- 需要额外的包来训练机器学习模型或准备数据。
- 找到了一个更好的包，不再需要之前的包。
- 团队已构建了一个需要在 Apache Spark 池中使用的自定义包。

为使第三方或本地生成的代码可用于自己的应用程序，可向其中一个无服务器 Apache Spark 池或 Notebook 会话中安装一个库。
  
## <a name="default-installation"></a>默认安装
Azure Synapse Analytics 中的 Apache Spark 具有完整的 Anacondas 安装和额外的库。 你可在 [Apache Spark 版本支持](apache-spark-version-support.md)中找到完整的库列表。 

当 Spark 实例启动时，将自动包含这些库。 可以在 Spark 池级别或会话级别添加其他包。

## <a name="workspace-packages"></a>工作区包
开发自定义应用程序或模型时，团队可能会开发各种代码项目（例如，滚轮或 jar 文件）来打包代码。 

在 Synapse 中，工作区包可以是自定义包，也可以是专用的滚轮或 jar 文件。 可以先将这些包上传到自己的工作区，以后再将其分配到特定的 Spark 池。 分配后，这些工作区包会自动安装在所有 Spark 池会话上。

若要了解有关如何管理工作区库的详细信息，请访问以下操作指南：

- [Python 工作区包（预览版）：](./apache-spark-manage-python-packages.md#install-wheel-files)将 Python 滚轮文件作为工作区包进行上传，以后可将这些包添加到特定的无服务器 Apache Spark 池中。
- [Scala/Java 工作区包（预览版）：](./apache-spark-manage-scala-packages.md#workspace-packages)将 Scala 和 Java jar 文件作为工作区包进行上传，以后可将这些包添加到特定的无服务器 Apache Spark 池中。

## <a name="pool-packages"></a>池包
在某些情况下，可能想要对给定的 Apache Spark 池上使用的一组包进行标准化。 如果团队中的多个用户统一安装了相同的包，则这种标准化会很有用。 

使用 Azure Synapse Analytics 池管理功能时，可以配置要在给定的无服务器 Apache Spark 池上安装的默认库集。 这些库都是基于[基本运行时](./apache-spark-version-support.md)来安装的。 

目前只有 Python 支持池管理。 对于 Python，Synapse Spark 池使用 Conda 安装和管理 Python 包依赖项。 指定池级别的库时，现在可以提供 requirements.txt 或 environment.yml。 每当通过该 Spark 池创建 Spark 实例时，都会用到这个环境配置文件。 

若要了解有关这些功能的详细信息，请访问有关 [Python 池管理](./apache-spark-manage-python-packages.md#pool-libraries)的文档。

> [!IMPORTANT]
> - 如果要安装的包很大，或者需要很长时间才能完成安装，则会影响 Spark 实例的启动时间。
> - 不支持更改 PySpark、Python、Scala/Java、.NET 或 Spark 版本。
> - 在启用 DEP 的工作区中不支持从 PyPI 安装包。

## <a name="session-scoped-packages"></a>会话范围内的包
通常，在进行交互式数据分析或机器学习时，可能会发现想要尝试更新版本的包，或者可能需要 Apache Spark 池上暂时还不可用的包。 现在，用户无需更新池配置，可以使用会话范围的包来添加、管理和更新会话依赖项。

使用会话范围的包时，用户可以在其会话开始时定义包依赖项。 安装会话范围的包时，只有当前会话可以访问指定的包。 因此，这些会话范围的包不会影响其他使用同一 Apache Spark 池的会话或作业。 此外，这些库将基于基本运行时和池级别库进行安装。 

若要进一步了解如何管理会话范围的包，请访问以下操作指南：

- [Python 会话包（预览版）：](./apache-spark-manage-python-packages.md)在会话开始时，提供 Conda *environment.yml*，以从常用存储库中安装其他 Python 包。 
- [Scala/Java 会话包：](./apache-spark-manage-scala-packages.md)在会话开始时，提供 jar 文件列表，以使用 `%%configure` 进行安装。

## <a name="next-steps"></a>后续步骤
- 查看默认库：[Apache Spark 版本支持](apache-spark-version-support.md)
