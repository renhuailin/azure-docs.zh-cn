---
title: 使用 MARS 还原卷中的所有文件
description: 了解如何使用 MARS 代理还原卷中的所有文件。
ms.topic: conceptual
ms.date: 01/17/2021
ms.openlocfilehash: 44c12809fc94f78721ab1788cb352076dfebabe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613214"
---
# <a name="restore-all-the-files-in-a-volume-using-the-mars-agent"></a>使用 MARS 代理还原卷中的所有文件

本文介绍如何使用 Microsoft Azure 恢复服务 (MARS) 代理中的“恢复数据”向导来还原整个卷中的所有备份文件。 你可以：

- 将卷中的所有备份文件还原到创建备份时所在的同一台计算机。
- 将卷中的所有备份文件还原到备用计算机。

>[!TIP]
>“卷”选项恢复指定卷中的所有备份数据。 此选项提供更快的传输速度（最高 40 MBps），建议用于恢复大型数据或整个卷。
>
>使用“单个文件和文件夹”选项可以快速访问恢复点数据。 该选项适用于恢复单个文件，建议文件总大小不超过 80 GB。 在恢复过程中，它提供最高 6 MBps 的传输或复制速度。

## <a name="volume-level-restore-to-the-same-machine"></a>卷级别还原到同一台计算机

以下步骤将帮助你恢复卷中的所有备份文件：

1. 打开“Microsoft Azure 备份”管理单元。 如果不知道管理单元的安装位置，请在计算机或服务器中搜索“Microsoft Azure 备份”。 该桌面应用应会显示在搜索结果中。

1. 选择“恢复数据”以启动向导。

    ![“恢复数据”菜单](./media/restore-all-files-volume-mars/recover.png)

1. 若要将数据还原到同一台服务器或计算机，请在“开始”页上选择“此服务器(服务器名称)” > “下一步”。  

    ![“入门”页](./media/restore-all-files-volume-mars/same-machine-instant-restore.png)

1. 在“选择恢复模式”页中，选择“卷” > “下一步”。

    ![选择恢复模式](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. 在“选择卷和日期”页中，选择要还原的卷。

    在日历中选择恢复点。 以 **粗体** 显示的日期表示至少有一个可用的恢复点。 如果单个日期有多个可用的恢复点，请从“时间”下拉菜单中选择特定的恢复点。

     ![选择卷和日期](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. 在“指定恢复选项”中，配置还原行为。
    1. 选择恢复目标：
        - **原始位置**：将数据还原到原始路径。
        - **其他位置**：指定要将数据还原到的备用位置。
    1. 选择 **备份中的项目已在恢复目标中时** 的行为：
        - **创建副本以便同时具有两个版本**：如果已存在同名的文件，恢复点中的数据将还原为副本。 此副本将使用本地还原作业时间作为本地化文件名前缀，如以下格式之一：
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **使用恢复的版本覆盖现有版本**：如果已存在同名的文件，则会将其内容替换为恢复点中的数据。
        - **不恢复已存在于恢复目标中的项目**：如果已存在同名的文件，则跳过此文件。
    1. 如果应还原恢复点中文件的原始权限，请 **将访问控制列表(ACL)权限还原到正在恢复的文件或文件夹**。
        ![指定恢复选项](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. 验证“确认”窗格中的恢复详细信息，并选择“恢复” 。

    ![确认详细信息](./media/restore-all-files-volume-mars/confirmation-details.png)

1. 在“恢复进度”页中，监视恢复作业的进度。 你也可以安全关闭此向导，恢复操作将在后台继续进行。 在仪表板中双击此恢复作业，可以再次查看进度。

## <a name="volume-level-restore-to-an-alternate-machine"></a>卷级别还原到备用计算机

以下步骤将帮助你将卷中的所有备份文件恢复到备用计算机。 如果整个服务器断开连接，可以使用以下步骤从 Azure 备份中恢复数据。

这些步骤使用了以下术语：

- *源计算机* – 从中创建备份并且当前不可用的原始计算机。
- *目标计算机* – 要将数据恢复到的计算机。
- *示例保管库* – 源计算机和目标计算机注册到的恢复服务保管库。

> [!NOTE]
> 无法将备份还原到运行较低 操作系统版本的目标计算机。 例如，在 Windows 7 计算机中创建的备份可以还原到 Windows 7（或更高版本）的计算机上。 在 Windows 10 计算机上创建的备份无法还原到 Windows 7 计算机。

1. 在目标计算机中打开“Microsoft Azure 备份”管理单元。

1. 确保目标计算机和源计算机已还原到同一个恢复服务保管库。

1. 选择“恢复数据”打开“恢复数据向导”。 

    ![Azure 备份的屏幕截图，其中突出显示了“恢复数据”（还原到备用计算机）](./media/backup-azure-restore-windows-server/recover.png)

1. 在“开始”页上选择“另一台服务器”。 

    ![恢复数据向导“开始”页的屏幕截图（还原到备用计算机）](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

1. 提供对应于示例保管库的保管库凭据文件，并选择“下一步”。

    如果保管库凭据文件无效（或已过期），请在 Azure 门户中[从示例保管库下载新的保管库凭据文件](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file)。 提供有效的保管库凭据后，会显示相应备份保管库的名称。

1. 在“选择备份服务器”窗格中，从显示的计算机列表中选择源计算机并提供通行短语。 然后，选择“下一步”。

    ![恢复数据向导“选择备份服务器”页的屏幕截图（还原到备用计算机）](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

1. 在“选择恢复模式”页中，选择“卷” > “下一步”。

    ![选择恢复模式](./media/restore-all-files-volume-mars/select-recovery-mode.png)

1. 在“选择卷和日期”页中，选择要还原的卷。

    在日历中选择恢复点。 以 **粗体** 显示的日期表示至少有一个可用的恢复点。 如果单个日期有多个可用的恢复点，请从“时间”下拉菜单中选择特定的恢复点。

     ![选择卷和日期](./media/restore-all-files-volume-mars/select-volume-and-date.png)

1. 在“指定恢复选项”中，配置还原行为。
    1. 选择恢复目标：
        - **原始位置**：将数据还原到原始路径。
        - **其他位置**：指定要将数据还原到的备用位置。
    1. 选择 **备份中的项目已在恢复目标中时** 的行为：
        - **创建副本以便同时具有两个版本**：如果已存在同名的文件，恢复点中的数据将还原为副本。 此副本将使用本地还原作业时间作为本地化文件名前缀，如以下格式之一：
            - `YYYY-MM-DD HH-mm Copy of <original file name>`
            - `YYYY-MM-DD HH-mm Copy (n) of <original file name>`
        - **使用恢复的版本覆盖现有版本**：如果已存在同名的文件，则会将其内容替换为恢复点中的数据。
        - **不恢复已存在于恢复目标中的项目**：如果已存在同名的文件，则跳过此文件。
    1. 如果应还原恢复点中文件的原始权限，请 **将访问控制列表(ACL)权限还原到正在恢复的文件或文件夹**。
        ![指定恢复选项](./media/restore-all-files-volume-mars/specify-recovery-options.png)

1. 验证“确认”窗格中的恢复详细信息，并选择“恢复” 。

    ![确认详细信息](./media/restore-all-files-volume-mars/confirmation-details.png)

1. 在“恢复进度”页中，监视恢复作业的进度。 你也可以安全关闭此向导，恢复操作将在后台继续进行。 在仪表板中双击此恢复作业，可以再次查看进度。

## <a name="next-steps"></a>后续步骤

- 恢复文件和文件夹后，可 [管理备份](backup-azure-manage-windows-server.md)。
- 查找[有关备份文件和文件夹的常见问题](backup-azure-file-folder-backup-faq.md)。
