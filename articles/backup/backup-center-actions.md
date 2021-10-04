---
title: 使用备份中心执行操作
description: 本文介绍如何使用备份中心执行操作
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: e8b357c19caeb20c2a50d9a05f9d8d583bd8f640
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083613"
---
# <a name="perform-actions-using-backup-center"></a>使用备份中心执行操作

利用备份中心，无需导航到单个保管库，即可从中心界面执行与关键备份相关的操作。 可以从备份中心执行的一些操作包括：

* 为数据源配置备份
* 还原备份实例
* 创建新的保管库
* 创建新备份策略
* 为备份实例触发按需备份
* 停止为备份实例备份

## <a name="supported-scenarios"></a>支持的方案

* 备份中心目前支持 Azure VM 备份、Azure VM 中的 SQL 备份、Azure VM 中的 SAP HANA 备份、Azure 文件存储备份和 Azure Database for PostgreSQL 服务器备份。
* 有关支持和不支持的方案的详细列表，请参阅[支持矩阵](backup-center-support-matrix.md)。

## <a name="configure-backup"></a>配置备份

如果要备份 Azure VM、Azure VM 中的 SQL、Azure VM 中的 SAP HANA 或 Azure 文件存储，应使用恢复服务保管库。 如果要备份 Azure Databases for PostgreSQL 服务器，应使用备份保管库。 

根据要备份的数据源的类型，请按照下面的相应说明进行操作。

### <a name="configure-backup-to-a-recovery-services-vault"></a>配置备份到恢复服务保管库

1. 导航到备份中心，然后在“概述”选项卡顶部选择“+ 备份” 。

    ![备份中心概述](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. 选择想要备份的数据源的类型。

    ![选择要配置 VM 备份的数据源](./media/backup-center-actions/backup-select-datasource-vm.png)

3. 选择恢复服务保管库，然后选择“继续”。 这样你就可以体验与恢复服务保管库一样的备份配置。 [详细了解如何使用恢复服务保管库为 Azure 虚拟机配置备份](tutorial-backup-vm-at-scale.md)。

### <a name="configure-backup-to-a-backup-vault"></a>为备份保管库配置备份

1. 导航到备份中心，然后在“概述”选项卡顶部选择“+ 备份” 。
2. 选择想要备份的数据源的类型（在本例中为 Azure Database for PostgreSQL 服务器）。

    ![选择要配置 Azure Database for PostgreSQL 服务器备份的数据源](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. 选择“继续”。 这样你就可以体验与还原保管库一样的备份配置。 [详细了解如何使用备份保管库为 Azure Database for PostgreSQL 服务器配置备份](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)。

## <a name="restore-a-backup-instance"></a>还原备份实例

根据要还原的数据源的类型，请按照下面的相应说明进行操作。

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>若要从恢复服务保管库还原

1. 导航到备份中心，然后在“概述”选项卡顶部选择“还原” 。

    ![用于还原 VM 的备份中心概述](./media/backup-center-actions/backup-center-overview-restore.png)

2. 选择想要还原的数据源的类型。

    ![选择用于 VM 还原的数据源](./media/backup-center-actions/restore-select-datasource-vm.png)

3. 选择备份实例，然后选择“继续”。 这样你就可以体验与恢复服务保管库一样的还原设置。 [详细了解如何使用恢复服务保管库还原 Azure 虚拟机](backup-azure-arm-restore-vms.md#before-you-start)。

### <a name="if-youre-restoring-from-a-backup-vault"></a>若要从备份保管库进行还原

1. 导航到备份中心，然后在“概述”选项卡顶部选择“还原” 。
2. 选择想要还原的数据源的类型（在本例中为 Azure Database for PostgreSQL 服务器）。

    ![选择用于 Azure Database for PostgreSQL 服务器还原的数据源](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. 选择备份实例，然后选择“继续”。 这样你就可以体验与恢复服务保管库一样的还原设置。 [详细了解如何使用备份保管库还原 Azure Database for PostgreSQL 服务器](restore-azure-database-postgresql.md)。

## <a name="create-a-new-vault"></a>创建新的保管库

可以通过导航到备份中心并在“概述”选项卡顶部选择“+ 保管库”来创建新的保管库 。

![创建保管库](./media/backup-center-actions/backup-center-create-vault.png)

* [详细了解如何创建恢复服务保管库](backup-create-rs-vault.md)
* [详细了解如何创建备份保管库](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>创建新备份策略

根据要备份的数据源的类型，请按照下面的相应说明进行操作。

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>若要备份到恢复服务保管库

1. 导航到备份中心，然后在“概述”选项卡顶部选择“+ 策略” 。

    ![备份策略的备份中心概述](./media/backup-center-actions/backup-center-overview-policy.png)

2. 选择想要备份的数据源的类型。

    ![选择要用于 VM 备份策略的数据源](./media/backup-center-actions/policy-select-datasource-vm.png)

3. 选择恢复服务保管库，然后选择“继续”。 这样你就可以体验与恢复服务保管库一样的策略创建。 [详细了解如何使用恢复服务保管库为 Azure 虚拟机创建新的备份策略](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。

### <a name="if-youre-backing-up-to-a-backup-vault"></a>若要备份到“备份”保管库

1. 导航到备份中心，然后在“概述”选项卡顶部选择“+ 策略” 。
2. 选择想要备份的数据源的类型（在本例中为 Azure Database for PostgreSQL 服务器）。

    ![选择要用于 Azure Database for PostgreSQL 服务器备份策略的数据源](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. 选择“继续”。 这样你就可以体验与还原保管库一样的策略创建。 [详细了解如何使用备份保管库创建新的备份策略](backup-azure-database-postgresql.md#create-backup-policy)。

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>为备份实例执行按需备份

通过备份中心，可以搜索整个备份资产中的备份实例，并按需执行备份操作。

若要停止保护，请导航到“备份中心”，然后选择“备份实例”菜单项。 通过选择此选项，可以查看你有权访问的所有备份实例的详细信息。 可以搜索想要备份的备份实例。 右键单击网格中的某个项会打开可用操作列表。 选择“立即备份”选项以执行按需备份。

![按需备份](./media/backup-center-actions/backup-center-on-demand-backup.png)

[详细了解如何对 Azure 虚拟机执行按需备份](backup-azure-manage-vms.md#run-an-on-demand-backup)。

[详细了解如何对 Azure Database for PostgreSQL 服务器执行按需备份](backup-azure-database-postgresql.md)。

## <a name="stop-backup-for-a-backup-instance"></a>停止为备份实例备份

在某些情况下，你可能想要停止为备份实例进行备份，例如当正在备份的基础资源不再存在时。

若要停止保护，请导航到“备份中心”，然后选择“备份实例”菜单项。 选择此选项可以查看有权访问的所有备份实例的详细信息。 可以搜索想要备份的备份实例。 右键单击网格中的某个项会打开可用操作列表。 选择“停止备份”选项以停止备份实例的备份。

![停止保护](./media/backup-center-actions/backup-center-stop-protection.png)

- [详细了解](backup-azure-manage-vms.md#stop-protecting-a-vm)如何停止 Azure 虚拟机的备份。
- [详细了解](manage-azure-managed-disks.md#stop-protection-preview)如何停止磁盘的备份。
- [详细了解](manage-azure-database-postgresql.md#stop-protection-preview)如何停止 Azure Database for PostgreSQL 服务器的备份。

## <a name="resume-backup"></a>恢复备份

如果你在“停止保护”期间保留了数据，则可以恢复工作负荷的备份。

- [详细了解](manage-azure-database-postgresql.md#resume-protection)如何继续保护磁盘。
- [详细了解](manage-azure-managed-disks.md#resume-protection)如何继续保护 Azure Database for PostgreSQL 服务器。

## <a name="next-steps"></a>后续步骤

* [监视和操作备份](backup-center-monitor-operate.md)
* [治理备份空间](backup-center-govern-environment.md)
* [获取有关备份的见解](backup-center-obtain-insights.md)
