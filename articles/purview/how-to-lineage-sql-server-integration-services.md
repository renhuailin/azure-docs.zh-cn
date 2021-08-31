---
title: 来自 SQL Server Integration Services 的世系
description: 本文介绍如何从 SQL Server Integration Services 提取数据世系。
author: chugugrace
ms.author: chugu
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: aa4fb6b7870831d0f24b0a7071cbf0dfd2431c14
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294554"
---
# <a name="how-to-get-lineage-from-sql-server-integration-services-ssis-into-azure-purview"></a>如何将来自 SQL Server Integration Services (SSIS) 的世系纳入 Azure Purview

本文详细阐述 Azure Purview 中 SQL Server Integration Services (SSIS) 的数据世系方面的内容。

## <a name="prerequisites"></a>先决条件

- [将 SQL Server Integration Services 工作负荷直接迁移到云](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)

## <a name="supported-scenarios"></a>支持的方案

当前支持范围包括从 Azure 数据工厂 SSIS integration runtime 所执行的 SSIS 包提取世系。

尚不支持本地 SSIS 世系提取。

### <a name="supported-data-stores"></a>支持的数据存储

| 数据存储 | 支持 |
| ------------------- | ------------------- |
| Azure Blob 存储 | 是 |
| Azure Data Lake Storage Gen1 | 是 |
| Azure Data Lake Storage Gen2 | 是 |
| Azure 文件存储 | 是 |
| Azure SQL 数据库 \* | 是 |
| Azure SQL 托管实例 \*| 是 |
| Azure Synapse Analytics \* | 是 |
| SQL Server \* | 是 |

\* Azure Purview 当前不支持针对世系或扫描的查询或存储过程。世系仅限于表和视图源。


## <a name="how-to-bring-ssis-lineage-into-purview"></a>如何将 SSIS 世系引入 Purview

### <a name="step-1-connect-a-data-factory-to-azure-purview"></a>步骤 1。 [将数据工厂连接到 Azure Purview](how-to-link-azure-data-factory.md)

### <a name="step-2-trigger-ssis-activity-execution-in-azure-data-factory"></a>步骤 2. 在 Azure 数据工厂中触发 SSIS 活动执行

可以[使用执行 SSIS 包活动运行 SSIS 包](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)，或者[使用 ADF SSIS Integration Runtime 中的 Transact-SQL 运行 SSIS 包](../data-factory/how-to-invoke-ssis-package-stored-procedure-activity.md)。  

执行 SSIS 包活动完成执行后，可以从[数据工厂活动监视器](../data-factory/monitor-visually.md#monitor-activity-runs)中的活动输出中检查世系报告状态。
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/activity-report-lineage-status.png" alt-text="ssis-status":::

### <a name="step-3-browse-lineage-information-in-your-azure-purview-account"></a>步骤 3. 在 Azure Purview 帐户中浏览世系信息

- 可以通过选择资产类型“SQL Server Integration Services”来浏览数据目录。

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png" alt-text="浏览器资产" lightbox="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-2.png" alt-text="browser-assets-ssis":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-3.png" alt-text="browse-assets-ssis-package":::

- 也可以使用关键字来搜索数据目录。

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/search-assets.png" alt-text="search-ssis" lightbox="media/how-to-lineage-sql-server-integration-services/search-assets.png":::

- 可以查看 SSIS 执行包活动的世系信息，并在数据工厂中打开以查看/编辑活动设置。

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage.png" alt-text="show-ssis-lineage" lightbox="media/how-to-lineage-sql-server-integration-services/lineage.png":::

- 可以选择一个数据源来深入了解源中的列如何映射到目标中的列。

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png" alt-text="show-ssis-lineage-drill-in" lightbox="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png":::

## <a name="next-steps"></a>后续步骤

- [将 SQL Server Integration Services 工作负荷直接迁移到云](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [了解 Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
- [将 Azure 数据工厂链接到推送自动世系](how-to-link-azure-data-factory.md)