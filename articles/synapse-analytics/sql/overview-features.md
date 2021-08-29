---
title: Synapse SQL 池中的 T-SQL 功能
description: Synapse SQL 中提供的 Transact-SQL 功能列表。
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 73f1eaebb60d02e27be5585a0287a702abf6df2a
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121860959"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Azure Synapse SQL 中支持的 Transact-SQL 功能

Azure Synapse SQL 是一个大数据分析服务，可让你使用 T-SQL 语言查询和分析数据。 可以使用对 SQL Server 和 Azure SQL 数据库使用的 SQL 语言的、符合 ANSI 规范的标准方言进行数据分析。 

Transact-SQL 语言在无服务器 SQL 池中使用，专用模型可以引用不同的对象，但在支持的功能集方面存在一些差异。 本页概要描述了 Synapse SQL 的使用模型之间的 Transact-SQL 语言差异。

## <a name="database-objects"></a>数据库对象

借助 Synapse SQL 中的使用模型，可以使用不同的数据库对象。 下表显示了受支持对象类型的比较：

|   | 专用 | 无服务器 |
| --- | --- | --- |
| **表** | [是](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | 否，无服务器模型只能查询 [Azure 存储](#storage-options)中的外部数据 |
| **视图** | [是](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true) 视图可以使用在专用模型中可用的[查询语言元素](#query-language)。 | [是](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true) 视图可以使用在无服务器模型中可用的[查询语言元素](#query-language)。 |
| **架构** | [是](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [是](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| **临时表** | [是](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?context=/azure/synapse-analytics/context/context) | 否 |
| **过程** | [是](/sql/t-sql/statements/create-procedure-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 是 |
| **函数** | [是](/sql/t-sql/statements/create-function-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | 是，仅限内联表值函数。 |
| **触发器** | 否 | 否 |
| **外部表** | [是](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) 请参阅受支持的[数据格式](#data-formats)。 | [是](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) 请参阅受支持的[数据格式](#data-formats)。 |
| **缓存查询** | 是，支持多个形式（基于 SSD 的缓存、内存中缓存和结果集缓存）。 此外，还支持具体化视图 | 否 |
| **表变量** | [否](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true)，使用临时表 | 否 |
| **[表分发](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?context=/azure/synapse-analytics/context/context)**               | 是 | 否 |
| **[表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?context=/azure/synapse-analytics/context/context)**                           | 是 | 否 |
| **[表分区](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?context=/azure/synapse-analytics/context/context)**                     | 是 | 否 |
| **[统计信息](develop-tables-statistics.md)**            | 是 | 是 |
| [工作负载管理、资源类和并发控制](../sql-data-warehouse/resource-classes-for-workload-management.md?context=/azure/synapse-analytics/context/context) | 是    | 否 |
| **成本控制** | 是，使用纵向扩展和纵向缩减操作。 | 是，使用 [Azure 门户或 T-SQL 过程](./data-processed.md#cost-control)。 |

## <a name="query-language"></a>查询语言

Synapse SQL 中使用的查询语言可能有不同的受支持功能，具体取决于使用模型。 下表概述了 Transact-SQL 方言中最重要的查询语言差异：

|   | 专用 | 无服务器 |
| --- | --- | --- |
| **SELECT 语句** | 是的。 不支持 Transact-SQL 查询子句 [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[MATCH](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true) 和 OFFSET/FETCH。 | 是。 不支持 Transact-SQL 查询子句 [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[MATCH](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true)、[PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)、GROUPNG SETS 和查询提示。 |
| **INSERT 语句** | 是 | 否 |
| **UPDATE 语句** | 是 | 否 |
| **DELETE 语句** | 是 | 否 |
| **MERGE 语句** | 是（[预览版](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true)） | 否 |
| **[事务](develop-transactions.md)** | 是 | 是，适用于元数据对象。 |
| **[标签](develop-label.md)** | 是 | 否 |
| **数据加载** | 是的。 首选的实用工具是 [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) 语句，但系统支持使用 BULK load (BCP) 和 [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) 加载数据。 | 否 |
| **数据导出** | 是的。 使用 [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true)。 | 是的。 使用 [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true)。 |
| **类型** | 是，支持除 [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true)、[ntext、text 和 image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[空间类型](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) 以外的所有 Transact-SQL 类型 | 是，支持除 [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true)、[ntext、text 和 image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[空间类型](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 Table 类型以外的所有 Transact-SQL 类型 |
| **跨数据库查询** | 否 | 是，包括 [USE](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true) 语句。 |
| **内置函数（分析）** | 是，支持除 [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 [PARSE](/sql/t-sql/functions/parse-transact-sql?view=azure-sqldw-latest&preserve-view=true) 以外的所有 Transact-SQL [分析](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)、转换、[日期和时间](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)、逻辑和[数学](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)函数 | 是，支持所有 Transact-SQL [分析](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)、转换、[日期和时间](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)、逻辑和[数学](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)函数。 |
| **内置函数（[字符串](/sql/t-sql/functions/string-functions-transact-sql)）** | 是的。 除 [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?view=azure-sqldw-latest&preserve-view=true) 以外的所有 Transact-SQL [字符串](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和排序规则函数 | 是的。 所有 Transact-SQL [字符串](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[JSON](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和排序规则函数。 |
| **内置函数（[加密](/sql/t-sql/functions/cryptographic-functions-transact-sql)）** | 一些 | 否 |
| **内置表值函数** | 是，支持除 [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) 以外的 [Transact-SQL 行集函数](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions) | 是，支持除 [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true) 以外的 [Transact-SQL 行集函数](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions)  |
| **聚合** |  除 [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?view=azure-sqldw-latest&preserve-view=true) 以外的 Transact-SQL 内置聚合 | Transact-SQL 内置聚合。 |
| **运算符** | 是，支持除 [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) 以外的所有 [Transact-SQL 运算符](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 是，支持所有 [Transact-SQL 运算符](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **流控制** | 是的。 除 [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[GOTO](/sql/t-sql/language-elements/goto-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[RETURN](/sql/t-sql/language-elements/return-transact-sql?view=azure-sqldw-latest&preserve-view=true)、[USE](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true) 和 [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?view=azure-sqldw-latest&preserve-view=true) 以外的所有 [Transact-SQL 流控制语句](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) | 是的。 `WHILE (...)` 条件中的所有 [Transact-SQL 流控制语句](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) |
| **DDL 语句（CREATE、ALTER、DROP）** | 是的。 适用于受支持对象类型的所有 Transact-SQL DDL 语句 | 是的。 适用于受支持对象类型的所有 Transact-SQL DDL 语句 |

## <a name="security"></a>安全性

Synapse SQL 池可让你使用内置安全功能来保护数据和控制访问。 下表概要比较了 Synapse SQL 使用模型之间的差异。

|   | 专用 | 无服务器 |
| --- | --- | --- |
| **登录名** | 不适用（数据库中仅支持包含的用户） | 是 |
| **用户** |  不适用（数据库中仅支持包含的用户） | 是 |
| **[包含的用户](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true)** | 是的。 **注意：** 只能分配一个 Azure AD 用户作为非受限管理员 | 否 |
| **SQL 用户名/密码身份验证**| 是 | 是 |
| **Azure Active Directory (Azure AD) 身份验证**| 是，Azure AD 用户 | 是，Azure AD 登录名和用户 |
| **存储 Azure Active Directory (Azure AD) 直通身份验证** | 是 | 是 |
| **存储 SAS 令牌身份验证** | 否 | 是，在 [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) 中使用 [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true)，或使用实例级的 [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true)。 |
| **存储访问密钥身份验证** | 是，在 [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) 中使用 [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 否 |
| **存储 [托管标识](../security/synapse-workspace-managed-identity.md)身份验证** | 是，使用[托管服务标识凭据](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&preserve-view=true&toc=%2fazure%2fsynapse-analytics%2ftoc.json&view=azure-sqldw-latest&preserve-view=true) | 是，使用 `Managed Identity` 凭据。 |
| **存储应用程序标识身份验证** | [是](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 否 |
| **服务器级别角色** | 否 | 是，支持 sysadmin 角色、public 角色和其他服务器角色 |
| **SERVER SCOPED CREDENTIAL** | 否 | 是 |
| **权限 - [服务器级别](/sql/relational-databases/security/authentication-access/server-level-roles)** | 否 | 是 |
| 数据库范围的角色 | 是 | 是 |
| **DATABASE SCOPED CREDENTIAL** | 是 | 是 |
| **权限 - [数据库级别](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true)** | 是 | 是 |
| **权限 - 架构级别** | 是，包括能够授予、拒绝和撤销用户/登录名对架构的权限 | 是，包括能够授予、拒绝和撤销用户/登录名对架构的权限 |
| **权限 - 对象级别** | 是，包括能够授予、拒绝和撤销用户的权限 | 是，包括能够授予、拒绝和撤销用户/登录名对受支持系统对象的权限 |
| **权限 - [列级安全性](../sql-data-warehouse/column-level-security.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)** | 是 | 是 |
| **安全性函数和标识函数** | 部分 Transact-SQL 安全性函数和运算符：`CURRENT_USER`、`HAS_DBACCESS`、`IS_MEMBER`、`IS_ROLEMEMBER`、`SESSION_USER`、`SUSER_NAME`、`SUSER_SNAME`、`SYSTEM_USER`、`USER`、`USER_NAME`、`EXECUTE AS`、`OPEN/CLOSE MASTER KEY` | 部分 Transact-SQL 安全性函数和运算符：`CURRENT_USER`、`HAS_DBACCESS`、`HAS_PERMS_BY_NAME`、`IS_MEMBER', 'IS_ROLEMEMBER`、`IS_SRVROLEMEMBER`、`SESSION_USER`、`SESSION_CONTEXT`、`SUSER_NAME`、`SUSER_SNAME`、`SYSTEM_USER`、`USER`、`USER_NAME``EXECUTE AS` 和 `REVERT`。 安全性函数不可用于查询外部数据（将结果存储在可在查询中使用的变量中）。  |
| **行级安全** | [是](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | 否 |
| **透明数据加密 (TDE)** | [是](../../azure-sql/database/transparent-data-encryption-tde-overview.md) | 否 | 
| **数据发现和分类** | [是](../../azure-sql/database/data-discovery-and-classification-overview.md) | 否 |
| **漏洞评估** | [是](../../azure-sql/database/sql-vulnerability-assessment.md) | 否 |
| **高级威胁防护** | [是](../../azure-sql/database/threat-detection-overview.md)
| **审核** | [是](../../azure-sql/database/auditing-overview.md) | 否 |
| **[防火墙规则](../security/synapse-workspace-ip-firewall.md)**| 是 | 是 |
| **[专用终结点](../security/synapse-workspace-managed-private-endpoints.md)**| 是 | 是 |

专用 SQL 池和无服务器 SQL 池使用标准 Transact-SQL 语言来查询数据。 有关详细差异，请查看 [Transact-SQL 语言参考](/sql/t-sql/language-reference)。

## <a name="tools"></a>工具

可以使用各种工具连接到 Synapse SQL 来查询数据。

|   | 专用 | 无服务器 |
| --- | --- | --- |
| **Synapse Studio** | 是，SQL 脚本 | 是，SQL 脚本 |
| **Power BI** | 是 | [是](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | 是 | 是 |
| **Azure Data Studio** | 是 | 是，1.18.0 版或更高版本。 支持 SQL 脚本和 SQL 笔记本。 |
| **SQL Server Management Studio** | 是 | 是，18.5 版或更高版本 |

> [!NOTE]
> 可以使用 SSMS 连接到无服务器 SQL 池并查询。 这从 18.5 版开始部分支持，仅可用于连接和查询。

大多数应用程序使用标准 Transact-SQL 语言来查询 Synapse SQL 的专用使用模型和无服务器使用模型。

## <a name="storage-options"></a>存储选项

分析的数据可以存储在各种类型的存储中。 下表列出了所有可用的存储选项：

|   | 专用 | 无服务器 |
| --- | --- | --- |
| **内部存储** | 是 | 否 |
| **Azure Data Lake v2** | 是 | 是 |
| **Azure Blob 存储** | 是 | 是 |
| **Azure SQL（远程）** | 否 | 否 |
| **Azure CosmosDB 事务存储** | 否 | 否 |
| **Azure CosmosDB 分析存储** | 否 | 是，使用 [Synapse Link](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) |
| **Apache Spark 表（在工作区中）** | 否 | PARQUET 表仅使用[元数据同步](develop-storage-files-spark-tables.md) |
| **Apache Spark 表（远程）** | 否 | 否 |
| **Databricks 表（远程）** | 否 | 否 |

## <a name="data-formats"></a>数据格式

可以采用各种存储格式来存储分析的数据。 下表列出了可分析的所有可用数据格式：

|   | 专用 | 无服务器 |
| --- | --- | --- |
| **带分隔符** | [是](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [是](query-single-csv-file.md) |
| **CSV** | 是（不支持多字符分隔符） | [是](query-single-csv-file.md) |
| **Parquet** | [是](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [是](query-parquet-files.md)，包括带有[嵌套类型](query-parquet-nested-types.md)的文件 |
| **Hive ORC** | [是](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 否 |
| **Hive RC** | [是](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | 否 |
| **JSON** | 是 | [是](query-json-files.md) |
| **Avro** | 否 | 否 |
| **[Delta-lake](https://delta.io/)** | 否 | [是](query-delta-lake-format.md) |
| **[CDM](/common-data-model/)** | 否 | 否 |

## <a name="next-steps"></a>后续步骤
在以下文章中可以找到有关专用 SQL 池和无服务器 SQL 池最佳做法的更多信息：

- [专用 SQL 池最佳做法](best-practices-dedicated-sql-pool.md)
- [无服务器 SQL 池最佳做法](best-practices-serverless-sql-pool.md)
