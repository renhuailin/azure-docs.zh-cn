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
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598008"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中管理 Apache Spark 使用的库
这些库提供了可重用的代码，你可能想要在程序或项目中包含这些代码。 

出于各种原因，你可能需要更新无服务器 Apache Spark 池环境。 例如，你可能发现：
- 某个核心依赖项发布了新版本。
- 你需要使用额外的包来训练机器学习模型或准备数据。
- 你找到了一个更好的包，因此不再需要旧包。
- 你的团队生成了一个需要在 Apache Spark 池中提供的自定义包。

要使第三方代码或本地生成的代码可在你的应用程序中使用，可在某个无服务器 Apache Spark 池或笔记本会话中安装一个库。
  
## <a name="default-installation"></a>默认安装
Azure Synapse Analytics 中的 Apache Spark 具有完整的 Anacondas 安装和额外的库。 你可在 [Apache Spark 版本支持](apache-spark-version-support.md)中找到完整的库列表。 

当 Spark 实例启动时，将自动包含这些库。 可以在 Spark 池级别或会话级别添加其他包。

## <a name="workspace-packages"></a>工作区包
开发自定义应用程序或模型时，团队可能会开发各种代码项目（例如 wheel 或 jar 文件）用于打包代码。 

在 Synapse 中，工作区包可以是自定义的或专用的 wheel 或 jar 文件。 可将这些包上传到工作区，然后再将其分配到特定的 Spark 池。 分配后，这些工作区包将自动安装在所有 Spark 池会话中。

若要详细了解如何管理工作区库，请访问以下操作指南：

- [Python 工作区包（预览）：](./apache-spark-manage-python-packages.md#install-wheel-files)将 Python wheel 文件作为工作区包上传，以后再将这些包添加到特定的无服务器 Apache Spark 池。
- [Scala/Java 工作区包（预览）：](./apache-spark-manage-scala-packages.md#workspace-packages)将 Scala 和 Java jar 文件作为工作区包上传，以后再将这些包添加到特定的无服务器 Apache Spark 池。

## <a name="pool-packages"></a>池包
在某些情况下，你可能想要标准化给定 Apache Spark 池中使用的包集。 如果多个团队成员共同安装了相同的包，则这种标准化可能很有用。 

使用 Azure Synapse Analytics 池管理功能，可以配置要在给定无服务器 Apache Spark 池中安装的默认库集。 这些库是以[基础运行时](./apache-spark-version-support.md)为基础安装的。 

目前仅支持对 Python 进行池管理。 对于 Python，Synapse Spark 池使用 Conda 来安装和管理 Python 包依赖项。 指定池级别的库时，现在可以提供 requirements.txt 或 environment.yml。 每当从该 Spark 池创建 Spark 实例时，都会使用此环境配置文件。 

若要详细了解这些功能，请访问有关 [Python 池管理](./apache-spark-manage-python-packages.md#pool-libraries)的文档。

> [!IMPORTANT]
> - 如果要安装的包很大，或者需要很长时间才能完成安装，则会影响 Spark 实例的启动时间。
> - 不支持更改 PySpark、Python、Scala/Java、.NET 或 Spark 版本。
> - 在启用 DEP 的工作区中不支持从 PyPI 安装包。

## <a name="session-scoped-packages"></a>会话范围的包
通常，在进行交互式数据分析或机器学习时，你可能想要尝试较新的包，或者需要一些尚未在 Apache Spark 池中提供的包。 现在，用户无需更新池配置，而可以使用会话范围的包来添加、管理和更新会话依赖项。

会话范围的包允许用户在其会话开始时定义包依赖项。 安装会话范围的包时，只有当前会话有权访问指定的包。 因此，这些会话范围的包不会影响使用同一个 Apache Spark 池的其他会话或作业。 此外，这些库是以基础运行时和池级别包为基础安装的。 

若要详细了解如何管理会话范围的包，请访问以下操作指南：

- [Python 会话包（预览）：](./apache-spark-manage-python-packages.md)在会话开始时，提供一个 Conda *environment.yml* 文件用于从流行存储库安装其他 Python 包。 
- [Scala/Java 会话包：](./apache-spark-manage-scala-packages.md)在会话开始时，提供要通过 `%%configure` 安装的 jar 文件列表。

## <a name="next-steps"></a>后续步骤
- 查看默认库：[Apache Spark 版本支持](apache-spark-version-support.md)
