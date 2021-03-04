---
title: 包管理
description: 了解如何添加和管理 Azure Synapse 分析中 Apache Spark 使用的库。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 38826451bbb8ec9303f61db53544cfe8ca2fcd41
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123082"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse 分析中管理 Apache Spark 的库
库提供你可能想要包括在程序或项目中的可重用代码。 

由于各种原因，可能需要更新无服务器 Apache Spark 池环境。 例如，你可能会发现：
- 核心依赖关系之一发布了新版本。
- 需要额外的包来训练机器学习模型或准备数据。
- 你找到了一个更好的包，并且不再需要较早的包。
- 你的团队已构建你需要在 Apache Spark 池中使用的自定义包。

要使第三方或本地生成的代码可用于你的应用程序，你可以将库安装到你的无服务器 Apache Spark 池或笔记本会话之一。
  
## <a name="default-installation"></a>默认安装
Azure Synapse Analytics 中的 Apache Spark 具有完整的 Anacondas 安装和额外的库。 可以在 [Apache Spark 版本支持](apache-spark-version-support.md)中找到 "完整库" 列表。 

当 Spark 实例启动时，将自动包含这些库。 可以在 Spark 池级别或会话级别添加其他包。

## <a name="workspace-packages"></a>工作区包
开发自定义应用程序或模型时，您的团队可能会开发各种代码项目（如滑轮或 jar 文件）来打包您的代码。 

在 Synapse 中，工作区包可以是自定义或专用鼠标轮或 jar 文件。 你可以将这些包上传到你的工作区，然后将它们分配给特定的 Spark 池。 分配后，这些工作区包会自动安装在所有 Spark 池会话上。

若要了解有关如何管理工作区库的详细信息，请访问以下操作方法指南：

- [ (预览) 的 Python 工作区包： ](./apache-spark-manage-python-packages.md#install-wheel-files) 将 Python 轮文件作为工作区包上传，然后将这些包添加到特定的无服务器 Apache Spark 池。
- [ (预览版的 Scala/Java 工作区包) ： ](./apache-spark-manage-scala-packages.md#workspace-packages) 将 Scala 和 Java jar 文件作为工作区包上传，稍后将这些包添加到特定的无服务器 Apache Spark 池。

## <a name="pool-packages"></a>池包
在某些情况下，你可能想要标准化给定 Apache Spark 池上使用的包集。 如果团队中的多个用户通常都安装了相同的包，则这种标准化会很有用。 

使用 Azure Synapse Analytics 池管理功能，你可以配置要在给定无服务器 Apache Spark 池上安装的默认库集。 这些库在 [基本运行时](./apache-spark-version-support.md)上安装。 

目前，仅支持 Python 的池管理。 对于 Python，Synapse Spark 池使用 Conda 安装和管理 Python 包依赖项。 指定池级库时，现在可以提供 requirements.txt 或 docker-compose.override.yml。 每次从该 Spark 池中创建 Spark 实例时，都会使用此环境配置文件。 

若要了解有关这些功能的详细信息，请访问有关 [Python 池管理](./apache-spark-manage-python-packages.md#pool-libraries)的文档。

> [!IMPORTANT]
> - 如果要安装的包很大或需要很长时间才能安装，这会影响 Spark 实例的启动时间。
> - 不支持更改 PySpark、Python、Scala/Java、.NET 或 Spark 版本。
> - 启用 DEP 的工作区中不支持从 PyPI 安装包。

## <a name="session-scoped-packages"></a>会话作用域的包
通常，在进行交互式数据分析或机器学习时，您可能会发现您要尝试更新的包，或者您的 Apache Spark 池上还没有可用的包。 用户现在可以使用会话范围的包来添加、管理和更新会话依赖项，而无需更新池配置。

会话范围内的包允许用户在其会话开始时定义包依赖关系。 安装会话范围的包时，只有当前会话可以访问指定的包。 因此，这些会话范围的包将不会影响使用同一个 Apache Spark 池的其他会话或作业。 此外，这些库安装在基本运行时和池级别包之上。 

若要了解有关如何管理会话范围的包的详细信息，请访问以下操作方法指南：

- [ (预览版的 Python 会话包) ：](./apache-spark-manage-python-packages.md) 在会话开始时，提供一个 Conda *docker-compose.override.yml* ，以从常用存储库安装其他 Python 包。 
- [Scala/Java 会话包： ](./apache-spark-manage-scala-packages.md) 在会话开始时，提供要使用安装的 jar 文件的列表 ```%%configure``` 。

## <a name="next-steps"></a>后续步骤
- 查看默认库： [Apache Spark 版本支持](apache-spark-version-support.md)
