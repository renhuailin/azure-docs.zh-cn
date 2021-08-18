---
title: 在 BareMetal 基础结构上配置适用于 Oracle 的 SnapCenter
description: 了解如何在 BareMetal 基础结构上配置适用于 Oracle 的 SnapCenter。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: cd3163b90d65f3e33fa56a190ed854069afd6703
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579201"
---
# <a name="configure-snapcenter-for-oracle-on-baremetal-infrastructure"></a>在 BareMetal 基础结构上配置适用于 Oracle 的 SnapCenter

本文将逐步介绍如何将 NetApp SnapCenter 配置为在 BareMetal 基础结构上运行 Oracle。

## <a name="add-storage-hosts-to-snapcenter"></a>将存储主机添加到 SnapCenter

首先，将存储主机添加到 SnapCenter。 

发起 SnapCenter 会话并保存安全豁免时，应用程序将启动。 使用 Active Directory 凭据在虚拟机 (VM) 上登录 SnapCenter。

https://\<hostname\>:8146/

现在即可添加生产存储位置和辅助存储位置。

### <a name="add-the-production-storage-location"></a>添加生产存储位置

若要添加生产存储虚拟机 (SVM)，请执行以下操作：

1. 在左侧菜单中选择“存储系统”，然后选择“+ 新建” 。

2. 输入“添加存储系统”信息：

      - 存储系统：输入 Microsoft Operations 提供的 SVM IP 地址。
      - 输入创建的用户名；默认为 snap center。
      - 输入 Microsoft Operations [使用 REST 修改客户密码](set-up-snapcenter-to-route-traffic.md#steps-to-integrate-snapcenter)时创建的密码。
      - 将“向存储系统发送失败操作的自动支持通知”保留未勾选状态。
      - 选择“将 SnapCenter 事件记录到 syslog”。

3. 选择“提交”。

    验证存储后，将显示添加的存储系统的 IP 地址以及输入的用户名。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/oracle-baremetal-snapcenter-add-production-storage-complete.png" alt-text="屏幕截图显示了添加的存储系统的 IP 地址。":::

    默认为每个租户一个 SVM。 如果客户拥有多个租户，建议配置 SnapCenter 中的所有 SVM。

### <a name="add-secondary-storage-location"></a>添加辅助存储位置

若要添加灾难恢复 (DR) 存储位置，主位置和 DR 位置中的客户订阅必须对等互连。 请联系 Microsoft Operations 获取帮助。

添加辅助存储位置与添加主存储位置类似。 但主位置和 DR 位置建立对等互连后，应对辅助位置上的存储执行 ping 操作以验证对辅助存储位置的访问。 

>[!NOTE]
>如果 ping 操作不成功，通常是因为客户端虚拟 LAN (VLAN) 的主机上不存在默认路由。

验证 ping 操作后，重复执行用于添加主存储的步骤，现在仅针对 DR 主机上的 DR 位置。 建议在两个位置使用同一证书，但这并不是必须的。

## <a name="set-up-oracle-hosts"></a>设置 Oracle 主机

>[!NOTE]
>此过程适用于所有 Oracle 主机，无论它们所处的位置是在生产环境中还是在灾难恢复中。

1. 在将主机添加到 SnapCenter 并安装 SnapCenter 插件之前，必须安装 JAVA 1.8。 在继续操作前，确认每个主机是否都安装了 Java 1.8。

2. 在每个 Oracle Real Application Clusters (RAC) 主机上创建相同的非根用户并添加到 /etc/sudoers。 你将需要提供一个新密码。

3. 创建用户后，必须将其添加到 /etc/sudoers，并提供一系列显式权限。 浏览到 SnapCenter VM 上的 C:\ProgramData\NetApp\SnapCenter\Package Repository 位置并打开文件 oracle\_checksum 即可查看这些权限。

4. 根据 sudo 包，复制相应的命令集，其中 LINUXUSER 替换为新创建的用户名。 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/netapp-snapcenter-oracle-checksum-details.png" alt-text="oracle_checksum 文件详细信息的屏幕截图。":::

    将 sudo 包 1.8.7 或更高版本的命令集复制到 /etc/sudoers。

5. 将用户添加到 sudoers 后，在 SnapCenter 中选择“设置” > “凭据” > “新建”  。

6. 填写“凭据”框，如下所示：

    - **凭据名称**：提供可标识用户名和 sudoers 的名称。
    - **身份验证**：Linux
    - **用户名**：提供新创建的用户名。
    - **密码**：<Enter Password>
    - 勾选“使用 sudo 权限”框
    
7. 选择“确定”。

8. 选择左侧导航中的“主机”，然后选择“+ 添加” 。

9. 为“添加主机”输入以下值：

    - **主机类型**：Linux
    - **主机名**：输入主要 RAC 主机的 FQDN 或主要 RAC 主机的 IP 地址。
    - **凭据**：选择下拉菜单并为 sudoers 选择新创建的凭据。
    - 勾选“Oracle 数据库”框。

    >[!NOTE]
    >必须输入实际的 Oracle 主机 IP 地址之一。 不支持输入节点 VIP 或扫描 IP。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-details.png" alt-text="屏幕截图显示了新主机的详细信息。":::
    
10. 选择“更多选项”。 确保选中“在 Oracle RAC 中添加所有主机”。 依次选择“保存”和“提交” 。

11. 插件安装程序现在将尝试与提供的 IP 地址进行通信。 如果通信成功，选择“确认并提交”以确认提供的 Oracle RAC 主机的标识。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-confirm-fingerprint.png" alt-text="屏幕截图显示了新的主机指纹。":::

12. 确认初始 Oracle RAC 后，SnapCenter 将尝试进行通信并确认群集中的所有其他 Oracle RAC 服务器。 选择“确认其他并提交”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-add-hosts-fingerprint-authentication.png" alt-text="屏幕截图显示了指纹身份验证。":::

    此时将显示托管主机的状态屏幕，其中说明 SnapCenter 正在安装所选的 Oracle 插件。 可以通过查看任何位于 Oracle RAC 主机上 /opt/NetApp/snapcenter/scc/logs/DISCOVER-PLUGINS\_0.log 中的日志来检查安装进度。 

    成功安装插件后，将显示以下屏幕。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-installed-plugins.png" alt-text="屏幕截图显示了所有已安装的 SnapCenter 插件。":::

## <a name="add-credentials-for-oracle-recovery-manager"></a>添加 Oracle 恢复管理器的凭据

Oracle 恢复管理器 (RMAN) 目录身份验证方法针对 RMAN 目录数据库进行身份验证。 如果已配置外部目录机制并注册了数据库来编制数据库目录，则需添加 RMAN 目录身份验证。 可以随时添加 RMAN 凭据。

1. 在 SnapCenter 中，选择左侧菜单上的“设置”，然后选择“凭据’ 。 选择右上角的“新建”。

2. 输入“凭据名称”，调用 SnapCenter 中存储的 RMAN 凭据。 在“身份验证”下拉菜单中，选择“用于身份验证的 Oracle RMAN 目录” 。 输入用户名和密码。 选择“确定”。

3. 添加凭据后，必须在 SnapCenter 中修改数据库设置。 选择资源中的数据库。 选择主窗口右上角的“数据库设置”。

4. 在“数据库设置”屏幕上，选择“配置数据库”。

5. 展开“配置 RMAN 目录设置”。 选择先前为此数据库设置的“使用现有凭据”下拉菜单，然后选择适当的选项。 为该数据库添加 TNS 名称。 选择“确定”。

    >[!NOTE]
    >在上一步骤中应该为每个唯一的用户名和密码组合创建不同的凭据。 如果愿意，SnapCenter 可以接受所有数据库的一组凭据。
    >
    >即使 RMAN 凭据已添加到数据库，也不会为 RMAN 编制目录，除非还为每个保护策略勾选了“使用 Oracle 恢复管理器 (RMAN) 进行目录备份”，如以下有关（根据备份策略）创建保护策略的部分所述。

## <a name="create-protection-policies"></a>创建保护策略

将主机成功添加到 SnapCenter 后，即可创建保护策略。 

在左侧菜单中选择“资源”。 SnapCenter 将显示 RAC 主机上存在的所有已识别数据库。 在保护方案中忽略了 bn6p1X 和网格的单实例类型。 此时，全部的状态应为“不受保护”。

如[概述](netapp-snapcenter-integration-oracle-baremetal.md#oracle-disaster-recovery-architecture)中所述，不同的文件类型具有不同的快照频率，通过每 15 分钟进行一次存档日志备份，可实现大约 15 分钟的本地 RPO。 然后，以更长的时间间隔备份数据文件，例如每小时或更长时间一次。 因此，创建了两个不同的策略。

标识 RAC 数据库后，创建多个保护策略，包括一个用于数据文件和控制文件的策略，以及另一个用于存档日志的策略。

### <a name="datafiles-protection-policy"></a>数据文件保护策略

按以下步骤创建数据文件保护策略。

1. 在 SnapCenter 中，选择左侧菜单中的“设置”，然后选择顶部菜单中的“策略” 。 选择“新建”。

2. 选择“数据文件和控制文件”的单选按钮并向下滚动。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-new-database-backup-policy.png" alt-text="屏幕截图显示了新数据库备份策略的详细信息。":::

3. 选择“每小时一次”单选按钮。 如果编制目录需要与 RMAN 集成且已添加 RMAN 凭据，请选择“使用 Oracle 恢复管理器 (RMAN) 进行目录备份”复选框。 选择“**下一页**”。 如果勾选了目录备份，则[必须添加 RMAN 凭据](#add-credentials-for-oracle-recovery-manager)才能进行编录。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-database-backup-policy-frequency.png" alt-text="屏幕截图显示了用于选择备份策略中的计划频率的选项。":::

    >[!IMPORTANT]
    >即使所需备份频率不是每小时或每天一次等，也必须为计划策略做出选择。 实际备份频率在该过程的后面设置。 请勿选择“无”，除非此策略下的所有备份都只是按需版本。

4. 在左侧菜单中选择“保留”。 以下是为备份策略设置的两种保留设置。 第一种保留设置是要保留的最大按需快照数。 根据备份策略保留一定数量的快照，例如以下示例中 48 个按需快照保留 14 天。 根据备份策略设置相应的按需保留策略。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/snapcenter-new-database-backup-policy-retention.png" alt-text="屏幕截图显示了数据库备份策略保留设置。":::

5. 第二种保留设置是根据先前每小时、每天、每周一次等的条目而要保留的计划快照数。选择“下一步”。

6. 在“复制”屏幕上，选择“创建本地快照副本后更新 SnapMirror”复选框，然后选择“下一步”  。 其他条目保留默认值。

    >[!NOTE]
    >Oracle BareMetal 基础结构环境当前不支持 SnapVault。

    稍后可以通过返回到“保护策略”屏幕，并选择左侧菜单中的“修改保护策略”和“复制”来添加复制 。

7. “脚本”页面用于输入在进行 Oracle 备份前后需要运行的任意脚本。 目前，不支持 SnapCenter 过程中的脚本。 选择“**下一页**”。

8. 在左侧菜单中选择“验证”。 如果想要能够验证快照的可恢复性完整性，请选择“每小时一次”旁的复选框。 当前不支持验证脚本命令。 选择“**下一页**”。

    >[!NOTE]
    >后面部分“将保护策略分配给资源”中将添加验证过程的实际位置和计划。

9. 在“摘要”页面上，验证所有设置是否均按预期输入并选择“完成” 。

### <a name="archive-logs-protection-policy"></a>存档日志保护策略

再次按上述步骤创建存档日志保护策略。 在步骤 2 中，选择“存档日志”单选按钮，而不是“数据文件和控制文件”单选按钮。

## <a name="assign-protection-policies-to-resources"></a>将保护策略分配给资源

创建策略后，需将其与资源关联，以便 SnapCenter 开始按该策略执行。

### <a name="datafiles-resource-group"></a>数据文件资源组

1. 选择左侧菜单中的“资源”，然后在主窗口右上角选择“新建资源组” 。

2. 为该资源组添加名称和任意标签，以便搜索。

    >[!NOTE]
    >建议勾选“为快照副本使用自定义名称格式”并添加以下条目：$ResourceGroup、$Policy 和 $ScheduleType   。 这可确保快照前缀符合 SnapCenter 标准，并且可确保通过快照名称可以一目了然地了解必要的详细信息。 如果将“为快照副本使用自定义名称格式”保留未勾选状态，则“名称”中的任何条目都将成为所创建快照的前缀 。 确保输入的名称标识数据库和受保护的内容，例如数据文件和控制文件。 

3. 在“备份”设置下，添加“/u95”以拒绝存档日志。

4. 在“资源”页面上，将受同一备份保护策略保护的所有数据库从“可用资源”移动到“选定资源”。 请勿为每个主机添加 Oracle 数据库实例，只需添加数据库。 选择“**下一页**”。

5. 为先前创建的数据文件和控制文件选择保护策略。 选择策略后，选择“配置计划”下的铅笔。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-datafiles-resource-group-available-policies.png" alt-text="屏幕截图显示了选择保护策略以配置计划。":::

6. 添加要执行的选定策略的计划。 确保开始日期晚于当前日期和时间。

    >[!NOTE]
    >计划频率无需与创建策略时所选的频率相同。 对于每小时一次，建议直接保留“每 1 小时重复一次”。 开始时间即每小时内开始进行备份的时间。 确保保护数据文件的计划与保护存档日志的计划不重叠，因为在指定时间只能有一个备份处于活动状态。

7. 验证确保创建的快照可用。 验证过程非常广泛，包括创建所有 Oracle 数据库卷的克隆、将数据库装载到主要主机，以及验证其可恢复性。 该过程既耗时又耗资源。 如果要配置验证计划，请选择 + 符号。

    >[!NOTE]
    >验证过程将长时间占用主机上的资源。 建议添加验证时在辅助位置的主机上执行此操作（如果可用）。
    
    以下屏幕截图显示了对创建时未启用复制且未复制快照的新资源组的验证。 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-resource-group-verification-schedules.png" alt-text="屏幕截图显示了如何为资源组配置验证计划。":::

    如果已启用复制并且将使用灾难恢复位置中的主机进行验证，则直接跳至以下小节中的步骤 2。 通过该步骤可加载辅助定位器，以验证辅助位置上的备份。

### <a name="add-verification-schedule-for-new-resource-group"></a>为新资源组添加验证计划

1. 选择“备份后运行验证”或“运行计划验证”，然后在下拉菜单中选择预先计划的频率 。 如果已启用 DR，可以选择“在辅助存储位置上验证”，从而在 DR 位置执行频率，以降低生产时的资源压力。 选择“确定”。

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/new-resource-group-add-verification-schedules.png" alt-text="屏幕截图显示了如何添加验证计划。":::

    如果无需验证或已在本地设置验证，则直接跳至以下 SMTP 设置（步骤 5）。

2. 如果创建数据文件保护策略时启用了更新 SnapMirror，并且添加了辅助存储位置，则 SnapCenter 会检测这两个位置之间的复制。 在这种情况下，会显示一个屏幕，可在其中加载辅助定位器以验证辅助位置上的备份。 选择“加载定位器”。

3. 选择加载定位器后，SnapCenter 将显示所发现的包含数据文件和控制文件的 SnapMirror 关系。 这些应与 Microsoft Operations 提供的灾难恢复框架相匹配。 选择“配置计划”下的铅笔。

4. 选择“在辅助存储位置上验证”复选框。 

5. 如果 SMTP 服务器可用，SnapCenter 可以向 SnapCenter 管理员发送电子邮件。 如果要发送电子邮件，请输入以下内容。

    - **电子邮件首选项**：输入电子邮件接收频率的首选项。
    - **发件人**：输入 SnapCenter 用于发送电子邮件的电子邮件地址。
    - **收件人**：输入 SnapCenter 向其发送电子邮件的电子邮件地址。
    - **主题**：输入 SnapCenter 发送电子邮件时使用的主题。
    - 选择“附加作业报表”复选框。
    - 选择“**下一页**”。

6. 确认“摘要”页中的设置，然后选择“完成”。

创建资源组后，若要标识资源组，请执行以下操作：

1. 在左侧菜单中选择“资源”。
2. 选择“视图”旁主窗口中的下拉菜单，然后选择“资源组”。
 
    如果这是第一个资源组，则只显示为数据文件和控制文件新创建的资源组。 如果还创建了存档日志资源组，则还会显示该资源组。

### <a name="archive-log-resource-group"></a>存档日志资源组

若要将保护策略分配到存档日志资源组，请按照在将保护策略分配到数据文件资源组时采用的步骤进行操作。 只存在下述区别：

- 在步骤 3 中，请勿添加 /u95 以拒绝存档日志；将该字段留空。
- 在步骤 6 中，建议每 15 分钟备份一次存档日志。
- 存档日志的验证选项卡为空。 

## <a name="next-steps"></a>后续步骤

了解如何在 SnapCenter 中创建 Oracle Database 的按需备份：

> [!div class="nextstepaction"]
> [在 SnapCenter 中创建按需备份](create-on-demand-backup-oracle-baremetal.md)
