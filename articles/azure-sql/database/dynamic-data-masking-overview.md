---
title: 动态数据屏蔽
description: 动态数据掩码通过对 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 的非特权用户屏蔽敏感数据，限制此类数据的泄露
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 09/12/2021
tags: azure-synpase
ms.openlocfilehash: 8a3740a228aa03a23f3584c3412b8451ebacc35e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812411"
---
# <a name="dynamic-data-masking"></a>动态数据屏蔽 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 支持动态数据掩码。 动态数据屏蔽通过对非特权用户屏蔽敏感数据来限制敏感数据的公开。 

动态数据掩码允许客户指定在对应用层产生最小影响的前提下可以透露的敏感数据量，从而帮助防止未经授权的用户访问敏感数据。 它是一种基于策略的安全功能，可以隐藏对指定数据库字段进行查询时获得的结果集中的敏感数据，不会更改数据库中的数据。

例如，呼叫中心的服务代表可能通过确认呼叫方电子邮件的几个字符来对其进行识别，但是完整的电子邮件地址不应泄露给服务代表。 掩码规则可定义为对任何查询的结果集中的所有电子邮件地址进行掩蔽。 另举一例：在需要进行故障排除时，开发人员可通过定义适当的数据掩码来保护个人数据，因此可在不违反符合性法规的情况下，对生产环境进行查询。

## <a name="dynamic-data-masking-basics"></a>动态数据掩码基础知识

通过在 SQL 数据库配置窗格中的“安全性”下选择“动态数据掩码”边栏选项卡，在 Azure 门户中设置动态数据掩码策略 。 不能使用 SQL 托管实例的门户设置此功能。 有关详细信息，请参阅 [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)。

### <a name="dynamic-data-masking-policy"></a>动态数据掩码策略

* **不对其进行屏蔽的 SQL 用户** - 一组可在 SQL 查询结果中获取非掩码数据的 SQL 用户或 Azure AD 标识。 始终不会对拥有管理员权限的用户进行掩码，这些用户可以看到没有任何掩码的原始数据。
* **掩码规则** - 一组规则，定义要掩码的指定字段，以及使用的掩码函数。 可以使用数据库架构名称、表名称和列名称定义指定的字段。
* **掩码函数** - 一组方法，用于控制不同情况下的数据透露。

| 掩码函数 | 掩码逻辑 |
| --- | --- |
| **默认** |**根据指定字段的数据类型完全掩码**<br/><br/>对于字符串数据类型（nchar、ntext、nvarchar），使用 XXXX；如果字段大小小于 4 个字符，则使用更少的 X。<br/>• 对于数字数据类型（bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real），使用零值。<br/>对于日期/时间数据类型（date、datetime2、datetime、datetimeoffset、smalldatetime、time），使用 1900-01-01。<br/>• 对于 SQL 变量，使用当前类型的默认值。<br/>• 对于 XML，使用文档 \<masked/>。<br/>• 对于特殊数据类型（timestamp、table、hierarchyid、GUID、binary、image、varbinary 空间类型），将使用空值。 |
| **信用卡** |**此掩码方法公开指定字段的最后四位数**，并添加一个信用卡格式的常量字符串作为前缀。<br/><br/>XXXX-XXXX-XXXX-1234 |
| **电子邮件** |**此掩码方法公开第一个字母并将域替换为 XXX.com**，并使用一个电子邮件地址格式的常量字符串作为前缀。<br/><br/>aXX@XXXX.com |
| **随机数** |**此掩码方法根据选定边界和实际数据类型生成随机数**。 如果指定的边界相等，则掩码函数是常数。<br/><br/>![此屏幕截图显示了用于生成随机数的掩码方法。](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **自定义文本** |**此掩码方法公开第一个和最后一个字符**，并在中间添加一个自定义填充字符串。 如果原始字符串短于公开的前缀和后缀，则只使用填充字符串。 <br/>前缀 [填充] 后缀<br/><br/>![导航窗格](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>建议进行掩码的字段

DDM 建议引擎会将数据库中的某些字段标记为可能的敏感字段，可以考虑对这些字段进行掩码。 在门户的“动态数据掩码”边栏选项卡中，会看到针对数据库建议的列。 用户只需针对一个或多个列单击“添加掩码”，单击“保存”，即可对这些字段应用掩码。

## <a name="manage-dynamic-data-masking-using-t-sql"></a>使用 T-SQL 管理动态数据掩码

- 若要创建动态数据掩码，请参阅[创建动态数据掩码](/sql/relational-databases/security/dynamic-data-masking#creating-a-dynamic-data-mask)。
- 若要在现有列上添加或编辑掩码，请参阅[在现有列中添加或编辑掩码](/sql/relational-databases/security/dynamic-data-masking#adding-or-editing-a-mask-on-an-existing-column)。
- 若要授予查看未屏蔽数据的权限，请参阅[授予查看未屏蔽数据的权限](/sql/relational-databases/security/dynamic-data-masking#granting-permissions-to-view-unmasked-data)。
- 若要删除动态数据掩码，请参阅[删除动态数据掩码](/sql/relational-databases/security/dynamic-data-masking#dropping-a-dynamic-data-mask)。

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>使用 PowerShell cmdlet 为数据库设置动态数据掩码

### <a name="data-masking-policies"></a>数据掩码策略

- [Get-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>数据掩码规则

- [Get-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>使用 REST API 为数据库设置动态数据掩码

你可以使用 REST API 以编程方式管理数据掩码策略和规则。 已发布的 REST API 支持以下操作：

### <a name="data-masking-policies"></a>数据掩码策略

- [创建或更新](/rest/api/sql/2014-04-01/datamaskingpolicies/createorupdate)：创建或更新数据库的数据掩码策略。
- [获取](/rest/api/sql/2014-04-01/datamaskingpolicies/get)：获取数据库的数据掩码策略。 

### <a name="data-masking-rules"></a>数据掩码规则

- [创建或更新](/rest/api/sql/2014-04-01/datamaskingrules/createorupdate)：创建或更新数据库的数据掩码规则。
- [按数据库列出](/rest/api/sql/2014-04-01/datamaskingrules/listbydatabase)：获取数据库的数据掩码规则列表。

## <a name="permissions"></a>权限

以下是用于配置动态数据掩码的内置角色：
- [SQL 安全管理器](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- [SQL DB 参与者](../../role-based-access-control/built-in-roles.md#sql-db-contributor)
- [SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)

以下是使用动态数据屏蔽所需的操作：

读/写：
- Microsoft.Sql/servers/databases/dataMaskingPolicies/* Read：
- Microsoft.Sql/servers/databases/dataMaskingPolicies/read Write：
-   Microsoft.Sql/servers/databases/dataMaskingPolicies/write

若要详细了解在使用带有 T-SQL 命令的动态数据掩码时的权限，请参阅[权限](/sql/relational-databases/security/dynamic-data-masking#permissions)

## <a name="see-also"></a>另请参阅

- 适用于 SQL Server 的[动态数据掩码](/sql/relational-databases/security/dynamic-data-masking)。
- 第 9 频道上有关 [Azure SQL 动态数据掩码粒度权限](https://channel9.msdn.com/Shows/Data-Exposed/Granular-Permissions-for-Azure-SQL-Dynamic-Data-Masking)的“已公开数据”剧集。
