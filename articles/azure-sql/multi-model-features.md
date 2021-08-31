---
title: 多模型功能
description: 使用 Microsoft Azure SQL，可以处理同一数据库中的多个数据模型。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, urmilano
ms.date: 12/17/2018
ms.openlocfilehash: 93005f665f816f0f6dcde3a1c06748622f4bf271
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113585818"
---
# <a name="multi-model-capabilities-of-azure-sql-database-and-sql-managed-instance"></a>Azure SQL 数据库和 SQL 托管实例的多模型功能
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

使用多模型数据库可以存储和处理以多种数据格式表示的数据，例如关系数据、图、JSON 或 XML 文档、空间数据和键值对。

[Azure SQL 产品系列](azure-sql-iaas-vs-paas-what-is-overview.md)使用一种关系模型，该模型可为各种通用应用程序提供最佳性能。 但是，Azure SQL 数据库和 SQL 托管实例等 Azure SQL 产品并不局限于关系数据。 这些产品允许使用与关系模型紧密集成的非关系格式。

对于以下情况，请考虑使用 Azure SQL 的多模型功能：

- 某些信息或结构更适合 NoSQL 模型，并且你不想要使用独立的 NoSQL 数据库。
- 大部分数据适合关系模型，并且你需要以 NoSQL 形式为某些数据部分建模。
- 你希望使用 Transact-SQL 语言来查询和分析关系数据与 NoSQL 数据，然后将这些数据集成到可以使用 SQL 语言的工具和应用程序。
- 你希望应用[内存中技术](in-memory-oltp-overview.md)等数据库功能来改善 NoSQL 数据结构的分析或处理性能。 可以使用[事务复制](managed-instance/replication-transactional-overview.md)或[只读副本](database/read-scale-out.md)创建数据的副本，并减轻主数据库中的某些分析工作负载。

以下各部分将介绍 Azure SQL 最重要的多模型功能。

> [!Note]
> 可以在同一个 Transact-SQL 查询中使用 JSONPath 表达式、XQuery/XPath 表达式、空间函数和图查询表达式来访问数据库中存储的任何数据。 可执行 Transact-SQL 查询的任何工具或编程语言也可以使用该查询接口来访问多模型数据。 这是与 [Azure Cosmos DB](../cosmos-db/index.yml) 等多模型数据库之间的主要差别，后者为数据模型提供专用的 API。

## <a name="graph-features"></a>图形功能

Azure SQL 产品提供图数据库功能，用于对数据库中的多对多关系建模。 图形是节点（或顶点）和边缘（或关系）的集合。 节点表示实体（例如，某个人或组织）。 边表示其连接的两个节点之间的关系（例如，爱好或朋友）。 

下面是使图形数据库独一无二的某些功能：

- 边是图数据库中的第一类实体。 它们可以有关联的特性或属性。
- 单个边可以灵活连接图数据库中的多个节点。
- 可以轻松表达模式匹配和多跃点导航查询。
- 可以轻松表达传递闭包和多态查询。

[图关系和图查询功能](/sql/relational-databases/graphs/sql-graph-overview)集成到 Transact-SQL 中，享有使用 SQL Server 数据库引擎作为基础数据库管理系统所带来的优势。 图功能使用通过图 `MATCH` 运算符增强的标准 Transact-SQL 查询来查询图数据。

图数据库能够实现的任何功能，关系数据库也都可以实现。 但是，使用图形数据库可以轻松表达某些查询。 可根据以下因素来确定是要选择图数据库还是关系数据库：

- 需要为分层数据建模，其中一个节点可以有多个父级，因此你不能使用 [hierarchyId 数据类型](/sql/t-sql/data-types/hierarchyid-data-type-method-reference)。
- 应用程序存在复杂的多对多关系。 在应用程序演进过程中，会不断添加新的关系。
- 需要分析互联的数据和关系。
- 你希望使用特定于图的 T-SQL 搜索条件，例如 [SHORTEST_PATH](/sql/relational-databases/graphs/sql-graph-shortest-path)。

## <a name="json-features"></a>JSON 功能

在 Azure SQL 产品中，可以分析和查询以 [JavaScript 对象表示法 (JSON)](https://www.json.org/) 格式表示的数据，并将关系数据导出为 JSON 文本。 [JSON](/sql/relational-databases/json/json-data-sql-server) 是 SQL Server 数据库引擎的核心功能。

使用 JSON 功能可以在表中插入 JSON 文档，将关系数据转换为 JSON 文档，并将 JSON 文档转换为关系数据。 可以使用通过 JSON 函数增强的标准 Transact-SQL 语言来分析文档。 还可以使用非聚集索引、列存储索引或内存优化表来优化查询。

JSON 是用于在新式 Web 与移动应用程序中交换数据的流行数据格式。 JSON 还用于将半结构化数据存储在日志文件或 NoSQL 数据库中。 许多 REST Web 服务返回采用 JSON 文本格式的结果，或接受采用 JSON 格式的数据。 

大多数 Azure 服务都具有可返回或使用 JSON 的 REST 终结点。 这些服务包括 [Azure 认知搜索](https://azure.microsoft.com/services/search/)、[Azure 存储](https://azure.microsoft.com/services/storage/)和 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)。

如果具有 JSON 文本，可使用内置函数 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql)、[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql) 和 [ISJSON](/sql/t-sql/functions/isjson-transact-sql)，从 JSON 中提取数据，或者验证 JSON 的格式是否正确。 其他函数包括：

- [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql)：可用于更新 JSON 文本中的值。 
- [OPENJSON](/sql/t-sql/functions/openjson-transact-sql)：可将 JSON 对象数组转换成行集，以进行更高级的查询和分析。 可对返回的结果集执行任何 SQL 查询。 
- [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server)：可用于将关系表中存储的数据格式化为 JSON 文本。

![演示 JSON 函数的示意图。](./media/multi-model-features/image_1.png)

有关详细信息，请参阅[如何处理 JSON 数据](database/json-features.md)。 

在某些特定的情况下，可以使用文档模型而不使用关系模型：

- 架构高度规范化不能带来明显的好处，因为你要一次访问对象的所有字段，或者永远不更新对象的规范化部分。 但是，规范化模型会增加查询的复杂性，因为需要联接大量的表才能获取数据。
- 你使用的应用程序原生将 JSON 文档用于通信或数据模型，而你不想要引入更多的层来将关系数据转换为 JSON，或反之。
- 需要通过将子表或“实体-对象-值”模式取消规范化来简化数据模型。
- 需要加载或导出以 JSON 格式存储的数据，且不使用其他某种用于分析数据的工具。

## <a name="xml-features"></a>XML 功能

XML 功能：在数据库中存储 XML 数据并为其编制索引，并使用本机 XQuery/XPath 运算来处理 XML 数据。 Azure SQL 产品提供专用的内置 XML 数据类型和查询函数用于处理 XML 数据。

SQL Server 数据库引擎提供一个强大的平台，以用于开发应用程序来管理半结构化数据。 数据库引擎的所有组件中都集成了[对 XML 的支持](/sql/relational-databases/xml/xml-data-sql-server)，其中包括：

- 原生可以在 XML 数据类型列中存储 XML 值，该列可根据 XML 架构集合来类型化，或者保持非类型化。 可为 XML 列编制索引。
- 可以针对列中存储的 XML 数据和 XML 类型的变量指定 XQuery 查询。 可在任意 Transact-SQL 查询中使用 XQuery 功能来访问数据库中使用的数据模型。
- 使用[主要 XML 索引](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index)为 XML 文档中的所有元素自动编制索引。 或者，可以使用[辅助 XML 索引](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)来指定要编制索引的确切路径。
- `OPENROWSET` 可用于批量加载 XML 数据。
- 能够将关系数据转换为 XML 格式。

在某些特定的情况下，可以使用文档模型而不使用关系模型：

- 架构高度规范化不能带来明显的好处，因为你要一次访问对象的所有字段，或者永远不更新对象的规范化部分。 但是，规范化模型会增加查询的复杂性，因为需要联接大量的表才能获取数据。
- 你使用的应用程序原生将 XML 文档用于通信或数据模型，而你不想要引入更多的层来将关系数据转换为 JSON，或反之。
- 需要通过将子表或“实体-对象-值”模式取消规范化来简化数据模型。
- 需要加载或导出以 XML 格式存储的数据，且不使用其他某种用于分析数据的工具。

## <a name="spatial-features"></a>空间功能

空间数据表示有关物理位置和对象形状的信息。 这些对象可能是点位置或更复杂的对象，例如国家/地区/区域、道路或湖泊。

Azure SQL 支持两种空间数据类型： 

- 几何类型在欧几里得（平面）坐标系中表示数据。
- 地理类型在环球坐标系中表示数据。

使用 Azure SQL 中的空间功能可以存储几何数据和地理数据。 可以使用 Azure SQL 中的空间对象来分析和查询以 JSON 格式表示的数据，并将关系数据导出为 JSON 文本。 这些空间对象包括 [Point](/sql/relational-databases/spatial/point)、[LineString](/sql/relational-databases/spatial/linestring) 和 [Polygon](/sql/relational-databases/spatial/polygon)。 Azure SQL 还提供可用于提高空间查询性能的专用[空间索引](/sql/relational-databases/spatial/spatial-indexes-overview)。

[空间支持](/sql/relational-databases/spatial/spatial-data-sql-server)是 SQL Server 数据库引擎的核心功能。

## <a name="key-value-pairs"></a>键值对

Azure SQL 产品不提供专用的类型或结构用于支持键值对，因为键值结构原生表示为标准的关系表：

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

可根据需求，不受约束地自定义此键值结构。 例如，值可以是 XML 文档而不是 `nvarchar(max)` 类型。 如果值为 JSON 文档，则你可以使用一个 `CHECK` 约束来验证 JSON 内容的有效性。 可在附加的列中放置与某个键相关的任意数目的值。 例如：

- 添加计算列与索引来简化和优化数据访问。
- 将表定义为内存优化的、仅限架构的表以提高性能。

有关如何在实践中有效地将关系模型用作键值对解决方案的示例，请参阅 [bwin 如何使用 SQL Server 2016 内存中 OLTP 实现前所未有的性能和规模优势](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale)。 在此案例研究中，bwin 为其 ASP.NET 缓存解决方案使用了关系模型，以实现每秒处理 120 万批。

## <a name="next-steps"></a>后续步骤

多模型功能是所有 Azure SQL 产品共有的 SQL Server 数据库引擎核心功能。 若要详细了解这些功能，请参阅以下文章：

- [SQL Server 和 Azure SQL 数据库中的图形处理](/sql/relational-databases/graphs/sql-graph-overview)
- [SQL Server 中的 JSON 数据](/sql/relational-databases/json/json-data-sql-server)
- [SQL Server 中的空间数据](/sql/relational-databases/spatial/spatial-data-sql-server)
- [SQL Server 中的 XML 数据](/sql/relational-databases/xml/xml-data-sql-server)
- [Azure SQL 数据库中的键值存储性能](https://devblogs.microsoft.com/azure-sql/azure-sql-database-as-a-key-value-store/)
