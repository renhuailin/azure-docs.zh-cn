---
title: 使用 dbForge Studio for MySQL 将 MySQL 数据库迁移到 Azure Database for MySQL
description: 本文演示如何使用 dbForge Studio for MySQL 迁移到 Azure Database for MySQL。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: d7ed106fb1922315ba181e2ca2c27dcc7d2b9cd3
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652918"
---
# <a name="migrate-data-to-azure-database-for-mysql-with-dbforge-studio-for-mysql"></a>使用 dbForge Studio for MySQL 将数据迁移到 Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

你要将 MySQL 数据库迁移到 Azure Database for MySQL 吗？ 请考虑使用 dbForge Studio for MySQL 中的迁移工具。 利用该工具，可以配置、保存、编辑、自动化和安排数据库转移。

若要完成本文中的示例，你需要下载并安装 [dbForge Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/)。

## <a name="connect-to-azure-database-for-mysql"></a>连接到 Azure Database for MySQL

1. 在 dbForge Studio for MySQL 中，请从“数据库”菜单选择“新建连接”。 

1. 提供主机名和登录凭据。

1. 选择“测试连接”以检查配置。

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="此屏幕截图显示测试 Azure Database for MySQL 连接成功。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png":::

## <a name="migrate-with-the-backup-and-restore-functionality"></a>使用“备份和还原”功能执行迁移

使用 dbForge Studio for MySQL 将数据库迁移到 Azure 时，你可从多个选项中进行选择。 如果需要移动整个数据库，最好使用“备份和还原”功能。

在此示例中，我们将 sakila 数据库从 MySQL Server 迁移到 Azure Database for MySQL。 使用“备份和还原”功能背后的逻辑是创建 MySQL 数据库的备份，然后在 Azure Database for MySQL 中将其还原。

### <a name="back-up-the-database"></a>备份数据库

1. 在 dbForge Studio for MySQL 中，从“备份和还原”菜单选择“备份数据库”。  “数据库备份向导”随即显示。

1. 在“数据库备份向导”的“备份内容”选项卡中，选择要备份的数据库对象。 

1. 在“选项”选项卡中，配置备份过程以满足你的要求。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="此屏幕截图显示备份向导的“选项”窗格。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png":::

1. 选择“下一步”，然后指定错误处理行为和日志记录选项。

1. 选择“备份”。

### <a name="restore-the-database"></a>还原数据库

1.  在 dbForge Studio for MySQL 中，连接到 Azure Database for MySQL。 [请参阅说明](#connect-to-azure-database-for-mysql)。

1. 从“备份和还原”菜单选择“还原数据库”。  **数据库还原向导** 随即显示。

1. 在“数据库还原向导”中，选择包含数据库备份的文件。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="此屏幕截图显示数据库还原向导的还原步骤。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png":::

1. 选择“还原”。

1. 检查结果。

## <a name="migrate-with-the-copy-databases-functionality"></a>使用“复制数据库”功能执行迁移

dbForge Studio for MySQL 中的“复制数据库”功能与“备份和还原”类似，只是它不需要执行两个步骤来迁移数据库。  此外，还可以一次转移两个或两个以上数据库。

>[!NOTE]
> “复制数据库”功能仅在 dbForge Studio for MySQL 企业版中提供。

在此示例中，我们将 world_x 数据库从 MySQL Server 迁移到 Azure Database for MySQL。

要使用“复制数据库”功能迁移数据库：

1. 在 dbForge Studio for MySQL 中，请从“数据库”菜单选择“复制数据库”。  

1. 在“复制数据库”选项卡上，指定源和目标连接。 此外，请选择要迁移的数据库。 

   输入 Azure MySQL 连接并选择“world_x”数据库。 选择绿色箭头以启动此过程。

1. 检查结果。

你会看到 world_x 数据库已成功出现在 Azure MySQL 中。

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="此屏幕截图显示“复制数据库”功能的结果。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png":::

## <a name="migrate-a-database-with-schema-and-data-comparison"></a>通过比较架构和数据迁移数据库

使用 dbForge Studio for MySQL 将数据库、架构和/或数据迁移到 Azure 时，你可从多个选项中进行选择。 如果需要将选择性表从 MySQL 数据库移至 Azure，最好使用“架构比较”和“数据比较”功能。 

在此示例中，我们将 world 数据库从 MySQL Server 迁移到 Azure Database for MySQL。 

使用“备份和还原”功能背后的逻辑是创建 MySQL 数据库的备份，然后在 Azure Database for MySQL 中将其还原。

此方法背后的逻辑是在 Azure Database for MySQL 中创建一个空数据库，并将其与源 MySQL 数据库同步。 我们先使用“架构比较”工具，接着使用“数据比较”功能。  这些步骤可确保将 MySQL 架构和数据准确地转至 Azure。

若要完成本练习，首先需要[连接到 Azure Database for MySQL](#connect-to-azure-database-for-mysql) 并创建一个空数据库。

### <a name="schema-synchronization"></a>架构同步

1. 在“比较”菜单中，选择“新建架构比较”。  “新建架构比较向导”随即显示。

1. 选择源和目标，然后指定架构比较选项。 选择“比较”。

1. 在显示的比较结果网格中，选择要同步的对象。 选择绿色箭头按钮以打开“架构同步向导”。

1. 逐步完成配置同步的向导步骤。 选择“同步”以部署更改。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="此屏幕截图显示架构同步向导。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png":::

### <a name="data-comparison"></a>数据比较

1. 在“比较”菜单中，选择“新建数据比较”。  “新建数据比较向导”随即显示。

1. 选择源和目标，然后指定数据比较选项。 如有必要，请更改映射，然后选择“比较”。

1. 在显示的比较结果网格中，选择要同步的对象。 选择绿色箭头按钮以打开“数据同步向导”。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="此屏幕截图显示数据比较结果。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png":::

1. 逐步完成配置同步的向导步骤。 选择“同步”以部署更改。

1. 检查结果。

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="此屏幕截图显示“数据同步向导”的结果。" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png":::

## <a name="next-steps"></a>后续步骤
- [MySQL 概述](overview.md)
