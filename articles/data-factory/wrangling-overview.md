---
title: Azure 数据工厂中的数据整理
description: Azure 数据工厂中的数据整理概述
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f922e7a2755a6e26a0d9f93f2668753e2f4dad5a
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98738163"
---
# <a name="what-is-data-wrangling"></a>什么是数据整理？

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

组织需要能够浏览其关键的业务数据，以进行数据准备和整理，以便能够准确分析每日持续增长的复杂数据。 需要进行数据准备，以便组织可以使用各种业务流程中的数据并缩短价值。

数据工厂使用 Power Query 以迭代规模为您提供无代码的数据准备。 数据工厂与 [Power Query Online](/power-query/) 集成，使 Power Query M 函数作为管道活动提供。

数据工厂通过将 M 转换为 Azure 数据工厂数据流，将 Power Query Online 混合编辑器生成的 M 转换为 spark 代码，以便云规模执行。 整理 Power Query 数据和数据流的数据对数据工程师或 "公民数据集成商" 特别有用。

> [!NOTE]
> Azure 数据工厂中的 Power Query 活动当前在公共预览版中提供

## <a name="use-cases"></a>用例

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>快速交互式数据探索和准备

多个数据工程师和公民数据集成商可以在云规模上以交互方式浏览和准备数据集。 由于数据 lake 中的数据量、种类和速度的增长，用户需要一种有效的方法来浏览和准备数据集。 例如，你可能需要创建一个数据集，该数据集包含自2017以来为新客户提供的所有客户人口统计信息。 你不会映射到已知目标。 在 lake 中发布之前，要浏览、整理和准备数据集，以满足要求。 整理通常用于不太正式的分析方案。 准备好数据集可用于执行转换和机器学习操作下游。

### <a name="code-free-agile-data-preparation"></a>无代码敏捷数据准备

公民数据集成商花费超过60% 的时间来查找和准备数据。 他们希望以一种代码免费的方式来提高运营效率。 允许公民数据集成商使用已知的工具（如 Power Query 以可缩放的方式在线）来丰富、调整和发布数据，大大提高了工作效率。 Azure 数据工厂中的整理支持熟悉的 Power Query Online 混合编辑器，使公民数据集成人员能够快速修复错误，使数据标准化，并生成高质量的数据，以支持商业决策。

### <a name="data-validation-and-exploration"></a>数据验证和探索

以无代码方式浏览数据，以删除任何离群值和异常，并将其与用于快速分析的形状相符。

## <a name="supported-sources"></a>受支持的源

| 连接器 | 数据格式 | 身份验证类型 |
| -- | -- | --|
| [Azure Blob 存储](connector-azure-blob-storage.md) | CSV，Parquet | 帐户密钥 |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Service Principal |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV，Parquet | 帐户密钥、服务主体 |
| [Azure SQL 数据库](connector-azure-sql-database.md) | - | SQL 身份验证 |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 身份验证 |

## <a name="the-mashup-editor"></a>混合编辑器

创建 Power Query 活动时，所有源数据集都将成为数据集查询，并放置在 **ADFResource** 文件夹中。 默认情况下，UserQuery 将指向第一个数据集查询。 所有转换都应在 UserQuery 上完成，因为不支持对数据集查询进行更改，也不会将其保留。 当前不支持重命名、添加和删除查询。

![整理](media/wrangling-data-flow/editor.png)

当前并非所有 Power Query M 函数都支持数据整理，但在创作期间可用。 在构建 Power Query 活动时，如果函数不受支持，系统将提示您提供以下错误消息：

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

有关支持的转换的详细信息，请参阅 [data 整理](wrangling-functions.md)function。

## <a name="next-steps"></a>后续步骤

了解如何 [创建整理 Power Query 混合的数据](wrangling-tutorial.md)。
