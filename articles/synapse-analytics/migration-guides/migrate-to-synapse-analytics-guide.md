---
title: Azure Synapse Analytics：迁移指南
description: 根据本指南将数据库迁移到 Azure Synapse Analytics 专用的 SQL 池。
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 5d2dc316d5f58374792e04460f1abd8e63002243
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726043"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>将数据仓库迁移到 Azure Synapse Analytics 中的专用 SQL 池

以下部分概述了将现有数据仓库解决方案迁移到 Azure Synapse Analytics 专用 SQL 池所涉及的内容。

## <a name="overview"></a>概述

开始迁移之前，应验证 Azure Synapse Analytics 是否是最适合你的工作负载的解决方案。 Azure Synapse Analytics 是一种分布式系统，用于对大型数据执行分析。 迁移到 Azure Synapse Analytics 需要一些设计更改，这些更改不难理解，但可能需要一些时间才能实现。 如果业务要求企业级的数据仓库，那么花费一些时间来实现这些优点是值得的。 但是，如果不需要 Azure Synapse Analytics 的功能，则使用 [SQL Server](/sql/sql-server/) 或 [Azure SQL 数据库](../../azure-sql/index.yml)更加经济高效。

如果存在以下情况，请考虑使用 Azure Synapse Analytics：

- 具有 1 TB 或数 TB 的数据。
- 计划对巨量数据运行分析。
- 需要能够缩放计算和存储。
- 想要在不需要计算资源时通过将其暂停来节省成本。

对于存在以下情况的运营 (OLTP) 工作负载，请考虑使用其他选项，而不是 Azure Synapse Analytics：

- 高频率读取和写入。
- 大量的单一实例选择。
- 大量的单行插入。
- 逐行处理需求。
- 不兼容的格式（如 JSON 和 XML）。

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

从一个系统迁移到另一个系统时，客户面临的主要阻碍之一是转换数据库对象。 [Azure Synapse Pathway](/sql/tools/synapse-pathway/azure-synapse-pathway-overview) 通过自动执行现有数据仓库的对象转换，帮助你升级到新式数据仓库平台。 它是一个免费、直观且易于使用的工具，可自动执行代码转换，从而更快地迁移到 Azure Synapse Analytics。

## <a name="prerequisites"></a>必备条件

# <a name="migrate-from-sql-server"></a>[从 SQL Server 迁移](#tab/migratefromSQLServer)

要将 SQL Server 数据仓库迁移到 Azure Synapse Analytics，请确保满足以下先决条件：

- 有一个数据仓库或分析工作负载。
- 下载最新版本的 [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061)，用于将 SQL Server 对象迁移到 Azure Synapse 对象。
- 在 Azure Synapse 工作区中有[专用 SQL 池](../get-started-create-workspace.md)。

# <a name="migrate-from-netezza"></a>[从 Netezza 迁移](#tab/migratefromNetezza)

要将 Netezza 数据仓库迁移到 Azure Synapse Analytics，请确保满足以下先决条件：

- 下载最新版本的 [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061)，用于将 SQL Server 对象迁移到 Azure Synapse 对象。
- 在 Azure Synapse 工作区中有[专用 SQL 池](../get-started-create-workspace.md)。

有关详细信息，请参阅[适用于 Netezza 的 Azure Synapse Analytics 解决方案和迁移](/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza)。

# <a name="migrate-from-snowflake"></a>[从 Snowflake 迁移](#tab/migratefromSnowflake)

要将 Snowflake 数据仓库迁移到 Azure Synapse Analytics，请确保满足以下先决条件：

- 下载最新版本的 [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=103061)，用于将 Snowflake 对象迁移到 Azure Synapse 对象。
- 在 Azure Synapse 工作区中有[专用 SQL 池](../get-started-create-workspace.md)。

# <a name="migrate-from-oracle"></a>[从 Oracle 迁移](#tab/migratefromOracle)

要将 Oracle 数据仓库迁移到 Azure Synapse Analytics，请确保满足以下先决条件：

- 有一个数据仓库或分析工作负载。
- 下载适用于 Oracle 的 SQL Server 迁移助手，以将 Oracle 对象转换为 SQL Server。 有关详细信息，请参阅[将 Oracle 数据库迁移到 SQL Server (OracleToSQL)](/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql)。
- 下载最新版本的 [Azure Synapse Pathway](https://www.microsoft.com/download/details.aspx?id=103061)，用于将 SQL Server 对象迁移到 Azure Synapse 对象。
- 在 Azure Synapse 工作区中有[专用 SQL 池](../get-started-create-workspace.md)。

有关详细信息，请参阅[适用于 Oracle 数据仓库的 Azure Synapse Analytics 解决方案和迁移](/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata)。

---

## <a name="pre-migration"></a>预迁移

决定将现有解决方案迁移到 Azure Synapse Analytics 后，需要在开始前对迁移进行规划。 规划的主要目的是确保数据、表架构以及代码与 Azure Synapse Analytics 兼容。 当前系统与 Azure Synapse Analytics 之间存在一些需要解决的兼容性差异问题。 此外，将大量数据迁移到 Azure 需要时间。 仔细规划可加快将数据迁移到 Azure 的过程。

规划的另一个重要目的是调整设计，以确保解决方案充分利用 Azure Synapse Analytics 可提供的高查询性能。 由于为了实现缩放性而设计数据仓库时会引入独特的设计模式，因此传统的方法不一定最合适。 虽然在迁移后可以进行某些设计调整，但是在过程中尽早进行更改可以为以后的操作节省时间。

## <a name="migrate"></a>Migrate

执行成功的迁移需要迁移表架构、代码和数据。 有关这些主题的更详细指导，请参阅以下文章：

- [考虑表设计](../sql-data-warehouse/sql-data-warehouse-tables-overview.md)
- [考虑代码更改](../sql-data-warehouse/sql-data-warehouse-overview-develop.md#development-recommendations-and-coding-techniques)
- [迁移数据](../sql-data-warehouse/design-elt-data-loading.md)
- [考虑工作负载管理](../sql-data-warehouse/sql-data-warehouse-workload-management.md)

## <a name="more-resources"></a>更多资源

客户咨询团队具有一些出色的 Azure Synapse Analytics（以前称为 Azure SQL 数据仓库）指导，以博客文章的形式发布。 有关迁移的详细信息，请参阅[在实践中将数据迁移到 Azure SQL 数据仓库](/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)。

## <a name="migration-assets-from-real-world-engagements"></a>实际操作中的迁移资产

若要获得完成此迁移方案的更多帮助，请参阅以下资源。 这些资源是为支持实际迁移项目而开发的。

| 标题/链接                              | 说明                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [数据工作负载评估模型和工具](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 此工具为给定工作负载提供建议的“最适合”目标平台、云就绪性和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。 |
| [处理在将数据加载到 Azure Synapse Analytics 时遇到的数据编码问题](https://azure.microsoft.com/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | 这篇博客文章旨在提供有关在使用 PolyBase 将数据加载到 SQL 数据仓库时可能遇到的一些数据编码问题的见解。 此文还提供了一些选项，可用于解决此类问题并成功加载数据。 |
| [在 Azure Synapse Analytics 专用 SQL 池中获取表大小](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | 架构师必须执行的一项关键任务是获取有关迁移后的新环境的指标。 例如，收集从本地到云的加载时间并收集 PolyBase 加载时间。 其中最重要的一项任务是确定 SQL 数据仓库中的存储大小情况（与客户当前平台相比）。 |
| [将本地 SQL Server 登录名迁移到 Azure Synapse Analytics 的实用程序](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | PowerShell 脚本可以创建 T-SQL 命令脚本，以重新创建登录名并从 SQL Server 的本地实例到 Azure SQL 平台即服务 (PaaS) 服务选择数据库用户。 使用该工具可将 Windows Server Active Directory 帐户自动映射到 Azure Active Directory 帐户，或者根据本地 Windows Server Active Directory 对每个登录名执行 UPN 查找。 该工具还可以选择性地移动 SQL Server 本机登录名。 对于自定义服务器和数据库角色以及角色成员资格、数据库角色和用户权限，均已编写脚本。 目前不支持所包含的数据库，并且仅对一部分可能的 SQL Server 权限进行脚本编写。 支持文档中提供了更多信息，该脚本带有注释，以便于理解。 |

数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft 的 Azure 数据平台的数据平台迁移项目的复杂现代化进程。

## <a name="videos"></a>视频

观看 [Walgreens 如何迁移其零售库存系统](https://www.youtube.com/watch?v=86dhd8N1lH4)，在创纪录的时间内将大约 100TB 的数据从 Netezza 迁移到 Azure Synapse Analytics。
