---
title: Azure Synapse Analytics 笔记本概述
description: 本文概述了可通过 Azure Synapse Analytics 笔记本使用的功能。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 5efdfcea7b6d727038bad124368179b9395bfafd
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093271"
---
# <a name="azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 笔记本

Synapse Studio 笔记本是一个 web 界面，用于创建包含实时代码、可视化效果和叙述性文本的文件。 笔记本是验证想法并使用快速试验从数据中获取见解的好地方。 

借助 Azure Synapse Studio 笔记本，可以：

* 无需设置即可开始工作。
* 利用内置企业安全功能确保数据安全。
* 针对 Spark 和 SQL，分析跨原始格式（CSV、txt、JSON 等）、已处理的文件格式（parquet、Delta Lake、ORC 等）以及 SQL 表格数据文件的数据。
* 利用增强的创作功能和内置的数据可视化功能提高工作效率。

本部分包含有关混合语言、创建数据可视化、参数化笔记本、构建管道等内容的文章。 它还包含有关如何开始进行笔记本开发的参考和教程。

## <a name="create-manage-and-use-notebooks"></a>创建、管理和使用笔记本
你可以使用 Synapse Studio UI 管理笔记本。 

若要了解有关如何创建和管理笔记本的详细信息，请参阅以下文章：
  - 管理笔记本
    - [创建笔记本](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [开发笔记本](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [将数据引入笔记本](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [使用幻命令和临时表使用多种语言](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [使用单元格幻命令](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - 开发
    - [配置 Spark 会话设置](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [使用 Microsoft Spark 实用工具](./spark/../microsoft-spark-utilities.md)
    - [使用笔记本和库实现数据的可视化效果](./spark/../apache-spark-data-visualization.md)
    - [将笔记本集成到管道中](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>后续步骤
笔记本还广泛用于数据准备、数据可视化、机器学习和其他大数据方案。 若要详细了解如何将笔记本用于数据分析和大数据方案，请访问以下教程：
  - [创建笔记本](./spark/../../quickstart-apache-spark-notebook.md)
  - [使用 Synapse Studio 笔记本创建可视化效果](./spark/../apache-spark-data-visualization-tutorial.md)
  - [Apache Spark MLlib 生成机器学习模型](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [用 Azure 自动 ML 生成机器学习模型](./spark/../apache-spark-azure-machine-learning-tutorial.md)