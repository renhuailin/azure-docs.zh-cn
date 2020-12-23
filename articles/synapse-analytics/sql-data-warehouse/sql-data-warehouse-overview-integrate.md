---
title: 生成集成的解决方案
description: 与专用 SQL 池集成的解决方案工具和合作伙伴 (以前的 SQL DW) 在 Azure Synapse Analytics 中。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2afc274bf7c040eca6a83abbab24c41767f16482
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453672"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>将其他服务与 Azure Synapse Analytics 中以前的 SQL DW)  (专用 SQL 池集成。

在 Azure Synapse Analytics 中，专用 SQL 池 (以前的 SQL DW) 功能，使用户能够与 Azure 中的其他许多服务集成。 专用的 SQL 池可以使用几个附加服务，其中一些服务包括：

* Power BI
* Azure 数据工厂
* Azure 机器学习
* Azure 流分析

有关跨 Azure 的集成服务的详细信息，请查看[集成合作伙伴](sql-data-warehouse-partner-data-integration.md)文章。

## <a name="power-bi"></a>Power BI

借助 Power BI 集成，可以结合数据仓库的计算能力以及 Power BI 的动态报告和视觉效果。 Power BI 集成当前包括：

* **直接连接**：对于使用专用 sql DW)  (预配的数据仓库，使用逻辑下推的更高级连接。 下推提供更快且更大规模的分析。
* **在 Power BI 中打开**：“在 Power BI 中打开”按钮将实例信息传递给 Power BI，以简化连接方式。

有关详细信息，请参阅[与 Power BI 集成](sql-data-warehouse-get-started-visualize-with-power-bi.md)或 [Power BI 文档](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)。

## <a name="azure-data-factory"></a>Azure 数据工厂

Azure 数据工厂为用户提供一个托管平台，用于创建复杂的提取和加载管道。 专用 SQL 池 (以前的 SQL DW) 与 Azure 数据工厂的集成包括：

* **存储过程**：协调存储过程的执行。
* **复制**：使用 ADF 将数据移动到专用 sql 池 (以前的 sql DW) 。 实际上，此操作可以使用 ADF 标准数据移动机制或封面下的 PolyBase。

有关详细信息，请参阅[与 Azure 数据工厂集成](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

## <a name="azure-machine-learning"></a>Azure 机器学习

Azure 机器学习是完全托管的分析服务，因此你可以使用大量预测工具创建复杂的模型。 专用 SQL 仓库 (以前的 SQL DW) 作为这些模型的源和目标都受支持，并且具有以下功能：

* **读取数据：** 使用 T-sql 对专用 SQL DW) 的专用 SQL (池使用 T-sql 进行大规模驱动模型。
* **写入数据：** (以前的 SQL DW) ，将任何模型中的更改提交回专用 SQL 池。

有关详细信息，请参阅[与 Azure 机器学习集成](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)。

## <a name="azure-stream-analytics"></a>Azure 流分析

Azure 流分析是复杂、完全托管的基础结构，用于处理和使用从 Azure 事件中心生成的事件数据。  与 (以前的 SQL DW) 的专用 SQL 池集成，可以有效地处理流数据，并将其与关系数据一起存储，以实现更深入、更高级的分析。  

* **作业输出：** 将流分析作业的输出直接发送到专用 SQL 池 (以前的 SQL DW) 。

有关详细信息，请参阅[与 Azure 流分析集成](sql-data-warehouse-integrate-azure-stream-analytics.md)。
