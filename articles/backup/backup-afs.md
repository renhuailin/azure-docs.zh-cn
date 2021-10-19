---
title: 在 Azure 门户中备份 Azure 文件共享
description: 了解如何使用 Azure 门户在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: 8e50cee7e177375fae41ef6fb3a27f871427342c
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714399"
---
# <a name="back-up-azure-file-shares"></a>备份 Azure 文件共享

本文介绍如何从 Azure 门户备份 [Azure 文件共享](../storage/files/storage-files-introduction.md)。

本文将指导如何进行以下操作：

* 创建恢复服务保管库。
* 从恢复服务保管库配置备份
* 从“文件共享”窗格配置备份
* 运行按需备份作业，以创建还原点

## <a name="prerequisites"></a>先决条件

* [了解](azure-file-share-backup-overview.md) Azure 文件共享基于快照的备份解决方案。
* 确保文件共享存在于[受支持的存储帐户类型](azure-file-share-support-matrix.md)之一中。
* 在与托管文件共享的存储帐户相同的区域和订阅中标识或创建[恢复服务保管库](#create-a-recovery-services-vault)。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>从恢复服务保管库配置备份

以下步骤说明如何从“恢复服务保管库”窗格中为多个文件共享配置备份：

1. 在 [Azure 门户](https://portal.azure.com/)中，打开要用于为文件共享配置备份的恢复服务保管库。

1. 在“恢复服务保管库”窗格中，选择顶部菜单中的“+ 备份” 。

   ![恢复服务保管库](./media/backup-afs/recovery-services-vault.png)

    1. 在“备份目标”窗格中，通过从下拉列表中选择“Azure”选项，将“工作负载的运行位置”设置为“Azure”   。

          ![选择 Azure 作为工作负载](./media/backup-afs/backup-goal.png)

    2. 在“要备份的内容”中，从下拉列表中选择“Azure 文件共享” 。

          ![选择 Azure 文件共享](./media/backup-afs/select-azure-file-share.png)

    3. 选择“备份”，将 Azure 文件共享扩展注册到保管库中。

          ![选择“备份”以将 Azure 文件共享与保管库相关联](./media/backup-afs/register-extension.png)

1. 选择“备份”后，将打开“备份”窗格 。 若要选择用于托管要保护的文件共享的存储帐户，请选择“存储帐户”文本框下的“选择”链接文本 。

   ![选择“选择”链接](./media/backup-afs/choose-select-link.png)

1. 此时会在右侧打开“选择存储帐户窗格”，其中列出了一组已发现的受支持的存储帐户。 这些帐户要么与此保管库关联，要么位于与保管库相同的区域中，但尚未关联到任何恢复服务保管库。

1. 从已发现的存储帐户列表中，选择一个帐户，然后选择“确定”。

   ![从已发现的存储帐户中进行选择](./media/backup-afs/select-discovered-storage-account.png)
   
   >[!NOTE]
   > 如果存储帐户所在的区域不同于保管库，则它不会出现在已发现的存储帐户列表中。

1. 下一步是选择要备份的文件共享。 选择“要备份的文件共享”部分中的“添加”按钮 。

   ![选择要备份的文件共享](./media/backup-afs/select-file-shares-to-back-up.png)

1. 右侧会打开“选择文件共享”上下文窗格。 Azure 搜索存储帐户中是否有可进行备份的文件共享。 如果你最近添加了文件共享，但没有在列表中看到它们，请稍等片刻，然后文件共享就会显示。

1. 在“选择文件共享”列表中选择一个或多个需要备份的文件共享。 选择“确定”。

   ![选择文件共享](./media/backup-afs/select-file-shares.png)

1. 若要为文件共享选择备份策略，有三个选项：

   * 选择默认策略。<br>
   此选项允许你启用每日备份，该备份将保留 30 天。 如果保管库中没有现有的备份策略，则会打开备份窗格，其中包含默认策略设置。 如果要选择默认设置，可以直接选择“启用备份”。

   * 创建新策略 <br>

      1. 若要为文件共享创建新的备份策略，请在“备份策略”部分中的下拉列表下选择链接文本。<br>

         ![创建新策略](./media/backup-afs/create-new-policy.png)

      1. 按照[创建新策略](manage-afs-backup.md#create-a-new-policy)部分中的步骤 3-7 进行操作。

      1. 定义策略的所有属性后，单击“确定”。

         ![提供策略名称和保留值](./media/backup-afs/policy-name.png)

   * 选择现有的备份策略之一 <br>

      若要选择现有的备份策略之一来配置保护，请从“备份策略”下拉列表中选择所需的策略。<br>

      ![选择现有策略](./media/backup-afs/choose-existing-policy.png)

1. 选择“启用备份”以开始保护文件共享。

   ![选择启用备份](./media/backup-afs/enable-backup.png)

设置备份策略后，将在计划的时间获取文件共享快照。 还会在所选期间保留恢复点。


## <a name="configure-backup-from-the-file-share-pane"></a>从“文件共享”窗格配置备份

以下步骤说明如何从各自的“文件共享”窗格中为单独的文件共享配置备份：

1. 在 [Azure 门户](https://portal.azure.com/)中，打开托管要备份的文件共享的存储帐户。

1. 进入存储帐户后，选择标记为“文件共享”的磁贴。 也可通过存储帐户的目录导航到“文件共享”。

   ![存储帐户](./media/backup-afs/storage-account.png)

1. 在文件共享列表中，应会看到存储帐户中存在的所有文件共享。 选择要备份的文件共享。

   ![文件共享列表](./media/backup-afs/file-shares-list.png)

1. 在“文件共享”窗格的“操作”部分下，选择“备份” 。 将在右侧加载“配置备份”窗格。

   ![“配置备份”窗格](./media/backup-afs/configure-backup.png)

1. 对于恢复服务保管库选择，请执行以下操作之一：

    * 如果已经有一个保管库，请选择“选择现有”恢复服务保管库单选按钮，然后从“保管库名称”下拉菜单中选择一个现有保管库 。

       ![选择现有保管库](./media/backup-afs/select-existing-vault.png)

    * 如果没有保管库，请选择“新建”恢复服务保管库单选按钮。 指定保管库的名称。 它是在与文件共享相同的区域内创建的。 默认情况下，会在文件共享所在的资源组中创建该保管库。 如果要选择其他资源组，请选择“资源类型”下拉菜单下的“新建”链接，并指定资源组的名称 。 选择“确定”以继续。

       ![创建新的保管库](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >如果存储帐户已经注册了保险库，或者在托管要保护的文件共享的存储帐户中有少量受保护的共享，则将预填充恢复服务保管库名称，并且你不能对它进行编辑。[此处查看详细信息](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-)。

1. 对于“备份策略”选择，请执行以下操作之一：

    * 保留默认策略。 它将计划每日备份，保留时间为 30 天。

    * 从“备份策略”下拉菜单中选择现有的备份策略（如果有）。

       ![选择备份策略](./media/backup-afs/choose-backup-policy.png)

    * 根据你的需求，按每日/每周/每月/每年保留创建新策略。  

         1. 选择“新建策略”链接文本。

         2. 按照[创建新策略](manage-afs-backup.md#create-a-new-policy)部分中的步骤 3-7 进行操作。

         3. 定义策略的所有属性后，单击“确定”。

            ![创建新的备份策略](./media/backup-afs/create-new-backup-policy.png)

1. 选择“启用备份”以开始保护文件共享。

   ![选择“启用备份”](./media/backup-afs/select-enable-backup.png)

1. 你可以在门户通知中，或通过监视用于保护文件共享的保管库下的备份作业来跟踪配置进度。

   ![门户通知](./media/backup-afs/portal-notifications.png)

1. 完成配置备份操作后，在“文件共享”窗格的“操作”部分中选择“备份” 。 右侧将加载列出“保管库必备组件”的上下文窗格。 在该处，可以触发按需备份和还原操作。

   ![保管库必备组件](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>运行按需备份作业

有时候，可能需要在备份策略计划的时间之外生成备份快照或恢复点。 生成按需备份的常见原因是在配置备份策略之后。 可能需要等待数小时或数天才会拍摄快照，具体取决于备份策略中的计划。 若要在该备份策略发挥作用之前对数据进行保护，可以启动按需备份。 在对文件共享进行计划内更改之前，通常需要创建按需备份。

### <a name="from-the-recovery-services-vault"></a>从恢复服务保管库

1. 打开用于备份文件共享的恢复服务保管库。 在“概述”窗格的“受保护的项”部分，选择“备份项”  。

   ![选择备份项](./media/backup-afs/backup-items.png)

1. 选择“备份项”后，“概述”窗格旁边会显示一个新窗格，其中列出了所有“备份管理类型”  。

   ![备份管理类型的列表](./media/backup-afs/backup-management-types.png)

1. 在“备份管理类型”列表中，选择“Azure 存储(Azure 文件)” 。 你将看到使用此保管库备份的所有文件共享和相应存储帐户的列表。

   ![“Azure 存储(Azure 文件)”备份项](./media/backup-afs/azure-files-backup-items.png)

1. 从 Azure 文件共享的列表中，选择所需的文件共享。 此时将显示“备份项”详细信息。 在“备份项”菜单中，选择“立即备份” 。 由于这是按需备份作业，因此没有与恢复点关联的保留策略。

   ![选择立即备份](./media/backup-afs/backup-now.png)

1. 此时将打开“立即备份”窗格。 指定要保留恢复点的最后一天。 按需备份的最大保留期限为 10 年。

   ![选择保留日期](./media/backup-afs/retention-date.png)

1. 选择“确定”以确认正在运行的按需备份作业。

1. 监视门户通知以跟踪备份作业运行完成。 可以在保管库仪表板中监视作业进度。 选择“备份作业” > “正在进行” 。

### <a name="from-the-file-share-pane"></a>从“文件共享”窗格

1. 打开要进行按需备份的文件共享的“概述”窗格。

1. 选择“操作”部分下的“备份” 。 右侧将加载列出“保管库必备组件”的上下文窗格。 选择“立即备份”以进行按需备份。

   ![选择立即备份](./media/backup-afs/select-backup-now.png)

1. 此时将打开“立即备份”窗格。 指定恢复点的保留期。 按需备份的最大保留期限为 10 年。

   ![保留备份日期](./media/backup-afs/retain-backup-date.png)

1. 选择“确定”  以确认。

>[!NOTE]
>为相应帐户中的任何文件共享配置保护时，Azure 备份会锁定存储帐户。 这样可以防止意外删除包含备份文件共享的存储帐户。

## <a name="best-practices"></a>最佳做法

* 请勿删除由 Azure 备份创建的快照。 删除快照可能导致恢复点丢失和/或还原失败。

* 请勿删除 Azure 备份对存储帐户所做的锁定。 删除此锁定后，存储帐户将容易被意外删除，而一旦存储帐户被删除，你将失去你的快照或备份。

## <a name="next-steps"></a>后续步骤

了解如何：

* [还原 Azure 文件共享](restore-afs.md)
* [管理 Azure 文件共享备份](manage-afs-backup.md)
