---
title: 'Azure 监控范围中的数据沿袭 (预览) '
description: 介绍数据沿袭的概念。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: ceb30cee194994e5b31db312f191194932c937b4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551724"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Azure 监控范围数据目录客户端中的数据沿袭

本文概述了 Azure 监控范围数据目录中的数据沿袭。 它还详细说明了数据系统如何与目录集成以捕获数据沿袭。 监控范围可以捕获组织数据场所不同部分的数据沿袭，还可以获得不同的准备级别，包括：

- 从各种平台暂存的完全原始数据
- 转换和准备的数据
- 可视化平台使用的数据。

## <a name="use-cases"></a>用例

数据沿袭被广泛理解为跨越数据源的生命周期，以及在数据区域中跨时间移动数据的生命周期。 它用于不同种类的反向方案，如故障排除、跟踪数据管道中的根本原因和调试。 沿袭还用于数据质量分析、合规性和 "假设假设" 方案通常称为影响分析。 沿袭以视觉方式表示，以显示从源到目标的数据移动，包括如何转换数据。 由于大多数企业数据环境的复杂性，这些视图很难理解，无需对外围数据点进行一些合并或屏蔽。

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Azure 监控范围数据目录中的沿袭体验

监控范围数据目录将与其他数据处理、存储和分析系统连接，以提取沿袭信息。 此信息组合在一起，以表示目录中特定于方案的一般沿袭体验。

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="端沿袭显示从 blob 存储复制的数据，一直到 Power BI 仪表板":::

数据空间可能包括执行数据提取、转换 (ETL/ELT 系统) 、分析和可视化系统）的系统。 每个系统都会捕获丰富的静态和操作元数据，用于描述系统边界内数据的状态和质量。 数据目录中的沿袭目的是从每个数据系统中尽可能地提取移动、转换和操作元数据。

下面的示例是在多个系统中移动数据的典型用例，其中数据目录将连接到每个系统进行沿袭。

- 数据工厂将数据从本地/原始区域复制到云中的登录区域。 
- 数据处理系统（如 Synapse、Databricks）使用笔记本处理数据，并将其从登陆区域转换为特选区域。
- 进一步将数据处理为分析模型，以获得最佳查询性能和聚合。 
- 数据可视化系统将使用数据集并通过其元模型进行处理，以创建 BI 仪表板、ML 试验等。

## <a name="lineage-granularity"></a>沿袭粒度

本节介绍有关数据目录收集沿袭信息的粒度的详细信息。 此粒度可能因数据系统而异。

### <a name="entity-level-lineage-sources--process--targets"></a>实体级别沿袭：源 (s) > 进程 > 目标 (s)  

- 沿袭表示为图形，通常它包含数据存储系统中的源和目标实体，这些实体是由计算系统调用的进程连接的。 
- 数据系统连接到数据目录以生成和报告引用基础数据系统的物理对象的唯一对象，例如： SQL 存储过程、笔记本等。
- 将捕获具有更高的元数据（如所有权）的高保真沿袭，以用户可读的格式显示沿袭 & 目标实体的沿袭。 例如： hive 表级别的沿袭，而不是分区或文件级别。

### <a name="column-or-attribute-level-lineage"></a>列或属性级别沿袭

标识用于在目标实体中创建或派生属性 () 的源实体的属性 () 。 源属性的名称可以在目标中保留或重命名。 ADF 这样的系统就可以从本地环境到云执行一对一复制。 例如：`Table1/ColumnA -> Table2/ColumnA`。

### <a name="process-execution-status"></a>进程执行状态

为了支持根本原因分析和数据质量方案，我们在数据处理系统中捕获作业的执行状态。 此要求与替换其他数据处理系统的监视功能无关，目的是替换它们。 

## <a name="summary"></a>总结

沿袭是监控范围数据目录的一项重要功能，用于支持质量、信任和审核方案。 数据目录的目标是构建一个强健的框架，其中，环境中的所有数据系统都可以自然连接和报告沿袭。 元数据可用后，数据目录可以将数据系统提供的元数据组合在一起以实现数据管理用例。

## <a name="next-steps"></a>后续步骤

* [快速入门：在 Azure 门户中创建 Azure 监控范围帐户](create-catalog-portal.md)
* [快速入门：使用 Azure PowerShell/Azure CLI 创建 Azure 监控范围帐户](create-catalog-powershell.md)
* [快速入门：使用监控范围 Studio](use-purview-studio.md)