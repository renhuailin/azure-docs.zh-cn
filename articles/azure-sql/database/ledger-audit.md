---
title: Azure SQL 数据库审核事件与启用了账本的表
description: Azure SQL 数据库账本审核功能概述
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: eafca78dc22a01784eee79293fb7a2ec798818ad
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385349"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>Azure SQL 数据库审核事件与启用了账本的表

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公开预览版。

使用启用了账本的表执行取证活动时，除了在账本视图和数据库账本中捕获的数据外，其他操作 ID 也会添加到 SQL 审核日志中。  下表概述了这些新的审核日志记录事件，以及触发事件的条件。

## <a name="enable-ledger"></a>启用账本

| 列 | 值 |
|--|--|
| **action_id** | ENLR |
| **name** | ENABLE LEDGER  |
| **class_desc** | OBJECT |
| covering_action_desc | Null |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | Null |
| configuration_group_name | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

触发事件的条件：创建新的账本表，或者将常规表转换为账本表。

## <a name="alter-ledger"></a>更改账本

| 列 | 值 |
|--|--|
| **action_id** | ALLR |
| **name** | ALTER LEDGER |
| **class_desc** | OBJECT |
| covering_action_desc | Null |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | Null |
| configuration_group_name | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

触发事件的条件：删除或重命名账本表，将账本表转换为普通表，添加、删除或重命名账本表中的列。


## <a name="generate-ledger-digest"></a>生成账本摘要

| 列 | 值 |
|--|--|
| **action_id** | GDLR |
| **name** | GENERATE LEDGER DIGEST |
| **class_desc** | DATABASE |
| covering_action_desc | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | Null |
| configuration_group_name | LEDGER_OPERATION_GROUP  |
| **action_in_log** | 1 |

触发事件的条件：生成账本摘要。

## <a name="verify-ledger"></a>验证账本

| 列 | 值 |
|--|--|
| **action_id** | VFLR |
| **name** | VERIFY LEDGER |
| **class_desc** | DATABASE |
| covering_action_desc | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | Null |
| configuration_group_name | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

触发事件的条件：验证账本摘要。

## <a name="ledger-operation-group"></a>账本操作组

| 列 | 值 |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | DATABASE |
| covering_action_desc | Null |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | Null |
| **configuration_level** | GROUP |
| configuration_group_name | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

触发事件的条件：不适用

| 列 | 值 |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | SERVER |
| covering_action_desc | Null |
| **parent_class_desc** | Null |
| **covering_parent_action_name** | Null |
| **configuration_level** | GROUP |
| configuration_group_name | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

触发事件的条件：不适用 

## <a name="next-steps"></a>后续步骤

- [Azure SQL 数据库和 Azure Synapse Analytics 的审核](auditing-overview.md)
- [Azure SQL 数据库账本概述](ledger-overview.md)
- [快速入门：创建启用了账本的 Azure SQL 数据库](ledger-create-a-single-database-with-ledger-enabled.md)
