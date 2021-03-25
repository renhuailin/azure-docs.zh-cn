---
title: 从 VM 窗格备份 SQL Server VM
description: 本文介绍如何从 VM 窗格备份 Azure 虚拟机上的 SQL Server 数据库。
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "88891651"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>从 VM 窗格备份 SQL Server

本文说明如何使用 [Azure 备份](backup-overview.md)服务备份在 Azure VM 中运行的 SQL Server。 可使用以下两种方法备份 SQL Server VM：

- 单个 SQL Server Azure VM：本文中的说明介绍如何直接通过 VM 视图备份 SQL Server VM。
- 多个 SQL Server Azure VM：可设置一个恢复服务保管库，并为多个 VM 配置备份。 按照[此文](backup-sql-server-database-azure-vms.md)中针对该方案的说明操作。

## <a name="before-you-start"></a>开始之前

1. 通过[支持矩阵](sql-support-matrix.md)验证环境。
2. 获取 SQL Server VM 的 Azure 备份[概述](backup-azure-sql-database.md)。
3. 验证 VM 是否已建立[网络连接](backup-sql-server-database-azure-vms.md#establish-network-connectivity)。

## <a name="configure-backup-on-the-sql-server"></a>在 SQL Server 上配置备份

可通过 VM 中的“备份”窗格对 SQL Server VM 启用备份。 此方法执行以下两项操作：

- 向 Azure 备份服务注册 SQL VM 以授予其访问权限。
- 自动保护 VM 内运行的所有 SQL Server 实例。 这意味着备份策略将应用于所有现有数据库以及将来向这些实例添加的数据库。

1. 选择页面顶部的横幅以打开 SQL Server 备份视图。

    ![SQL Server 备份视图](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >看不到横幅？ 仅对使用 Azure 市场映像创建的 SQL Server VM 显示横幅。 此外，还会对受 Azure VM 备份保护的 VM 显示。 对于其他映像，可按[此处](backup-sql-server-database-azure-vms.md)所述配置备份。

2. 输入恢复服务保管库名称。 保管库是用于存储和管理所有备份的逻辑实体。 如果创建新的保管库：

    - 它将在你要保护的 SQL Server VM 所在订阅和区域中进行创建。
    - 它将使用适合所有备份的异地冗余存储 (GRS) 设置进行创建。 如果要更改冗余类型，应在保护 VM 之前进行操作。 有关详细信息，请参阅[此文章](backup-create-rs-vault.md#set-storage-redundancy)。

3. 选择备份策略。 可选择默认策略，也可选择你在保管库中创建的任何其他现有策略。 如果想要创建新策略，请参阅[此文](backup-sql-server-database-azure-vms.md#create-a-backup-policy)获取分步指南。

    ![选择备份策略](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. 选择“启用备份”。 此操作可能需要几分钟才能完成。

    ![选择“启用备份”](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. 操作完成后，横幅中会显示保管库名称。

    ![保管库名称显示在横幅中](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. 选择横幅转到保管库视图，你可在这里看到所有已注册的 VM 及其保护状态。

    ![显示已注册的 VM 的保管库视图](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. 对于非市场映像，注册可能会成功，但在向 Azure 备份扩展授予对 SQL Server 的权限之前，可能不会触发“配置备份”。 在这种情况下，“备份就绪情况”列会显示“未就绪” 。 你需要为非市场映像手动[分配适当权限](backup-azure-sql-database.md#set-vm-permissions)，以便能够触发配置备份。

    ![备份就绪情况为“未就绪”](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. 若要进一步操作或监视需要在备份的 SQL Server VM 上执行的操作，请转到相应的恢复服务保管库。 请转到“备份项”来查看在此保管库中备份的所有数据库，并触发按需备份和还原等操作。 同样，请转到“备份作业”来[监视](manage-monitor-sql-database-backup.md)与配置保护、备份和还原等操作相关的作业。

    ![请参阅“备份项”中已备份的数据库](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>对于以后可能会添加到受保护 VM 的任何新的 SQL Server 实例，不会自动配置备份。 若要对新添加的实例配置备份，需要转到 VM 注册到的保管库，并按照[此处](backup-sql-server-database-azure-vms.md)列出的步骤操作。

## <a name="next-steps"></a>后续步骤

了解如何：

- [还原已备份的 SQL Server 数据库](restore-sql-database-azure-vm.md)
- [管理已备份的 SQL Server 数据库](manage-monitor-sql-database-backup.md)
