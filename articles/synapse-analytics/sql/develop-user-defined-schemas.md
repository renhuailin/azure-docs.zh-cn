---
title: Synapse SQL 中的用户定义的架构
description: 以下各节介绍如何通过 T-SQL 用户定义的架构使用 Azure Synapse Analytics 的 Synapse SQL 功能来开发解决方案的各种相关技巧。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 5bbe3e3471101df5d147c5079d6cd4a7e5ce00cb
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114287550"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Synapse SQL 中的用户定义的架构

以下各节介绍如何通过 T-SQL 用户定义的架构在 Synapse SQL 中开发解决方案的各种相关技巧。

## <a name="schemas-for-application-boundaries"></a>应用程序边界的架构

传统分析体系结构通常使用不同的数据库，根据工作负荷、域或安全性来创建应用程序边界。 例如，传统 SQL Server 分析体系结构可能包含临时数据库、分析数据库和数据市场数据库。 在此拓扑中，每个数据库均作为体系结构中的工作负载和安全边界来运行。

而 Synapse SQL 在一个数据库中运行整个分析工作负载。 不允许跨数据库联接。 Synapse SQL 预期仓库使用的所有表都存储在一个数据库中。

> [!NOTE]
> 专用 SQL 池不支持任何种类的跨数据库查询。 因此，需要修改利用此模式的分析实现。 无服务器 SQL 池支持跨数据库查询。

## <a name="user-defined-schema-recommendations"></a>用户定义的架构建议

包括针对使用用户定义的架构合并工作负荷、安全性、域和功能边界的建议：

- 使用一个数据库运行整个分析工作负载。
- 合并现有的分析环境以使用一个数据库。
- 利用 **用户定义的架构** 来提供以前使用数据库实现的边界。

如果以前尚未使用用户定义的架构，就不会存在任何记录。 使用旧数据库名称作为 Synapse SQL 数据库中用户定义的架构的基础。

如果已使用架构，则可采用以下几个选项：

- 删除旧架构名称并重新开始
- 在表名称前面附加旧架构名称，以保留旧架构名称
- 在额外架构中的表上实现视图来重建旧架构结构，以保留旧架构名称。

> [!NOTE]
> 在首次检查时，选项 3 似乎像是最吸引人的选项。 视图在 Synapse SQL 中是只读的。 任何表修改或数据修改只能针对基础表执行。 选项 3 还在系统中引入了一个视图层。 如果已在体系结构中使用视图，可以再三考虑一下此选项。
> 
> 

### <a name="examples"></a>示例

基于数据库名称实现用户定义的架构。

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

在表名称前面附加旧架构名称，以保留旧架构名称。 使用工作负荷边界的架构。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

使用视图保留旧架构名称。

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> 如果架构策略发生任何更改，则需要检查数据库的安全模型。 在许多情况下，可以在架构级别分配权限，以简化安全模型。

如果需要更细粒度的权限，可以使用数据库角色。 有关数据库角色的详细信息，请参阅[管理数据库角色和用户](/sql/relational-databases/security/authentication-access/database-level-roles)一文。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [Synapse SQL 开发概述](develop-overview.md)。