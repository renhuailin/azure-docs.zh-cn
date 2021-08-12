---
title: 还原 Oracle 数据库
description: 了解如何使用 SnapCenter 在 BareMetal 基础结构上还原 Oracle Database。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/07/2021
ms.openlocfilehash: 3d7f417b6881fd44d67011d33e4fdde87ff2a6e1
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576359"
---
# <a name="restore-oracle-database"></a>还原 Oracle 数据库

本文逐步介绍如何使用 SnapCenter 在 BareMetal 基础结构上还原 Oracle Database。

在 BareMetal 基础结构上还原 Oracle Database 的选项有好几种。 我们建议在开始还原之前，先咨询 [Oracle 还原决策矩阵](oracle-high-availability-recovery.md#choose-your-method-of-recovery)。 在给定还原时间和可能丢失数据的情况下，此矩阵可以帮助你选择最适当的还原方法。 

由于目标是将数据库还原到上一个已知的恢复点，因此你通常要还原数据和存档日志卷的最新快照。 还原快照是一个永久过程。 

>[!IMPORTANT]
>要确保还原相应的快照，你需要非常小心。 从快照还原会删除所有其他快照及其关联数据， 其中便包括比选定进行还原的快照还要更新的快照。 因此，建议你保守处理还原过程。 不确定应该还原哪一张快照时，首选时间最新的快照是错误的。

## <a name="restore-database-locally-with-restored-archive-logs"></a>使用还原的存档日志在本地还原数据库

在尝试恢复之前，数据库必须处于脱机状态（如果当时尚未脱机）。 已验证数据库未在 Oracle Real Application Clusters (RAC) 的任何节点上运行后，即可开始操作。 首先，还原存档日志。 

1. 确定可用的备份。 在 SnapCenter 的左侧菜单中，选择“资源”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-available-backups.png" alt-text="显示数据库视图的屏幕截图。":::

2. 从列表中选择要还原的数据库。 数据库将包含已创建资源组及其关联策略的列表。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/available-backups-list.png" alt-text="显示用于还原的完整可用备份列表的屏幕截图。":::

3. 随后会显示主备份列表。 备份按备份名称及其类型（日志或数据）进行标识。 首先处理日志还原，因为无法直接在 SnapCenter 还原存档日志卷。 标识需要还原的存档日志卷。 要还原的卷通常是最新备份，因为大多数恢复都将前滚上次已知的良好数据文件备份中的所有存档日志。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/primary-backups-list.png" alt-text="显示主备份列表的屏幕截图。":::

4. 选择存档日志卷后，备份列表右上角的装载选项将启用。 选择“装载”。 在“装载备份”页上，从下拉列表选择将装载备份的主机。 可以选择任意 RAC 节点，但存档日志和数据文件恢复主机必须相同。 复制装载路径，因为下一步将要使用该路径来恢复数据文件。 选择“装载”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-mount-backups.png" alt-text="显示要装载的备份的屏幕截图。":::

5. 与备份作业不同的是，此页上的作业查看器不会显示装载过程的状态。 若要查看装载状态，请在左侧菜单中选择“监视”。 然后，已运行的所有作业的状态即会突出显示。 装载应为第一个操作条目， 也可以导航到复制的装载路径，以验证存档日志是否存在。

    选定用于装载相应文件系统的主机还将在上述装载路径中收到 into/etc/fstab 条目。 恢复完成后，可将其删除。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-mount-output.png" alt-text="显示装载输出的屏幕截图。":::

6. 接下来，还原数据文件和控制文件。 与之前一样，从左侧菜单中选择“资源”，然后选择要还原的数据库。 选择要还原的数据库。 还原的备份通常同样是最新备份。 这次在选择数据备份时，还原选项将启用。 选择“还原”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-selected-data-backup.png" alt-text="要还原的数据备份的屏幕截图。":::

7. 在“还原范围”选项卡上：从下拉列表选择之前选定用于装载日志文件的同一主机。 确保选中“所有数据文件”，然后选中“控制文件”复选框。 选择“**下一页**”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-recovery-scope.png" alt-text="显示数据文件还原范围详细信息的屏幕截图。":::

8. 在“恢复范围”选项卡上：已装载日志备份的系统更改号 (SCN) 已经输入。 “直至 SCN”当前也已选中。 
 
    SCN 可以在每个备份的完整备份列表中找到。 “指定外部存档日志文件位置”下也已经输入先前复制的装入点位置。 本文档目前不涵盖有关处理多个存档日志还原位置的内容。 选择“**下一页**”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-choose-recovery-scope.png" alt-text="显示恢复范围选项的屏幕截图。":::

9. 在“PreOps”选项卡上：当前不建议在预还原过程中使用任何脚本。 选择“**下一页**”。

10. 在“PostOps”选项卡上：还原后过程同样不支持使用任何脚本。 选中“恢复后以读写模式打开数据库或容器数据库”复选框。 选择“**下一页**”。

11. 在“通知”选项卡上：如果 SMTP 服务器可用，并且你想要接收有关还原过程的电子邮件通知，请填写电子邮件设置。 选择“**下一页**”。

12. 在“摘要”选项卡上：验证所有详细信息是否正确。 选择“完成”  。

13. 在“活动”底部屏幕中选择还原作业，即可查看还原状态。 选择绿色箭头打开每个还原的子部分及其进度，即可跟踪还原进度。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-job-details.png" alt-text="显示还原作业详细信息的屏幕截图。":::

    还原完成后，所有步骤都将更改为勾号标记。 选择“关闭”。

    所创建的简单数据库中已删除的行现已验证为已还原。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/output-of-restored-files.png" alt-text="显示已还原文件输出验证的屏幕截图。":::

## <a name="clone-database-locally-or-remotely"></a>在本地或远程克隆数据库

克隆数据库与还原过程很相似。 克隆数据库操作可以在本地或远程使用，具体取决于目标结果。 在灾难恢复 (DR) 位置克隆数据库适用于多种用途，包括灾难恢复故障转移测试和生产数据质量保证。 此情形假定灾难恢复位置可用于测试、开发、质量保证等。

### <a name="create-a-clone"></a>创建克隆

创建克隆是 SnapCenter 中的一项功能，可用于将快照用作时间点引用，从而使用指针采集父卷和克隆卷之间的类似数据集。 然后，克隆卷将可读写，并且只能通过写入进行扩展，而父卷上仍可进行读取操作。 使用此功能可以创建一组供主机使用的“重复”数据，而且不会干扰父卷上的数据。 

此功能对于灾难恢复测试特别有用，因为临时文件系统可以基于将在实际恢复中使用的同一快照进行创建。 你可以验证数据以及应用程序是否按预期正常工作，然后关闭灾难恢复测试，而不会影响灾难恢复卷或复制。

下面介绍克隆数据库的步骤。 

1. 在 SnapCenter 中，选择“资源”，然后选择要克隆的数据库。 如果要在本地创建克隆，请继续执行下一步。 如果要在辅助位置还原克隆，请选择“镜像副本”框上方的备份。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-manage-copies.png" alt-text="克隆数据库托管副本示意图。":::

2. 从提供的备份列表中选择适当的数据备份。 选择“克隆”。

    >[!NOTE]
    >如果需要克隆时间戳，则必须在创建时间戳或系统更改号 (SCN) 之前先创建数据备份。 

3. 在“名称”选项卡上：输入克隆的 SID 的名称。 验证数据及存档日志的源卷和目标卷是否与 Microsoft Operations 为灾难恢复映射提供的卷相匹配 。 选择“**下一页**”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/provide-clone-database-sid.png" alt-text="显示数据库 SID 输入位置的屏幕截图。":::

4. 在“位置”选项卡上：克隆操作将会在“数据文件位置”、“控制文件”和“重做日志”为恢复创建必要的文件系统。 因此，我们建议不要对其作出任何修改。 本文档不涵盖替代方法的相关内容。 请务必为还原位置选择适当的主机。 选择“**下一页**”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-select-host.png" alt-text="显示如何选择待克隆主机的屏幕截图。":::

5. 在“凭据”选项卡上：从现有生产位置拉取“凭据”和“Oracle 主设置”值。 建议不要更改这些值，除非你知道辅助位置的值与主位置不同。 选择“**下一页**”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-credentials.png" alt-text="输入克隆数据库凭据的屏幕截图。":::

6. 在“PreOps”选项卡上：当前不支持任何预恢复脚本。 选择“**下一页**”。

7. 在“PostOps”选项卡上：若仅在特定时间或 SCN 之前恢复数据库，请选择相应的单选按钮，并添加时间戳或 SCN。 在到达该时间戳或 SCN 之前，SnapCenter 将持续恢复数据库。 否则，请保留选中“直至取消”。 选择“**下一页**”。

8. 在“通知”选项卡上：如果想要在克隆完成后发送通知电子邮件，请输入必要的 SMTP 信息。 选择“**下一页**”。

9. 在“摘要”选项卡上：验证输入的“克隆 SID”是否适当，以及选择的主机是否正确。 向下滚动，确保输入的恢复范围正确无误。 选择“完成”  。

10. 克隆作业将显示在屏幕底部的“活动”弹出窗口中。 选择克隆活动以显示作业详细信息。 活动完成后，“作业详细信息”页的全部作业将显示绿色勾号标记并提供完成时间。 克隆过程通常约需 7-10 分钟。

11. 作业完成后，切换到用来克隆的目标主机，并使用 cat/etc/fstab 验证装入点。 此验证可确保克隆向导期间列出的数据库具有适当装入点，还会突出显示在向导中输入的数据库 SID。 在下方示例中，SID 为依据主机上的装入点给定的 dbsc4。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-switch-to-target-host.png" alt-text="用于切换到目标主机的命令的屏幕截图。":::

12. 在主机上，键入“oraenv”，然后按“enter”。 输入新创建的 SID。

    你可以自行决定验证数据库是否已正确还原。 但是，以下添加的步骤是基于非 Oracle 用户创建的数据库。 

13. 输入“sqlplus/as sysdba”。 此表由其他用户创建，因此自动输入的原始用户名和密码均无效。 输入正确的用户名和密码。 成功登录后，SQL> 提示即会显示。

输入基本数据库查询，以验证能否接收正确数据。 在下方示例中，我们使用存档日志前滚数据库。 下文显示，所使用的存档日志正确无误，因为 clonetest 条目的确是在数据备份之后创建。 因此，如果存档日志没有前滚，则该条目不会在表中列出。

```sql
SQL> select * from acolvin.t;

COL1
-----------------------------------------
COL2
---------------
first insert
17-DEC-20

log restore
17-DEC-20

clonetest
18-DEC-20

COL1
-----------------------------------------
COL2
---------------
after first insert
17-DEC-20

next insert
17-DEC-20

final insert
18-DEC-20

COL1
-----------------------------------------
COL2
---------------
BILLY
17-DEC-20

7 rows selected.

```

### <a name="delete-a-clone"></a>删除克隆

完成克隆后，一定要删除克隆。 如果打算继续使用，则应该拆分克隆。 父克隆的快照不得删除，其会作为保留计数的一部分予以跳过。 如果未删除完成后的克隆，则将要维护的大量快照可能会占用过多存储空间。 

拆分克隆会将现有卷和快照中的数据复制到新卷中。 此过程将对克隆和父快照之间的关系进行处理，以允许系统在达到一定的保留数目时删除快照。 如果已经达到保留数目，则系统将在进行下一次快照时删除快照。 拆分克隆同时还会产生存储成本。

创建克隆后，相应数据库的“资源”选项卡将会列出一个克隆（无论是本地还是远程）。 如要删除克隆：

1. 在“资源”选项卡上：选中包含要删除的克隆的选框。

2. “辅助镜像克隆”页将显示该克隆。 在此示例中，该克隆正位于辅助位置。 选择克隆列表右上角的“删除”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/delete-clone-secondary-mirror-clones.png" alt-text="辅助镜像克隆的屏幕截图。":::

3. 请先确认已从 SQLPLUS 退出，然后再执行。 选择“确定”。

4. 若要查看作业进度，请在左侧菜单中选择“作业”。 成功删除克隆后，绿色勾号标记即会显示。

5. 删除克隆后，也许还可以卸载在克隆过程中用到的存档日志（如适用）。 返回（在创建克隆时生成的）相应备份列表，此时可依据是否已装载备份来对备份列表进行排序。 排序算法尚待改进，因此不会始终对所有项进行排序，而是通常按正确方向进行排序。 以下都是之前装载的卷。 选择“卸载”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/delete-clone-unmount-backup.png" alt-text="显示备份列表的屏幕截图。":::

6. 选择“确定”。 通过在左侧菜单中选择“监视器”，可以查看卸载作业的状态。 成功卸载备份时，一个绿色的勾号标记即会显示。

### <a name="split-a-clone"></a>拆分克隆

通过将父卷中的所有数据复制到使用快照创建既有克隆的位置，拆分克隆可创建父卷副本。 此过程会将父卷和克隆卷分开，并删除用于创建克隆卷的快照上的保留项。 之后，快照可以作为保留策略的一部分予以删除。

拆分克隆可用于在生产环境或灾难恢复环境中填充数据。 拆分后，新卷可以独立于父卷进行运行。

>[!NOTE]
>拆分克隆进程无法撤消或取消。

如要拆分克隆：

1. 选择已有克隆的数据库。

2. 选定克隆位置后，其将在“辅助镜像克隆”页的列表中显示。

3. 在克隆列表的正上方，选择“克隆拆分”。 SnapCenter 禁止在拆分进程中作出任何更改，因此下一页会简单显示将会发生的更改。 选择“开始”。

    >[!NOTE]
    >拆分进程可能需要很长时间，具体取决于必须复制的数据量、存储中的数据库布局以及存储的活动级别。

完成此进程后，已拆分的克隆将会从备份列表中删除，并且与该克隆关联的快照现可在 SnapCenter 上作为正常保留计划的一部分予以删除。

## <a name="restore-database-remotely-after-disaster-recovery-event"></a>在发生灾难恢复事件后远程还原数据库

SnapCenter 当前无法用于自动执行故障转移过程。 如果发生灾难恢复事件，则需要使用 Microsoft Operations 或 REST 恢复脚本来还原辅助位置中的数据库。 本文档目前不会详细介绍执行 REST 恢复脚本的过程。

## <a name="restart-all-rac-nodes-after-restore"></a>还原后重启所有 RAC 节点

当在 SnapCenter 成功还原数据库后，这些数据库将只会在你当时还原数据库时选择的 RAC 节点上活动，如下所示。 在此示例中，我们已将数据库还原到 bn6sc2，这足以说明你可以选择任意 RAC 节点来执行还原。

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-example-output-rac-node.png" alt-text="显示已将数据库还原到 bn6sc2 的屏幕截图。":::

使用“srvctl start database”命令启动其余 RAC 节点。 命令生效后，验证所有 RAC 节点是否正处于参与状态。

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-srvctl-start-database-command.png" alt-text="显示用于重启其余 RAC 节点的 srvctl start database 命令的屏幕截图。":::

## <a name="unmount-log-archive-volume"></a>卸载日志存档卷

克隆或还原数据库后，应卸载日志存档卷，因为仅用于还原数据库的这些存档卷之后将不再使用。 

1. 在“资源”选项卡上选择本地列表或远程列表，然后在数据库上选择相应的备份列表。

2. 选择相应备份列表后，按是否已装载备份对备份列表进行排序。 排序算法尚待改进，因此不会始终对所有项进行排序，而是提供可用结果。 
 
3. 找到先前装载的目标卷后，选择“卸载”。 选择“确定”。

4. 通过在左侧菜单中选择“监视器”，可查看卸载作业的状态。 成功卸载日志存档卷时，一个绿色勾号将会显示。

>[!NOTE]
>选择卸载即会从装载卷的主机上的 /etc/fstab 中删除相应条目。 此外，卸载作业还会根据保护策略中设置的保留策略，在必要时释放备份以供删除。

## <a name="next-steps"></a>后续步骤

详细了解 BareMetal 上的 Oracle 的高可用性和灾难恢复：

> [!div class="nextstepaction"]
> [BareMetal 上的 Oracle 的高可用性和灾难恢复](concepts-oracle-high-availability.md)
