---
title: 管理 Azure 文件共享备份
description: 本文介绍了管理和监视由 Azure 备份所备份的 Azure 文件共享时需要执行的常见任务。
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: e955ed1cf01c055ea72218076799d7da31d096b7
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714262"
---
# <a name="manage-azure-file-share-backups"></a>管理 Azure 文件共享备份

本文介绍了管理和监视由 [Azure 备份](./backup-overview.md)所备份的 Azure 文件共享时需要执行的常见任务。 你将了解如何在恢复服务保管库中执行管理任务。

## <a name="monitor-jobs"></a>监视作业

触发备份或还原操作时，备份服务会创建一个用于跟踪的作业。 可以在“备份作业”页中监视所有作业的进度。

若要打开“备份作业”页，请执行以下操作：

1. 打开用于为文件共享配置备份的恢复服务保管库。 在“概述”窗格的“监视”部分选择“备份作业”。

   ![“监视”部分的“备份作业”](./media/manage-afs-backup/backup-jobs.png)

1. 选择“确定”后，“备份作业”窗格会列出所有作业的状态。 选择与要监视的文件共享对应的工作负荷名称。

   ![工作负荷名称](./media/manage-afs-backup/workload-name.png)

## <a name="monitor-using-azure-backup-reports"></a>使用 Azure 备份报告进行监视

Azure 备份提供使用 [Azure Monitor 日志](../azure-monitor/logs/log-analytics-tutorial.md)和 [Azure 工作簿](../azure-monitor/visualize/workbooks-overview.md)的报告解决方案。 这些资源可帮助你深入了解备份。 可以利用这些报告来获取有关 Azure 文件存储备份项、项级别作业的见解和活动策略的详细信息。 使用备份报告中提供的“通过电子邮件发送报告”功能，可以创建自动化任务以通过电子邮件接收定期报告。[了解](/azure/backup/configure-reports#get-started)如何配置和查看 Azure 备份报告。

## <a name="create-a-new-policy"></a>创建新策略

你可以创建新的策略，以便通过恢复服务保管库的“备份策略”部分备份 Azure 文件共享。 为文件共享配置备份时创建的所有策略都会显示，其“策略类型”为“Azure 文件共享”。 

若要创建新的备份策略，请执行以下步骤：

1. 在恢复服务保管库的“备份策略”窗格中，选择“+ 添加”。

   :::image type="content" source="./media/manage-afs-backup/new-backup-policy.png" alt-text="显示用于开始创建新备份策略的选项的屏幕截图。":::

1. 在“添加”窗格中，选择“Azure 文件共享”作为“策略类型”。

   :::image type="content" source="./media/manage-afs-backup/define-policy-type.png" alt-text="显示选择“Azure 文件共享”作为策略类型的屏幕截图。":::

1. 在“Azure 文件共享”的“备份策略”窗格打开时，指定策略名称。 

1. 在“备份计划”中，选择适当的备份频率：“每日”或“每小时”。  

   :::image type="content" source="./media/manage-afs-backup/backup-frequency-types.png" alt-text="显示备份的频率类型的屏幕截图。":::

   - 每日：每日触发一次备份。 对于每日频率，请为以下选项选择适当的值：

     - 时间：需要触发备份作业时的时间戳。
     - 时区：备份作业的对应时区。

   - 每小时：每天触发多个备份。 对于每小时频率，请为以下选项选择适当的值：
   
     - 计划：连续备份之间的时间间隔（以小时为单位）。
     - 开始时间：需要触发当天第一个备份作业的时间。
     - 持续时间：表示备份窗口（以小时为单位），即根据所选计划需要触发备份作业的时间跨度。
     - 时区：备份作业的对应时区。
     
     例如，你的 RPO（恢复点目标）要求为 4 小时，你的工作时间为上午 9 点至晚上 9 点。 为了满足这些要求，备份计划的配置将如下所示：
    
     - 计划：每 4 小时
     - 开始时间：上午 9 点 
     - 持续时间：12 小时 
     
     :::image type="content" source="./media/manage-afs-backup/hourly-backup-frequency-values-scenario.png" alt-text="显示每小时备份频率值示例的屏幕截图。":::

     根据你的选择，备份作业详细信息（触发备份作业时的时间戳）将显示在备份策略边栏选项卡上。

1. 在“保留期”中，为备份指定适当的保留值 - 标记为每天、每周、每月或每年。

1. 定义策略的所有属性后，单击“创建”。
  
### <a name="view-policy"></a>查看策略

若要查看现有备份策略，请执行以下操作：

1. 打开用于为文件共享配置备份的恢复服务保管库。 在恢复服务保管库菜单的“管理”部分选择“备份策略”。  此时会显示保管库中配置的所有备份策略。

   :::image type="content" source="./media/manage-afs-backup/all-backup-policies.png" alt-text="显示所有备份策略的屏幕截图。":::

1. 若要查看特定于 Azure 文件共享的策略，请从右上角的下拉列表中选择“Azure 文件共享”。

   :::image type="content" source="./media/manage-afs-backup/azure-file-share.png" alt-text="显示选择 Azure 文件共享的过程的屏幕截图。":::

## <a name="modify-policy"></a>修改策略

你可以修改备份策略以更改备份频率或保持期。

若要修改策略，请执行以下操作：

1. 打开用于为文件共享配置备份的恢复服务保管库。 在恢复服务保管库菜单的“管理”部分选择“备份策略”。  此时会显示保管库中配置的所有备份策略。

   ![保管库中的所有备份策略](./media/manage-afs-backup/all-backup-policies-modify.png)

1. 若要查看特定于 Azure 文件共享的策略，请从右上角的下拉列表中选择“Azure 文件共享”。 选择要修改的备份策略。

   ![要修改的 Azure 文件共享](./media/manage-afs-backup/azure-file-share-modify.png)

1. 此时会打开“计划”窗格。 根据需要编辑“备份计划”和“保持期”，然后选择“保存”。 窗格中会显示“更新正在进行”消息。 在策略更改成功更新后，你会看到“已成功更新备份策略”消息。

   ![保存修改的策略](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>停止对文件共享的保护

可以通过两种方法来停止保护 Azure 文件共享：

* 停止所有将来的备份作业，并删除所有恢复点。
* 停止所有将来的备份作业，但保留恢复点。

在存储中保留恢复点可能会产生费用，因为 Azure 备份创建的基础快照将会保留。 保留恢复点的好处是，以后可以还原文件共享。 如需了解保留恢复点的成本，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/backup/)。 如果你决定删除所有恢复点，则无法还原文件共享。

若要停止对 Azure 文件共享的保护，请执行以下操作：

1. 打开包含文件共享恢复点的恢复服务保管库。 在“受保护的项”部分选择“备份项”。 此时会显示备份项类型的列表。

   ![备份项](./media/manage-afs-backup/backup-items.png)

1. 在“备份管理类型”列表中，选择“Azure 存储(Azure 文件)”。 此时会显示“备份项(Azure 存储(Azure 文件存储))”列表。

   ![选择“Azure 存储(Azure 文件存储)”](./media/manage-afs-backup/azure-storage-azure-files.png)

1. 在“备份项(Azure 存储(Azure 文件存储))”列表中，选择要停止保护的备份项。

1. 选择“停止备份”选项。

   ![选择“停止备份”](./media/manage-afs-backup/stop-backup.png)

1. 在“停止备份”窗格中，选择“保留备份数据”或“删除备份数据”。   然后选择“停止备份”。

    ![选择“保留备份数据”或“删除备份数据”](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>恢复对文件共享的保护

如果在停止对文件共享的保护时选择了“保留备份数据”选项，则可恢复对文件共享的保护。 如果选择了“删除备份数据”选项，则不能恢复对文件共享的保护。

若要恢复对 Azure 文件共享的保护，请执行以下操作：

1. 打开包含文件共享恢复点的恢复服务保管库。 在“受保护的项”部分选择“备份项”。 此时会显示备份项类型的列表。

   ![要恢复的备份项](./media/manage-afs-backup/backup-items-resume.png)

1. 在“备份管理类型”列表中，选择“Azure 存储(Azure 文件)”。 此时会显示“备份项(Azure 存储(Azure 文件存储))”列表。

   ![“Azure 存储(Azure 文件存储)”列表](./media/manage-afs-backup/azure-storage-azure-files.png)

1. 在“备份项(Azure 存储(Azure 文件存储))”列表中，选择要恢复保护的备份项。

1. 选择“恢复备份”选项。

   ![选择“恢复备份”](./media/manage-afs-backup/resume-backup.png)

1. 此时会打开“备份策略”窗格。 选择一个用于恢复备份的策略。

1. 选择备份策略后，选择“保存”。 你会在门户中看到“更新正在进行”消息。 成功恢复备份后，你会看到“已成功为受保护的 Azure 文件共享更新备份策略”消息。

   ![已成功更新备份策略](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>删除备份数据

可以在执行“停止备份”作业期间删除文件共享的备份，也可以在停止保护之后的任何时间这样做。 等待几天甚至几周再删除恢复点可能较好。 删除备份数据时，无法选择要删除的具体恢复点。 如果决定删除备份数据，请删除与文件共享关联的所有恢复点。

以下过程假定已停止对文件共享的保护。

若要删除 Azure 文件共享的备份数据，请执行以下操作：

1. 停止备份作业后，“备份项”仪表板中会出现“恢复备份”和“删除备份数据”选项。   选择“删除备份数据”选项。

   ![删除备份数据](./media/manage-afs-backup/delete-backup-data.png)

1. 此时会打开“删除备份数据”窗格。 输入文件共享的名称以确认删除。 （可选）在“原因”或“注释”框中提供更多信息。  确定删除备份数据后，选择“删除”。

   ![确认删除数据](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>注销存储帐户

若要使用一个不同的恢复服务保管库来保护特定存储帐户中的文件共享，请先在该存储帐户中[停止对所有文件共享的保护](#stop-protection-on-a-file-share)。 然后，从用于保护的当前恢复服务保管库中注销该帐户。

以下过程假定已停止保护要注销的存储帐户中的所有文件共享。

若要注销存储帐户，请执行以下操作：

1. 打开在其中注册了存储帐户的恢复服务保管库。
1. 在“概述”窗格的“管理”部分选择“备份基础结构”选项。

   ![选择“备份基础结构”。](./media/manage-afs-backup/backup-infrastructure.png)

1. 此时会打开“备份基础结构”窗格。 在“Azure 存储帐户”部分选择“存储帐户”。

   ![选择“存储帐户”](./media/manage-afs-backup/storage-accounts.png)

1. 选择“存储帐户”后，会显示在保管库中注册的存储帐户的列表。
1. 右键单击要注销的存储帐户，然后选择“注销”。

   ![选择“注销”](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[对 Azure 文件共享备份进行故障排除](./troubleshoot-azure-files.md)。
