---
title: 阻止 T-SQL 命令创建或修改 Azure SQL 资源
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: 本文详细介绍一项可让 Azure 管理员阻止 T-SQL 命令创建或修改 Azure SQL 资源的功能
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 65ea6d78eb807671df1732392e112d368877a2b5
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108132042"
---
# <a name="what-is-block-t-sql-crud-feature"></a>什么是阻止 T-SQL CRUD 功能？
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


此功能可让 Azure 管理员阻止通过 T-SQL 创建或修改 Azure SQL 资源。 此功能是在订阅级别强制执行的，可以阻止 T-SQL 命令影响任何 Azure SQL 数据库或托管实例中的 SQL 资源。

## <a name="overview"></a>概述

若要阻止通过 T-SQL 创建或修改资源，并通过 Azure 资源管理器模板（ARM 模板）对给定的订阅强制实施资源管理，可以使用 Azure 门户中的订阅级预览功能。 使用 [Azure 策略](../../governance/policy/overview.md)通过 ARM 模板强制实施组织标准时，此方法特别有用。 既然 T-SQL 不遵守 Azure 策略，那么可以对 T-SQL 创建或修改操作应用阻止措施。 阻止的语法包括针对 Azure SQL 中数据库的 CRUD（创建、更新、删除）语句，具体而言，是 `CREATE DATABASE`、`ALTER DATABASE` 和 `DROP DATABASE` 语句。 

可以通过 Azure 门户、[PowerShell](/powershell/module/az.resources/register-azproviderfeature) 或 [Azure CLI](/cli/azure/feature#az_feature_register) 阻止 T-SQL CRUD 操作。

## <a name="permissions"></a>权限

若要注册或删除此功能，Azure 用户必须是订阅“所有者”或“参与者”角色的成员。

## <a name="examples"></a>示例

以下部分介绍如何在 Azure 门户上的 Microsoft.Sql 资源提供程序中注册或取消注册预览功能： 

### <a name="register-block-t-sql-crud"></a>注册阻止 T-SQL CRUD

1. 在 Azure 门户中转到你的订阅。
2. 选择“预览功能”选项卡。 
3. 选择“阻止 T-SQL CRUD”。
4. 选择“阻止 T-SQL CRUD”后，会打开一个新窗口，请在其中选择“注册”以在 Microsoft.Sql 资源提供程序中注册此阻止功能 。

![在“预览功能”列表中选择“阻止 T-SQL CRUD”](./media/block-tsql-crud/block-tsql-crud.png)

![选中“阻止 T-SQL CRUD”后，选择“注册”](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>重新注册 Microsoft.sql 资源提供程序 
在 Microsoft.Sql 资源提供程序中注册阻止 T-SQL CRUD 后，必须重新注册 Microsoft.Sql 资源提供程序才能使更改生效。 若要重新注册 Microsoft.Sql 资源提供程序，请执行以下操作：

1. 在 Azure 门户中转到你的订阅。
2. 选择“资源提供程序”选项卡。
3. 搜索并选择“Microsoft.Sql”资源提供程序。
4. 选择“重新注册”。 

> [!NOTE]
> 要将 T-SQL 阻止应用于订阅，必须执行重新注册步骤。 

![重新注册 Microsoft.Sql 资源提供程序](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>删除阻止 T-SQL CRUD
若要从订阅中删除阻止 T-SQL 创建或修改操作的功能，请先取消注册之前已注册的 T-SQL 阻止功能。 然后，如上所述重新注册 Microsoft.Sql 资源提供程序，使删除 T-SQL 阻止功能的设置生效。 


## <a name="next-steps"></a>后续步骤

- [Azure SQL 数据库安全功能概述](security-overview.md)
- [Azure SQL 数据库安全最佳做法](security-best-practice.md)