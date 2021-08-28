---
title: Azure SQL 数据库审核事件与启用了账本的表
description: Azure SQL 数据库账本审核功能概述
ms.custom: references_regions
ms.date: 07/23/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: d032b311809449d35a017fdd053fb97447b8479c
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666723"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>Azure SQL 数据库审核事件与启用了账本的表

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL 数据库账本目前为公共预览版，在欧洲西部、巴西南部和美国中西部提供。

使用支持账本的表执行取证活动时，将在账本视图和数据库账本中捕获数据。 其他操作 ID 也会添加到 SQL 审核日志中。 下表概述了这些新的审核日志记录事件。 触发事件的条件在每个表后面提供。

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

触发事件的条件：创建新账本表或将常规表转换为账本表时。

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

触发事件的条件：删除或重命名账本表时、将账本表转换为普通表时，以及在账本表中添加、删除或重命名列时。


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

触发事件的条件：生成账本摘要时。

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

触发事件的条件：验证账本摘要时。

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
- [快速入门：在 Azure SQL 数据库中创建启用了账本的数据库](ledger-create-a-single-database-with-ledger-enabled.md)
