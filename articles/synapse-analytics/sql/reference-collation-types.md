---
title: 排序规则支持
description: 排序规则类型支持 Azure Synapse Analytics 中的 Synapse SQL
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 436dbac814197556385a33d956928f97fd4716bf
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311916"
---
# <a name="database-collation-support-for-synapse-sql-in-azure-synapse-analytics"></a>针对 Azure Synapse Analytics 中的 Synapse SQL 的数据库排序规则支持 

排序规则提供基于字符的数据类型的区域设置、代码页、排序顺序和字符敏感度规则。 选择以后，需要排序规则信息的所有列和表达式会从数据库设置中继承所选排序规则。 可以重写默认继承，只需为基于字符的数据类型显式声明另一个排序规则即可。

创建新的专用 SQL 池数据库时，可以从 Azure 门户更改默认数据库排序规则。 利用此功能，可以更轻松地使用 3800 个受支持的数据库排序规则之一创建新数据库。

您可以在创建时使用 CREATE DATABASE 语句指定默认的无服务器 SQL 池数据库排序规则。

## <a name="change-collation"></a>更改排序规则
若要更改专用 SQL 池数据库的默认排序规则，请更新预配体验中的排序规则字段。 例如，如果要将默认排序规则更改为区分大小写，请将排序规则从 SQL_Latin1_General_CP1_CI_AS 重命名为 SQL_Latin1_General_CP1_CS_AS。 

若要更改无服务器 SQL 池数据库的默认排序规则，您可以使用 ALTER DATABASE 语句。

## <a name="list-of-unsupported-collation-types"></a>不受支持的排序规则类型的列表
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

此外，专用 SQL 池不支持以下排序规则类型：

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>检查当前排序规则
若要查看数据库的当前排序规则，可运行以下 T-SQL 代码片段：
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
将“Collation”作为属性参数传递时，DatabasePropertyEx 函数将返回指定数据库的当前排序规则。 可以在 MSDN 上详细了解 DatabasePropertyEx 函数。

## <a name="next-steps"></a>后续步骤

有关专用 SQL 池和无服务器 SQL 池最佳实践的其他信息，请参阅以下文章：

- [专用 SQL 池的最佳实践](best-practices-sql-pool.md)
- [无服务器 SQL 池的最佳实践](best-practices-sql-on-demand.md)


