---
title: 专用 SQL 池 (以前的 SQL DW) 常见问题
description: 本文列出了有关从客户和开发人员的 Azure Synapse 分析中的专用 SQL DW)  (以前的常见问题。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: c8f8ae81f6f030245899ec82dbe16b29846dab23
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460519"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-frequently-asked-questions"></a>专用 SQL 池 (以前的 SQL DW) Azure Synapse Analytics 常见问题解答

## <a name="general"></a>常规

问： 什么是 Azure Synapse？

A. Azure Synapse 是一种分析服务，它将数据仓库和大数据分析结合在一起。 Azure Synapse 将这两个世界结合起来，同时提供了针对 BI 和机器学习需求引入、准备、管理和提供数据的统一体验。 有关详细信息，请参阅[什么是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

问： Azure SQL 数据仓库有什么变化？

A. Azure Synapse 是 Azure SQL 数据仓库的演进版。 我们将同一个行业领先的数据仓库提升到了一个全新的性能和功能级别。 你可以继续在生产中运行现有数据仓库工作负荷，其中包含 Azure Synapse 中以前的 SQL DW)  (。 有关详细信息，请参阅[什么是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

问： 什么是 Azure Synapse Analytics 中 (以前的 SQL DW) 的专用 SQL 池？

A. 专用 SQL DW (以前的 SQL DW) 是指在 Azure Synapse 中正式发布的企业数据仓库功能。 有关详细信息，请参阅[什么是 Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)。

问： Azure Synapse 如何入门？

A. 若要[了解详细信息](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)，可以开始使用[Azure 免费帐户](https://azure.microsoft.com/free/sql-data-warehouse/)或联系销售额。

问： Azure Synapse 提供哪些功能来确保数据安全性？

A. Azure Synapse 提供多种解决方案来保护数据，例如 TDE 和审核。 有关详细信息，请参阅[安全性](sql-data-warehouse-overview-manage-security.md)。

问： 从何处可以查明 Azure Synapse 符合哪些法律或企业标准？

A. 请访问 [Microsoft 符合性](https://www.microsoft.com/trustcenter/compliance/complianceofferings)页面，查明产品（如 SOC 和 ISO）的各种符合性规定。 首先，选择 "符合性标题"。 然后在页面右侧的 "Microsoft 范围内云服务" 部分中展开 "Azure"，以了解哪些服务符合 Azure Synapse。

问： 是否可以连接 Power BI？

A. 能！ 尽管 Power BI 支持使用 Azure Synapse 进行直接查询，但不适合大量用户或实时数据。 若要进一步优化 Power BI 性能，请考虑在 Azure Analysis Services 或 Analysis Service IaaS 的顶层使用 Power BI。

问： 什么是专用 SQL 池 (以前的 SQL DW) 容量限制？

A. 请参阅当前[容量限制](sql-data-warehouse-service-capacity-limits.md)页。

问： 为什么缩放/暂停/恢复会花费很长时间？

A. 多种因素可能会影响计算管理操作的时间。 长时间运行的操作的常见例子是事务回退。 缩放或暂停操作启动时，会阻止所有传入会话和查询。 为了使系统处于稳定状态，必须在操作开始前回退事务。 事务数量越多，其日志大小越大，使系统恢复到稳定状态的操作耗时越久。

## <a name="user-support"></a>用户支持

问： 在何处提交功能请求？

A. 如果有功能请求，请在 [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) 页上提交

问： 该如何操作？

A. 若要获得有关使用 Azure Synapse 开发的帮助，可在 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) 页上提问。

问： 如何提交支持票证？

A. 可通过 Azure 门户提交[支持票证](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="sql-languagefeature-support"></a>SQL 语言/功能支持

问： 支持哪些数据类型？

A. 请参阅[数据类型](sql-data-warehouse-tables-data-types.md)。

问： 支持哪些表功能？

A. 支持许多功能。 不支持的功能可在[不支持的表功能](sql-data-warehouse-tables-data-types.md)中找到。

## <a name="tooling-and-administration"></a>工具和管理

问： 专用 SQL 池 (以前的 SQL DW) 是否支持 REST Api？

A. 是的。 与 SQL 数据库一起使用的大多数 REST 功能还可用于专用 SQL 池 (以前的 SQL DW) 。 可以在 REST 文档页或[数据库](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中找到 API 信息。

## <a name="loading"></a>加载

问： 支持哪些客户端驱动程序？

A.  (以前的 SQL DW) 的专用 SQL 池的驱动程序支持，可以在 " [连接字符串](sql-data-warehouse-connection-strings.md) " 页上找到

问：PolyBase 支持哪些文件格式？

答：Orc、RC、Parquet 和带分隔符的平面文本

问：使用 PolyBase 可以连接到哪些数据源？

答： [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) 和 [Azure 存储 blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

问：连接 Azure 存储 Blob 或 ADLS 时能否进行计算下推？

答：不能，PolyBase 仅与存储组件交互。

问：能否连接到 HDI？

答：HDI 可使用 ADLS 或 WASB 作为 HDFS 层。 如果你有一个作为 HDFS 层，可以将该数据加载到 (以前的 SQL DW) 的专用 SQL 池中。 但是，无法生成 HDI 实例的下推计算。

## <a name="next-steps"></a>后续步骤

有关专用 SQL 池 (以前的 SQL DW) 在 Azure Synapse 中的详细信息，请参阅 [概述](sql-data-warehouse-overview-what-is.md) 页。
