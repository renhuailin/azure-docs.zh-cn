---
title: 管理和监视 MARS 代理备份
description: 了解如何使用 Azure 备份服务管理和监视 Microsoft Azure 恢复服务 (MARS) 代理备份。
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/05/2021
ms.openlocfilehash: 525bdff82c224b02b941354983276747b483ae56
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535104"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>使用 Azure 备份服务管理 Microsoft Azure 恢复服务 (MARS) 代理备份

本文介绍如何管理使用 Microsoft Azure 恢复服务代理备份的文件与文件夹。

## <a name="modify-a-backup-policy"></a>修改备份策略

修改备份策略时，可以添加新项、从备份中删除现有项，或使用“排除设置”来排除文件，使其不会被备份。

- **添加项**：此选项仅用于添加要备份的新项。 若要删除现有项，请使用“删除项”或“排除设置”选项。   
- **删除项**：使用此选项可以删除项，使其不会被备份。
  - 使用“排除设置”会删除卷中的所有项，此选项的功能与“删除项”不同。 
  - 清除卷中的所有选择内容会导致系统根据上次备份时的保留设置保留项的旧备份，且这些备份没有可修改的余地。
  - 重新选择这些项会生成第一个完整备份，且新的策略更改不会应用到旧备份。
  - 取消选择整个卷会保留以前的备份，且保留策略没有任何可修改的余地。
- **排除设置**：使用此选项可以排除特定项，使其不会被备份。

### <a name="add-new-items-to-existing-policy"></a>将新项添加到现有策略

1. 在“操作”中选择“计划备份” 。

    ![计划 Windows Server 备份](./media/backup-configure-vault/schedule-first-backup.png)

2. 在“选择策略项”选项卡中，选择“修改文件和文件夹的备份计划”并选择“下一步”  。

    ![选择策略项](./media/backup-azure-manage-mars/select-policy-items.png)

3. 在“修改或停止计划备份”选项卡中，选择“对备份项或时间进行更改”并选择“下一步”  。

    ![修改或计划备份](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. 在“选择要备份的项”选项卡中，选择“添加项”以添加要备份的项 。

    ![修改或计划备份 - 添加项](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. 在“选择项”窗口中选择要添加的文件或文件夹，然后选择“确定” 。

    ![选择项](./media/backup-azure-manage-mars/select-item.png)

6. 完成后续步骤，然后选择“完成”以完成操作。

### <a name="add-exclusion-rules-to-existing-policy"></a>将排除规则添加到现有策略

可以添加排除规则，以跳过你不想要备份的文件和文件夹。 可以在定义新策略或修改现有策略期间执行此操作。

1. 在“操作”窗格中，选择“计划备份”。 转到“选择要备份的项”并选择“排除设置” 。

    ![排除设置](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. 在“排除设置”中，选择“添加排除项” 。

    ![添加排除项](./media/backup-azure-manage-mars/add-exclusion.png)

3. 在“选择要排除的项”中浏览文件和文件夹，选择要排除的项，然后选择“确定” 。

    ![选择要排除的项](./media/backup-azure-manage-mars/select-items-exclude.png)

4. 默认会排除所选文件夹中的所有 **子文件夹**。 可以选择“是”或“否”对此进行更改。  可按如下所示编辑并指定要排除的文件类型：

    ![选择子文件夹类型](./media/backup-azure-manage-mars/subfolders-type.png)

5. 完成后续步骤，然后选择“完成”以完成操作。

### <a name="remove-items-from-existing-policy"></a>从现有策略中删除项

1. 在“操作”窗格中，选择“计划备份”。 转到“选择要备份的项”。 在列表中，选择要从备份计划中删除的文件和文件夹，然后选择“删除项”。

    ![选择要删除的项](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > 从策略中彻底删除卷时请谨慎。  如果需要再次添加该卷，该卷将被视为新卷。 下一次计划备份将执行初始备份（完整备份），而不是增量备份。 如果以后需要临时删除和添加项，建议使用“排除设置”而不是“删除项”，以确保执行增量备份而不是完整备份 。

2. 完成后续步骤，然后选择“完成”以完成操作。

## <a name="stop-protecting-files-and-folder-backup"></a>停止保护文件和文件夹备份

可通过两种方式来停止保护文件和文件夹备份：

- **停止保护并保留备份数据**。
  - 此选项会停止将来所有备份作业的保护。
  - Azure 备份服务将继续保留所有现有恢复点。  
  - 可以还原尚未过期的恢复点的备份数据。
  - 如果你决定恢复保护，可以使用“重新启用备份计划”选项。 然后，系统会根据新的保留策略保留数据。
- **停止保护并删除备份数据**。
  - 此选项会停止将来所有备份作业对数据的保护。 如果未启用保管库安全功能，则会立即删除所有恢复点。<br>如果启用安全功能，删除会延迟 14 天，你将收到一封警报电子邮件，其中包含消息“已删除此备份项的数据。此数据将暂时保留 14 天，此后将永久删除”，以及建议的操作“在 14 天内重新保护备份项以恢复数据” 。<br>在此状态下，保留策略继续应用，备份数据仍可计费。 [详细了解](backup-azure-security-feature.md#enable-security-features)如何启用保管库安全功能。
  - 若要恢复保护，请在执行删除操作后的 14 天内重新保护服务器。 在此期间，还可以将数据恢复到备用服务器。

### <a name="stop-protection-and-retain-backup-data"></a>停止保护并保留备份数据

1. 打开 MARS 管理控制台，转到“操作”窗格并选择“计划备份”。 

    ![选择计划备份](./media/backup-azure-manage-mars/mars-actions.png)
1. 在“选择策略项”页中，选择“修改文件和文件夹的备份计划”并选择“下一步”  。

    ![选择策略项](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. 在“修改或停止计划的备份”页中，选择“停止使用此备份计划，但保留存储的备份，直到再次激活计划”。  然后，选择“下一步”。

    ![停止计划的备份。](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. 在“暂停计划的备份”中复查信息，然后选择“完成” 。

    ![暂停计划的备份。](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. 在“修改备份进度”中，检查计划备份暂停是否处于成功状态，然后选择“关闭”以完成操作 。

### <a name="stop-protection-and-delete-backup-data"></a>停止保护并删除备份数据

1. 打开 MARS 管理控制台，转到“操作”窗格并选择“计划备份”。 
2. 在“修改或停止计划的备份”页中选择“停止使用此备份计划并删除所有存储的备份”选项。  然后，选择“下一步”。

    ![修改或停止计划的备份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 在“停止计划的备份”页中选择“完成”。 

    ![停止计划的备份并选择完成](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系统会提示输入安全 PIN（个人标识号）。必须手动生成该 PIN。 为此，请先登录到 Azure 门户。
5. 转到“恢复服务保管库” > “设置” > “属性”。
6. 在“安全 PIN”下选择“生成” 。 复制此 PIN。 该 PIN 的有效时间仅为五分钟。
7. 在管理控制台中粘贴该 PIN，然后选择“确定”。

    ![生成安全 PIN。](./media/backup-azure-delete-vault/security-pin.png)

8. “修改备份进度”页中会显示以下消息：“删除的备份数据会保留 14 天。该时间过后，备份数据会被永久删除。”  

    ![修改备份进度](./media/backup-azure-delete-vault/deleted-backup-data.png)

删除本地备份项后，遵循门户中的后续步骤。

## <a name="re-enable-protection"></a>重新启用保护

如果你在保留数据时停止了保护，后来决定恢复保护，可以使用“修改备份策略”重新启用备份计划。

1. 在“操作”中选择“计划备份”。 
1. 选择“重新启用备份计划。也可以修改备份项或时间”，然后选择“下一步”。<br>

    ![重新启用备份计划](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. 在“选择要备份的项”中，选择“下一步” 。

    ![选择要备份的项](./media/backup-azure-manage-mars/re-enable-next.png)
1. 在“指定备份计划”中，指定备份计划并选择“下一步” 。
1. 在“选择保留策略”中，指定保留期并选择“下一步” 。
1. 最后，在“确认”屏幕中，复查策略详细信息并选择“完成” 。

## <a name="re-generate-passphrase"></a>重新生成通行短语

使用 MARS 代理向/从 Azure 备份或还原本地计算机时，通行短语用于加密和解密数据。 如果丢失或忘记了通行短语，可以通过以下步骤重新生成通行短语（前提是计算机仍注册在恢复服务保管库中，并且已配置备份）：

1. 在 MARS 代理控制台中转到“操作”窗格  > “更改属性”>。  然后转到“加密”选项卡。<br>
1. 选中“更改通行短语”复选框。<br>
1. 输入新的通行短语，或选择“生成通行短语”。
1. 选择“浏览”以保存新的通行短语。

    ![生成通行短语](./media/backup-azure-manage-mars/passphrase.png)

1. 选择“确定”以应用更改。  如果在 Azure 门户中为恢复服务保管库启用了[安全功能](./backup-azure-security-feature.md#enable-security-features)，则系统会提示你输入安全 PIN。 若要接收该 PIN，请遵循[此文](./backup-azure-security-feature.md#authentication-to-perform-critical-operations)中列出的步骤。<br>
1. 粘贴门户中的安全 PIN，然后选择“确定”以应用更改。<br>

    ![粘贴安全 PIN](./media/backup-azure-manage-mars/passphrase2.png)
1. 确保将通行短语安全保存在备用位置（源计算机之外的位置），最好是保存在 Azure Key Vault 中。 如果正在使用 MARS 代理备份多台计算机，请跟踪所有通行短语。

## <a name="validate-passphrase"></a>验证密码

从 MARS 代理版本 2.0.9190.0 及更高版本开始，必须验证密码，以确保它满足[更新后的要求](/azure/backup/backup-azure-file-folder-backup-faq#what-characters-are-allowed-for-the-passphrase-)。

若要验证密码，请执行以下步骤：

1. 打开 MARS 控制台。

   顶部会显示一条消息，要求验证密码。 

1. 单击 **“验证”** 。

   :::image type="content" source="./media/backup-azure-manage-mars/validate-passphrase-prompt-inline.png" alt-text="显示密码验证提示的屏幕截图。" lightbox="./media/backup-azure-manage-mars/validate-passphrase-prompt-expanded.png":::

   密码验证程序将打开，并提示输入当前密码。 如果密码不满足更新后的要求，则会显示一个重新生成密码的选项。

1. 生成具有以下详细信息的密码：

   - 满足要求的新密码。
   - 安全 PIN（请参阅[生成安全 PIN 的步骤](#generate-security-pin)）。
   - 服务器上用于保存新生成的密码的安全位置。

   :::image type="content" source="./media/backup-azure-manage-mars/generate-passphrase.png" alt-text="显示生成具有所需详细信息的密码的过程的屏幕截图。":::

### <a name="validate-passphrase-for-dpmmabs-agent"></a>验证 DPM/MABS 代理的密码

对于 DPM/MABS，请从提升的命令提示符执行密码验证工具。
   
可以在以下位置之一找到该工具：

- **System Center Data Protection Manager**
     
  %ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\PassphraseValidator.exe

- **Microsoft Azure 备份服务器**
      
  %ProgramFiles%\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\PassphraseValidator.exe

密码验证程序将打开，并提示输入当前密码。 如果密码不符合更新后的要求，请重新生成密码。
   
:::image type="content" source="./media/backup-azure-manage-mars/passphrase-validator-prompts-for-current-passphrase.png" alt-text="显示当前密码的密码验证程序提示的屏幕截图。":::

请使用以下步骤：

1. 在管理控制台中，导航到“管理”选项卡，然后选择“联机” -> “配置”。
1. 按照“配置订阅设置向导”操作，并在“加密设置”步骤中，提供更新的密码。

:::image type="content" source="./media/backup-azure-manage-mars/configure-subscription-settings-wizard.png" alt-text="显示按照“配置订阅设置向导”提供密码的过程的屏幕截图。":::

## <a name="generate-security-pin"></a>生成安全 PIN

1. 转到“恢复服务保管库” -> “设置” -> “属性”。
1. 在“安全 PIN”下选择“生成” 。
 
复制 PIN。 该 PIN 的有效时间仅为五分钟。

## <a name="managing-backup-data-for-unavailable-machines"></a>管理不可用计算机的备份数据

本部分讨论以下应用场景：受 MARS 保护的源计算机不再可用，因为它已被删除、损坏、感染了恶意软件/勒索软件或已停止使用。

对于这些计算机，Azure 备份服务会根据备份策略中指定的保留规则，确保最新的恢复点不会过期（即，不会被修剪）。 因此，你可以安全地还原计算机。  请考虑以下可对备份数据执行的方案：

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>应用场景 1：源计算机不可用，你不再需要保留备份数据

- 可以使用[本文](backup-azure-delete-vault.md#delete-protected-items-on-premises)列出的步骤从 Azure 门户中删除备份数据。

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>应用场景 2：源计算机不可用，你需要保留备份数据

通过 MARS 控制台而不是门户来管理 MARS 的备份策略。 如果需要在现有恢复点过期之前扩展保留设置，则需要还原计算机、安装 MARS 控制台并扩展策略。

- 若要还原计算机，请执行以下步骤：
  1. [将 VM 还原到备用目标计算机](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. 使用与源计算机相同的主机名重新创建目标计算机
  1. 安装代理，并使用相同的密码重新注册到同一保管库
  1. 启动 MARS 客户端，以根据要求延长保留期
- 最近还原并受 MARS 保护的计算机将继续进行备份。  

## <a name="configuring-antivirus-for-the-mars-agent"></a>为 MARS 代理配置防病毒软件

建议防病毒软件采用以下配置，以免与 MARS 代理的操作发生冲突。

1. **添加路径排除**：为避免性能下降和可能的冲突，请从防病毒软件实时监视中排除以下路径：
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent` 和子文件夹
    1. **Scratch 文件夹**：如果 scratch 文件夹不在标准位置，请将其添加到排除项。  [请参阅此处的步骤](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)，确定 scratch 文件夹的位置。
1. **添加二进制排除**：为避免备份和控制台活动减少，请从防病毒软件实时监视中排除以下二进制文件的进程：
    1. `%ProgramFiles%\Microsoft Azure Recovery Services Agent\bin\cbengine.exe`

>[!NOTE]
>尽管排除这些路径足以应对大多数防病毒软件，但某些路径可能仍会继续干扰 MARS 代理操作。 如果出现意外故障，请暂时卸载防病毒软件，并进行监视以查看问题是否消失。 如果这样可以解决问题，请与防病毒软件供应商联系，以获取正确配置其产品方面的帮助。

## <a name="monitor-using-backup-reports"></a>使用备份报告进行监视

Azure 备份提供使用 Azure Monitor 日志和 Azure 工作簿的报告解决方案。 要开始使用，必须为保管库[配置备份报告](configure-reports.md)。 配置后，数据将开始流入工作区，并可以使用备份报告进行查询。

要监控备份数据使用情况和每日流失，请执行以下步骤：

1. 导航到保管库的“概述”窗格并单击“备份报告” 。

1. 在“备份报告”边栏选项卡的“概述”部分下，选择配置的日志分析工作区 。 

1. 将报告筛选器“备份解决方案”设置为“Azure 备份代理”以仅查看 MARS 代理备份 。 

   根据需要设置“订阅名称”、“保管库位置”和“保管库名称”  。
 
    ![设置报告筛选器备份解决方案。](./media/backup-azure-manage-mars/set-report-filter-backup-solution.png)

1. 若要查看按计费实体的使用情况，请导航到“使用情况”选项卡。 

   已计费的受保护实例总数。 并显示存储使用情况数据。 你还可以查看趋势信息。
 
    ![按计费实体查看使用情况。](./media/backup-azure-manage-mars/view-usage-by-billed-entity.png)

1. 要查看受保护服务器中每个卷的备份作业添加的平均备份数据，请导航到“作业”选项卡。 
 
    ![查看平均备份数据。](./media/backup-azure-manage-mars/view-average-backup-data.png)

详细了解[其他报告选项卡](configure-reports.md)并[通过电子邮件](backup-reports-email.md)接收这些报告。

## <a name="next-steps"></a>后续步骤

- 有关支持的方案和限制的详细信息，请参阅 [MARS 代理的支持矩阵](./backup-support-matrix-mars-agent.md)。
- 详细了解[按需备份策略保留行为](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior)。
- 有关更多常见问题，请参阅 [MARS 代理常见问题解答](backup-azure-file-folder-backup-faq.yml)。
