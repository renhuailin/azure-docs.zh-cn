---
title: 监控范围连接器概述
description: 本文概述在监控范围中支持的不同数据存储和功能
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 88fb9c823df6ae5df345911ccce1c579009fba02
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "96780206"
---
# <a name="supported-data-stores"></a>支持的数据存储

监控范围支持以下数据存储。 单击每个数据存储以了解详细信息中支持的功能和相应的配置。

## <a name="purview-data-sources"></a>监控范围数据源

|**类别**|  **数据存储**  |**元数据提取**|**完整扫描**|**增量扫描**|**作用域扫描**|**分类**|**沿袭**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob 存储](register-scan-azure-blob-storage-source.md)| 是| 是| 是| 是| 是| 是|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|是| 是| 是| 是| 是| 是|
||[Azure 数据资源管理器](register-scan-azure-data-explorer.md)|是| 是| 是| 是| 是| 是|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|是| 是| 是| 是| 是| 是|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|是| 是| 是| 是| 是| 是|
||[Azure SQL 数据库](register-scan-azure-sql-database.md)|是| 是| 否| 是| 是| 是|
||[Azure SQL 数据库托管实例](register-scan-azure-sql-database-managed-instance.md)|是| 是| 否| 是| 是| 是|
||[Azure Synapse Analytics（以前称为 SQL DW）](register-scan-azure-synapse-analytics.md)|是| 是| 否| 是| 是| 是|
|数据库|[SQL Server](register-scan-on-premises-sql-server.md)|是| 是| 否| 是| 是| 是|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|是| 是| 否| 否| 否| 是|

## <a name="next-steps"></a>后续步骤

- [注册并扫描 Azure Blob 存储源](register-scan-azure-blob-storage-source.md)