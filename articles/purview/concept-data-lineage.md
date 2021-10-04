---
title: Azure Purview 中的数据世系
description: 介绍数据世系的概念。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 37cffe89fc0e0cb10aaadbb8d63f9aa12d0589eb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207443"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Azure Purview 数据目录客户端中的数据世系

本文概述了 Azure Purview 数据目录中的数据世系。 它还详细说明了数据系统如何与目录集成以捕获数据世系。 Purview 可以捕获组织数据资产不同部分以及不同准备级别的数据世系，包括：

- 来自各种平台暂存的完整原始数据
- 转换和准备的数据
- 可视化平台使用的数据。

## <a name="use-cases"></a>用例

数据世系被广泛地理解为生命周期，它跨越数据的起源，并随着时间的推移在数据资产中移动。 它用于不同种类的反向场景，如故障排除、跟踪数据管道中的根本原因和调试。 世系还用于数据质量分析、合规性和通常称为影响分析的“what if”场景。 世系以可视化的方式表示，以显示从源到目标的数据移动，包括数据是如何转换的。 考虑到大多数企业数据环境的复杂性，如果不对外围数据点进行整合或过滤，这些视图可能很难理解。

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Azure Purview 数据目录中的世系体验

Purview 数据目录将与其他数据处理、存储和分析系统连接，以提取世系信息。 这些信息组合在一起，表示目录中通用的、特定于场景的世系体验。

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="端到端世系显示从 Blob 存储一直复制到 Power BI 仪表板的数据":::

数据资产可能包括执行数据提取、转换（ETL/ELT 系统）、分析的系统和可视化系统。 每个系统都会捕获丰富的静态和操作元数据，用于描述系统边界内数据的状态和质量。 数据目录中世系的目标是以尽可能低的粒度从每个数据系统中提取移动、转换和操作元数据。

下面的示例是在多个系统中移动数据的典型用例，其中数据目录将连接到每个系统以用于世系。

- 数据工厂将数据从本地/原始区域复制到云中的登陆区域。 
- 数据处理系统（如 Synapse、Databricks）将使用笔记本处理数据，并将其从登陆区域转换为特选区域。
- 进一步将数据处理为分析模型，以获得最佳查询性能和聚合。 
- 数据可视化系统将使用数据集并通过其元模型进行处理，以创建 BI 仪表板、ML 试验等。

## <a name="lineage-granularity"></a>世系粒度

以下部分介绍有关 Purview 收集世系信息的粒度的详细信息。 此粒度可能因 Purview 中支持的数据系统而异。

### <a name="entity-level-lineage-sources--process--targets"></a>实体级别世系：源 > 进程 > 目标 

- 世系以图表示，通常它包含数据存储系统中的源和目标实体，这些实体由计算系统调用的进程连接。 
- 数据系统连接到数据目录以生成和报告引用基础数据系统的物理对象的唯一对象，例如 SQL 存储过程、笔记本等。
- 将捕获具有其他元数据（如所有权）的高保真世系，以用户可读的格式显示源和目标实体的世系。 例如 hive 表级别的世系，而不是分区或文件级别的世系。

### <a name="column-or-attribute-level-lineage"></a>列或属性级别世系

标识用于在目标实体中创建或派生属性的源实体的属性。 可以在目标中保留源属性的名称或对其重命名。 ADF 这样的系统可以执行从本地环境到云的一对一复制。 例如：`Table1/ColumnA -> Table2/ColumnA`。

### <a name="process-execution-status"></a>进程执行状态

为了支持根本原因分析和数据质量方案，我们在数据处理系统中捕获作业的执行状态。 此要求与替换其他数据处理系统的监视功能无关，其目的也不是替换它们。 

## <a name="summary"></a>总结

世系是 Purview 数据目录的一项重要功能，用于支持质量、信任和审核方案。 数据目录的目标是构建一个可靠的框架，在这个框架中，环境中的所有数据系统都可以自然地连接并报告世系。 元数据可用后，数据目录可以将数据系统提供的元数据组合在一起以支持数据治理用例。

## <a name="next-steps"></a>后续步骤

* [快速入门：在 Azure 门户中创建 Azure Purview 帐户](create-catalog-portal.md)
* [快速入门：使用 Azure PowerShell/Azure CLI 创建 Azure Purview 帐户](create-catalog-powershell.md)
* [使用 Purview Studio](use-purview-studio.md)
