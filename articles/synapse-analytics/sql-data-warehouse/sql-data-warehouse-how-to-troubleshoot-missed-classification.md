---
title: 排查专用 SQL 池中错误分类的工作负载的问题
description: 确定并排查在 Azure Synapse Analytics 中工作负载被错误分类为专用 SQL 池中的意外工作负载组的问题。
author: SudhirRaparla
ms.author: nvraparl
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 10/01/2021
ms.custom: template-how-to
ms.reviewer: wiassaf
ms.openlocfilehash: 1fa0394f3e711ec172f5a3ad32288f63bcf55dce
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390413"
---
# <a name="troubleshooting-a-misclassified-workload-in-azure-synapse-analytics"></a>排查 Azure Synapse Analytics 中错误分类的工作负载的问题

本文介绍如何排查错误分类的工作负载的问题，以及如何确定专用 SQL 池中工作负载的分类的原因。

Azure Synapse Analytics 提供[将工作负载分类为适当的工作负载组](sql-data-warehouse-workload-classification.md)、[分配重要性](sql-data-warehouse-workload-importance.md)和[隔离资源](sql-data-warehouse-workload-isolation.md)等工作负载管理功能以满足 SLA。 

但是，在某些情况下，这些功能的组合可能会导致工作负载分类不反映用户意向。 本文列出了此类常见情况以及如何排查这些问题。 首先，应查询基本信息，以排查错误分类的工作负载情况的问题。

> [!NOTE]
> 本文不适用于 Azure Synapse Analytics 中的无服务器 SQL 池。

## <a name="basic-troubleshooting-information"></a>基本疑难解答信息

若要排查错误分类的工作负载情况的问题，需要以下信息：

- 所有[工作负载组](#workload-groups)的列表
- 所有[工作负载分类器](#workload-classifiers)和相关工作负载组的列表
- [用户和映射的工作负载组](#users-and-mapped-resource-classes)（系统和用户定义）的列表 
- 请求的[工作负载组和分类器详细信息](#workload-group-and-classifier-details-of-a-request)的列表


### <a name="workload-groups"></a>工作负荷组

#### <a name="azure-portal"></a>Azure 门户

若要在 Azure 门户中获取所有工作负载组（包括系统工作负载组）和相关详细信息的列表，请执行以下操作：

1. 转到已在其下创建感兴趣的专用 SQL 池的 Azure Synapse 工作区。
1. 在左侧窗格中，将列出在工作区下创建的所有池类型。 在“分析池”部分下，选择“SQL 池”。
1. 选择感兴趣的专用 SQL 池。
1. 在左侧窗格中的“设置”下，选择“工作负载管理”。
1. 在“工作负载组”部分下，将列出所有工作负载。 默认情况下，仅列出用户定义的工作负载组。 若要查看用户定义和系统定义的工作负载组的列表，请编辑筛选器并选择“全选”。

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png" alt-text="工作负载组列表筛选器" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png":::

#### <a name="t-sql"></a>T-SQL

若要使用 T-SQL 查看工作负载组，请[使用 SQL Server Management Studio (SSMS) 连接到专用 SQL 池](../sql/get-started-ssms.md)并发出以下查询：
 
```sql
SELECT * FROM sys.workload_management_workload_groups;
```

有关详细信息，请参阅 [sys.workload_management_workload_groups](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql)。


### <a name="workload-classifiers"></a>工作负载分类器

#### <a name="azure-portal"></a>Azure 门户

若要在 Azure 门户中按工作负载组列出所有工作负载分类器（包括系统定义的分类器），请参阅“工作负载组”表的“分类器”列中列出的数字（请参阅上一部分）。

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png" alt-text="工作负载组分类器列表" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png":::

#### <a name="t-sql"></a>T-SQL

若要使用 T-SQL 获取所有工作负载分类器（包括系统定义的分类器）的列表，请使用以下查询：

```sql
SELECT * FROM sys.workload_management_workload_classifiers;
```

有关详细信息，请参阅 [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql)。

### <a name="users-and-mapped-resource-classes"></a>用户和映射的资源类

若要在 T-SQL 中获取系统定义的资源类和映射的用户的列表，请使用以下查询：

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS [Member Name]
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');
```

### <a name="workload-group-and-classifier-details-of-a-request"></a>请求的工作负载组和分类器详细信息

排查错误分类的工作负载问题的第一步是确定查询的工作负载组和工作负载分类器。 使用动态管理视图 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 查看提交的查询及其分类：

```sql
SELECT * FROM sys.dm_pdw_exec_requests;
```

有关详细信息，请参阅 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-catalog-views/sys-dm-pdw-exec-requests-transact-sql)。

## <a name="common-scenarios-of-misclassified-workloads"></a>错误分类的工作负载的常见情况

下面是可能发生意外错误分类的工作负载的一些常见情况：

### <a name="mixed-usage-of-resource-classes-and-user-defined-workload-management"></a>混合使用资源类和用户定义的工作负载管理

当资源类和工作负载组一起使用时，用户角色到资源类的映射可能会与工作负载分类器规则冲突，并且会导致意外的查询分类。 

请考虑以下情况：

1. 使用 `sp_addrolemember` 过程将数据库用户 DBAUser 分配到 largerc 资源类角色。
1. DBAUser 已使用工作负载管理创建了新的工作负载组和分类器。
1. 新创建的工作负载分类器将数据库角色 DBARole 映射到重要性较高的 mediumrc 资源类。 
1. DBAUser 是 DBARole 数据库角色的成员。
1. 当 DBAUser 运行查询时，查询应基于工作负载分类器在 mediumrc 上运行。 相反，它将分配给 largerc，因为用户映射优先于角色成员身份到分类器的映射。

最好避免混合使用资源类和工作负载管理组进行工作负载管理。 有关将资源类转换为工作负载的步骤的详细信息，请参阅[将资源类转换为工作负载组](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)。 

但是，在某些情况下，资源类和工作负载管理需要一起使用。 在此类情况下，为了简化分类错误的排查，我们建议在创建工作负载分类器时删除资源类角色映射。 以下代码返回现有的资源类角色成员身份。 针对相应资源类返回的每个成员名称运行 [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

```sql
SELECT  r.name AS [Resource Class]
, m.name AS membername
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
EXEC sp_droprolemember '[Resource Class]', membername;
```

### <a name="some-administrative-users-are-always-mapped-to-smallrc-workload-group"></a>某些管理用户始终映射到 smallrc 工作负载组

请考虑 Azure Synapse 工作区 SQL 管理员登录名、Azure Synapse Azure Active Directory 管理员（用户或组成员）或数据库所有者的情况。 这些用户可能仍具有工作负载分类器，或者已添加到除 smallrc 以外的资源类角色。 这些用户执行的所有查询仍将在 smallrc 资源类中运行，即使用户映射到不同的资源类或工作负载组。 

建议：这些管理用户不能更改其默认工作负载组。 有关详细信息，请参阅[使用资源类管理工作负载](resource-classes-for-workload-management.md#default-resource-class)。 建议关键型或性能敏感型工作负载不要以专用 SQL 池中的管理用户身份运行。

在 Azure 门户中指定了 Azure Synapse 工作区 SQL 管理员登录名和 Azure Synapse Azure Active Directory 管理员（用户或组成员）：

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png" alt-text="通过查看“工作区 SQL 管理员登录名”字段来标识服务管理员" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png":::

同样，不允许数据库所有者 (dbo) 和 db_owner 数据库角色更改其默认资源类。 如果用户是数据库所有者或添加到 db_owner 数据库角色下，则默认情况下，该用户执行的所有查询都将转到 smallrc。 不能将这些角色添加到除 smallrc 以外的资源类。 但是，如果属于此角色的用户希望将其查询分类为不同的工作负载组，则他们可以使用[工作负载分类器定义](sql-data-warehouse-workload-classification.md)中的 MEMBERNAME 选项。


### <a name="use-workload-group-precedence-for-better-classification"></a>使用工作负载组优先级进行更好的分类

当工作负载分类器映射到多个工作负载组或用户映射到多个资源类时，优先级顺序确定所选的工作负载组和资源类。 优先级规则的列表： 

> [!NOTE]
> 如[混合使用资源类和用户定义的工作负载管理](#mixed-usage-of-resource-classes-and-user-defined-workload-management)部分中所述，建议不要组合使用资源类和用户定义的工作负载组/类。

#### <a name="if-resource-classes-are-being-used"></a>如果使用的是资源类：

当使用的是资源类时，最好为正在运行的每种工作负载类型创建专用用户。 但是，如果用户是多个资源类的成员，则将采用[资源类优先级](resource-classes-for-workload-management.md#resource-class-precedence)中介绍的优先级规则：

1. 动态资源类优先于静态资源类。 例如，如果用户是 mediumrc（动态）和 staticrc80（静态）的成员，则用户查询将使用 mediumrc 来运行。
1. 更大的资源类优先于更小的资源类。 例如，如果用户是 staticrc20 和 staticrc80 的成员，则用户查询将使用 staticrc80 来运行。

#### <a name="if-workload-management-capabilities-are-used"></a>如果使用的是工作负载管理功能

WLM 提供为同一用户或工作负载组创建多个工作负载分类器的功能。 分类器定义语句具有多个参数，具体取决于哪些传入请求分配给工作负载。 这些参数具有如下所示的权重分数，并且此分数确定优先级顺序： 

|**分类器参数** |**Weight**   |
|---------------------|---------|
|MEMBERNAME:USER      |64       |
|MEMBERNAME:ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

让我们通过示例来了解起作用的优先级规则。 如果用户按如下所示创建两个工作负载分类器：

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,START_TIME     = '18:00' 
 ,END_TIME       = '07:30'
 ,IMPORTANCE     = 'Low')
```

可以通过两个分类器提交用户 1 提交的查询。 用户 1 在上午 6 点和上午 7 点 (UTC) 之间使用“dimension_loads”标签运行的查询将分配给 `wgDashboards`，因为 WLM_LABEL 的权重分数高于 START_TIME/END_TIME。 `CLASSIFIER-1` 的权重为 80（用户 64，加上 WLM_LABEL 的 16）。 `CLASSIFIER-2` 的权重为 68（用户 64，加上 START_TIME/END_TIME 的 4）。 

有关工作负载分类的详细信息，请参阅[分类权重](sql-data-warehouse-workload-classification.md#classification-weighting)和[查询标签](../sql/develop-label.md)。

### <a name="what-happens-if-precedence-in-workload-classification-leads-to-a-tie"></a>如果工作负载分类中的优先级平局，会发生什么情况？

即使应用了工作负载分类器优先级，查询也有可能分类为多个工作负载组。 例如，考虑以下分类器：

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'Low');
```

如果用户使用 `OPTION (LABEL = 'dimension_loads')` 运行查询，则可以根据优先级规则分类为 `wgDataLoad` 或 `wgUserqueries`。 但选择了哪个工作负载组？

#### <a name="workload-group-and-classifier-tie-breakers"></a>工作负载组和分类器决定项

如果工作负载组或分类器平局，则将采用以下优先级：

1. 选择具有最高资源分配的工作负载组。 当登录名是多个资源类的成员时，此行为可优化性能。 此行为还可确保向后兼容性。  
    
请考虑以下两个工作负载组和工作负载分类器：
    
```sql
-- Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 26 
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2              
,CAP_PERCENTAGE_RESOURCE = 100 )

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 15
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5               
,CAP_PERCENTAGE_RESOURCE = 50 );

--Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');
```
                  
如果用户使用 `OPTION (LABEL = 'dimension_loads')` 运行查询，则查询同时满足这两个分类器的条件。 但是，用户请求将路由到 `wgUserQueries` 工作负载组，因为每个请求都具有更高的最小资源分配。

2. 接下来，将各个工作负载组的并发设置和可用并发性视为决定项。 收到请求时，具有最高可用并发性的工作负载组将为查询提供最佳执行机会。 

请考虑以下两个工作负载组和工作负载分类器：

```sql
--Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 30              
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 30
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

-- Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');
```
              
如果用户使用 `OPTION (LABEL = 'dimension_loads')` 运行查询，则分类器平局，因为查询同时满足这两个分类器的条件。 当用户提交查询时，请考虑以下情况：在 `wgUserqueries` 组中执行五个并发查询，在 `wgDataLoad` 组中执行十个查询。 用户请求将使用 `CLASSIFIER-2` 路由到 `wgUserqueries` 组，因为 `wgUserqueries` 工作负载组在用户提交查询时具有更高的可用并发性。

3. 接下来，将请求的重要性设置视为决定项。 如果使用优先级规则的工作负载分类平局，则请求将路由到重要性最高的工作负载组。 有关详细信息，请参阅[工作负载重要性](sql-data-warehouse-workload-importance.md)。

4. 最后，将工作负载组的创建时间视为决定项。 用户请求将路由到最近创建的工作负载组。 


## <a name="next-steps"></a>后续步骤
- 有关工作负载分类的详细信息，请参阅[工作负载分类](sql-data-warehouse-workload-classification.md)。
- 有关工作负载重要性的详细信息，请参阅[工作负载重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [开始配置工作负荷重要性](sql-data-warehouse-how-to-configure-workload-importance.md)
 
