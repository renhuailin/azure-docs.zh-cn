---
title: 生成集成的解决方案
description: 与 Azure Synapse Analytics 中的专用 SQL 池（之前称为 SQL DW）集成的解决方案工具和合作伙伴。
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
ms.openlocfilehash: 3e55ef054d5c305937f88d6ec5b2b4453cac6792
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117752"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>将其他服务与 Azure Synapse Analytics 中的专用 SQL 池（之前称为 SQL DW）集成。

借助 Azure Synapse Analytics 中的专用 SQL 池（之前称为 SQL DW）功能，用户能够与 Azure 中的许多其他服务集成。 专用 SQL 池可以利用一些其他服务，其中包括：

* Power BI
* Azure 数据工厂
* Azure 机器学习
* Azure 流分析

有关跨 Azure 的集成服务的详细信息，请查看[集成合作伙伴](sql-data-warehouse-partner-data-integration.md)文章。

## <a name="power-bi"></a>Power BI

借助 Power BI 集成，可以结合数据仓库的计算能力以及 Power BI 的动态报告和视觉效果。 Power BI 集成当前包括：

* **直接连接**：使用逻辑下推与使用专用 SQL 池（之前称为 SQL DW）预配的数据仓库建立更高级的连接。 下推提供更快且更大规模的分析。
* **在 Power BI 中打开**：“在 Power BI 中打开”按钮将实例信息传递给 Power BI，以简化连接方式。

有关详细信息，请参阅[与 Power BI 集成](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)或 [Power BI 文档](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)。

## <a name="azure-data-factory"></a>Azure 数据工厂

Azure 数据工厂为用户提供一个托管平台，用于创建复杂的提取和加载管道。 专用 SQL 池（之前称为 SQL DW）与 Azure 数据工厂的集成包括：

* **存储过程**：协调存储过程的执行。
* **复制**：使用 ADF 将数据移动到专用 SQL 池（之前称为 SQL DW）。 实际上，此操作可以使用 ADF 标准数据移动机制或封面下的 PolyBase。

有关详细信息，请参阅[与 Azure 数据工厂集成](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

## <a name="azure-machine-learning"></a>Azure 机器学习

Azure 机器学习是完全托管的分析服务，因此你可以使用大量预测工具创建复杂的模型。 支持将专用 SQL 池（之前称为 SQL DW）用作这些模型的源和目标，该池具有以下功能：

* **读取数据：** 使用 T-SQL 针对专用 SQL 池（之前称为 SQL DW）大规模驱动模型。
* **写入数据：** 将任一模型中的更改提交回专用 SQL 池（之前称为 SQL DW）。

有关详细信息，请参阅[与 Azure 机器学习集成](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)。

## <a name="azure-stream-analytics"></a>Azure 流分析

Azure 流分析是复杂、完全托管的基础结构，用于处理和使用从 Azure 事件中心生成的事件数据。  通过与专用 SQL 池（之前称为 SQL DW）集成，可以有效地处理流数据，并将其与关系数据一起存储以实现更深入、更高级的分析。  

* **作业输出：** 将流分析作业的输出直接发送到专用 SQL 池（之前称为 SQL DW）。

有关详细信息，请参阅[与 Azure 流分析集成](sql-data-warehouse-integrate-azure-stream-analytics.md)。