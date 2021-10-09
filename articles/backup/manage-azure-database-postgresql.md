---
title: 管理 Azure Database for PostgreSQL 服务器
description: 了解如何管理 Azure Database for PostgreSQL 服务器。
ms.topic: conceptual
ms.date: 09/21/2021
ms.openlocfilehash: 2546279f22df9a39f454b926d9960f38cf42f2fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631226"
---
# <a name="manage-azure-database-for-postgresql-server"></a>管理 Azure Database for PostgreSQL 服务器

本文介绍如何管理使用 Azure 备份服务备份的 Azure Database for PostgreSQL 服务器。

## <a name="change-policy"></a>更改策略

你可以通过备份实例更改关联的策略。

1. 选择“备份实例” -> “更改策略”。


   :::image type="content" source="./media/manage-azure-database-postgresql/change-policy.png" alt-text="屏幕截图，显示用于更改策略的选项。":::
   
1. 选择要应用于数据库的新策略。

   :::image type="content" source="./media/manage-azure-database-postgresql/reassign-policy.png" alt-text="屏幕截图，显示用于重新分配策略的选项。":::

## <a name="stop-protection-preview"></a>停止保护（预览）

可以通过三种方式停止保护 Azure Database for PostgreSQL 服务器。

- 停止保护并保留数据（永久保留）：使用此选项，可以阻止所有将来的备份作业保护 Azure Database for PostgreSQL 服务器。 但是，Azure 备份服务会永久保留已备份的恢复点。 你需要付费才能将恢复点保留在保管库中（有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)）。 你可以根据需要从这些恢复点还原。 若要恢复保护，请使用“恢复备份”选项。

- 停止保护并保留数据（按策略保留）：使用此选项，可以阻止所有将来的备份作业保护 Azure Database for PostgreSQL 服务器。 恢复点将按策略保留，并可根据 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)来计费。 但是，最新的恢复点会永久保留。

- 停止保护并删除数据：使用此选项，可以阻止所有将来的备份作业保护 Azure Database for PostgreSQL 服务器，并会删除所有恢复点。 你将无法还原数据库或使用“恢复备份”选项。

### <a name="stop-protection-and-retain-data"></a>停止保护并保留数据

1. 转到“备份中心”，选择“Azure Database for PostgreSQL 服务器(预览)”。 

1. 从服务器备份实例列表中，选择要保留的实例。

1. 选择“停止备份(预览)”。

   :::image type="content" source="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-inline.png" alt-text="屏幕截图，显示要停止 Azure Database for PostgreSQL 服务器备份实例的选择。" lightbox="./media/manage-azure-database-postgresql/select-postgresql-server-backup-instance-to-delete-expanded.png":::

1. 选择以下数据保留选项之一：

   1. 永久保留
   1. 按策略保留
   
   :::image type="content" source="./media/manage-azure-database-postgresql/data-retention-options-inline.png" alt-text="屏幕截图，显示要选择的数据保留的选项。" lightbox="./media/manage-azure-database-postgresql/data-retention-options-expanded.png":::

   还可以从下拉列表中选择停止备份的原因。

1. 单击“停止备份”。

1. 选择“确认”以停止备份。

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="确认停止备份的屏幕截图。" lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

### <a name="stop-protection-and-delete-data"></a>停止保护并删除数据

1. 转到“备份中心”，选择“Azure Database for PostgreSQL 服务器(预览)”。 

1.  从服务器备份实例列表中，选择要删除的实例。

1. 单击“停止备份(预览)”。

1. 选择“删除备份数据”。

   提供备份实例的名称、删除原因和任何其他注释。

   :::image type="content" source="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-inline.png" alt-text="屏幕截图，显示用于删除备份数据的选项以及需要添加的详细信息。" lightbox="./media/manage-azure-database-postgresql/delete-backup-data-and-provide-details-expanded.png":::

1. 选择“停止备份”。

1. 选择“确认”以停止备份。

   :::image type="content" source="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-inline.png" alt-text="确认停止备份的屏幕截图。" lightbox="./media/manage-azure-database-postgresql/confirmation-to-stop-backup-expanded.png":::

## <a name="resume-protection"></a>恢复保护

如果在停止数据备份时选择了“停止保护并保留数据”选项，可以恢复对 Azure Database for PostgreSQL 服务器的保护。

>[!Note]
>开始保护数据库时，备份策略也适用于保留的数据。 如果按照策略，恢复点已过期，则会清理这些已过期的恢复点。

请使用以下步骤：

1. 转到“备份中心”，选择“Azure Database for PostgreSQL 服务器(预览)”。 

1. 从服务器备份实例列表中，选择要恢复的实例。

1. 选择“恢复备份(预览)”。

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-protection-inline.png" alt-text="屏幕截图，显示用于恢复数据保护的选项。" lightbox="./media/manage-azure-database-postgresql/resume-data-protection-expanded.png":::

1. 选择“恢复备份”。

   :::image type="content" source="./media/manage-azure-database-postgresql/resume-data-backup-inline.png" alt-text="屏幕截图，显示用于恢复数据备份的选项。" lightbox="./media/manage-azure-database-postgresql/resume-data-backup-expanded.png":::

## <a name="next-steps"></a>后续步骤

[备份保管库概述](backup-vault-overview.md)
