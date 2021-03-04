---
title: 使用 dbForge Studio for MySQL 连接到 Azure Database for MySQL
description: 本文演示了如何通过 dbForge Studio for MySQL 连接到 Azure Database for MySQL Server。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 5138e542d1cc744a23fa1017dfec1f24b2ec1a5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107352"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>使用 dbForge Studio for MySQL 连接到 Azure Database for MySQL

使用 dbForge Studio for MySQL 连接到 Azure Database for MySQL：

1. 在 "数据库" 菜单上，选择 "新建连接"。

2. 提供主机名和登录凭据。

3. 选择 "测试连接" 按钮以查看配置。

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure 连接":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>使用备份和还原功能迁移数据库

Studio 允许通过多种方式将数据库迁移到 Azure，选择哪一种取决于你的需求。 如果需要移动整个数据库，最好使用备份和还原功能。 在此示例中，我们将位于 MySQL server 上的 *sakila* 数据库迁移到 Azure Database for MySQL。 使用 dbForge Studio for MySQL 的备份和还原功能的迁移过程的逻辑是创建 MySQL 数据库的备份，然后在 Azure Database for MySQL 中将其还原。

### <a name="back-up-the-database"></a>备份数据库

1. 在 "数据库" 菜单上，指向 "备份和还原"，然后选择 "备份数据库"。 "数据库备份向导" 随即出现。

2. 在 "数据库备份向导" 的 "备份内容" 选项卡上，选择要备份的数据库对象。

3. 在 "选项" 选项卡上，配置备份过程以满足你的要求。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="备份向导选项":::

4. 接下来，指定错误处理行为和日志记录选项。

5. 选择 "备份"。

### <a name="restore-the-database"></a>还原数据库

1. 如前文所述，连接到适用于 MySQL 的数据库的 Azure。

2. 右键单击数据库资源管理器主体，指向 "备份和还原"，然后选择 "还原数据库"。

3. 在打开的 "数据库还原" 向导中，选择包含数据库备份的文件。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="还原步骤":::

4. 选择 "还原"。

5. 检查结果。

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>使用复制数据库功能迁移数据库

复制数据库功能与备份和还原类似，只是在这种情况下，你不需要执行两个步骤来迁移数据库。 而且，该功能可以在一次中转中传输两个或多个数据库。 复制数据库功能仅适用于 dbForge Studio for MySQL Enterprise edition。
在此示例中，我们将 *world_x* 数据库从 MySQL 服务器迁移到 Azure Database for MySQL。
使用复制数据库功能迁移数据库：

1. 在 "数据库" 菜单上，选择 "复制数据库"。 

2. 在出现的 "复制数据库" 选项卡中，指定源连接和目标连接，然后选择要迁移的数据库 () 。 输入 Azure MySQL 连接并选择 *world_x* 数据库。 选择绿色箭头以启动进程。

3. 检查结果。

由于我们的数据库迁移工作， *world_x* 数据库已成功在 Azure MySQL 中出现。

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="复制数据库结果":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>使用架构和数据比较工具迁移数据库

dbForge Studio for MySQL 包含一些可用于将 MySQL 数据库、MySQL 架构和/或数据迁移到 Azure 的工具。 功能的选择取决于你的需求和项目的要求。 如果需要选择性地移动数据库（即，将某些 MySQL 表迁移到 Azure），最好使用架构和数据比较功能。
在此示例中，我们将驻留在 MySQL 服务器上的 *世界* 数据库迁移到 Azure Database for MySQL。 使用 dbForge Studio for MySQL 的架构和数据比较功能的迁移过程的逻辑是在 Azure Database for MySQL 中创建一个空的数据库，首先使用架构比较工具将其与所需的 MySQL 数据库同步，然后使用数据比较工具。 这样一来，MySQL 架构和数据就能准确地转移到 Azure。

### <a name="connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>连接到 Azure Database for MySQL 并创建空数据库

连接到 Azure Database for MySQL 并创建一个空数据库。

### <a name="step-2-schema-synchronization"></a>步骤 2。 架构同步

1. 在 "比较" 菜单上，选择 "新建架构比较"。
此时将显示 "新建架构比较向导"。

2. 选择源和目标，并指定架构比较选项。 选择 "比较"。

3. 在出现的 "比较结果" 网格中，选择要同步的对象。 选择绿色箭头按钮以打开架构同步向导。

4. 演练配置同步的向导步骤。 选择 "同步" 以部署更改。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="架构同步向导":::

### <a name="data-comparison"></a>数据比较

1. 在 "比较" 菜单上，选择 "新建数据比较"。 此时将显示 "新建数据比较向导"。

2. 选择 "源" 和 "目标"，然后指定数据比较选项，并根据需要更改映射。 选择 "比较"。

3. 在出现的 "比较结果" 网格中，选择要同步的对象。 选择绿色箭头按钮以打开数据同步向导。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="数据复合结果":::

4. 演练配置同步的向导步骤。 选择 "同步" 以部署更改。

5. 检查结果。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="数据同步结果":::

## <a name="summary"></a>总结

如今更多的企业将其数据库移到 Azure Database for MySQL，因为此数据库服务易于设置、管理和缩放。 这种迁移不需要太费力。 dbForge Studio for MySQL 的 immaculate 迁移工具可显著简化此过程。 通过 Studio，可以轻松地配置、保存、编辑、自动化和计划数据库传输。

## <a name="next-steps"></a>后续步骤
- [MySQL 概述](overview.md)