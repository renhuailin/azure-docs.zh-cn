---
title: 监视 Azure 备份保护的工作负荷
description: 本文介绍使用 Azure 门户对 Azure 备份工作负荷执行的监视和通知功能。
ms.topic: conceptual
ms.date: 08/06/2021
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 4b20448896de05e888de33b2a680623b662b5e5a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178440"
---
# <a name="monitoring-azure-backup-workloads"></a>监视 Azure 备份工作负荷

Azure 备份根据备份要求和基础结构拓扑（本地或 Azure）提供多个备份解决方案。 任何备份用户或管理员都应看到所有解决方案中发生的情况，并会在出现重大情况时收到通知。 本文详细介绍了 Azure 备份服务提供的监视和通知功能。

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>恢复服务保管库中的备份项

可通过恢复服务保管库监视所有备份项。 导航到保管库中的“备份项”部分后会打开一个视图，其中提供与保管库关联的每种工作负载的备份项数量。 单击任意行会打开一个详细视图，其中列出了给定工作负载类型的所有备份项，以及有关每个项的上次备份状态、可用的最新还原点等信息。

![屏幕截图：查看 RS 保管库备份项](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> 对于使用 DPM 备份到 Azure 的项，该列表将显示使用 DPM 服务器保护的所有数据源（包括磁盘和联机）。 如果对保留了备份数据的数据源停止保护，则该数据源仍会在门户中列出。 可访问数据源的详细信息，查看恢复点是否存在于磁盘、联机或同时存在于这两者中。 此外，对于联机保护已停止但数据仍保留的数据源，在数据完全删除之前，将继续对联机恢复点进行计费。
>
> DPM 版本必须是 DPM 1807 (5.1.378.0) 或 DPM 2019（10.19.58.0 或更高版本），才能在恢复服务保管库门户中显示备份项。

## <a name="backup-jobs-in-recovery-services-vault"></a>恢复服务保管库中的 Azure 备份作业

Azure 备份针对 Azure 备份保护的工作负荷提供内置的监视和警报功能。 在恢复服务保管库设置中，“监视”部分提供了内置的作业和警报。

![屏幕截图：RS 保管库内置监视](media/backup-azure-monitoring-laworkspace/rs-vault-inbuilt-monitoring-menu.png)

执行配置备份、备份、还原、删除备份等操作时，会生成作业。

此处会显示以下 Azure 备份解决方案中的作业：

- Azure VM 备份
- Azure 文件备份
- Azure 工作负荷备份，例如 SQL 和 SAP HANA 备份
- Microsoft Azure 恢复服务 (MARS) 代理

不会显示 System Center Data Protection Manager (SC-DPM) 和 Microsoft Azure 备份服务器 (MABS) 中的作业。

> [!NOTE]
> Azure VM 中的 Azure 工作负荷（例如 SQL 和 SAP HANA 备份）包含大量的备份作业。 例如，日志备份可能每隔 15 分钟运行一次。 因此，对于此类数据库工作负荷，只会显示用户触发的操作。 不会显示计划的备份操作。

## <a name="backup-alerts-in-recovery-services-vault"></a>恢复服务保管库中的备份警报

警报主要用于通知用户，让他们采取相关的措施。 “备份警报”部分显示 Azure 备份服务生成的警报。 这些警报由服务定义，用户无法以自定义方式创建任何警报。

### <a name="alert-scenarios"></a>警报方案

以下方案由服务定义为可发出警报的方案。

- 备份/还原失败
- 备份成功，并显示针对 Microsoft Azure 恢复服务 (MARS) 代理的警告
- 停止保护并保留数据/停止保护并删除数据

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>此处会显示以下 Azure 备份解决方案中的警报

- Azure VM 备份
- Azure 文件备份
- Azure 工作负荷备份，例如 SQL 备份、SAP HANA 备份
- Microsoft Azure 恢复服务 (MARS) 代理

> [!NOTE]
> 此处不会显示 System Center Data Protection Manager (SC-DPM) 和 Microsoft Azure 备份服务器 (MABS) 中的警报。

### <a name="consolidated-alerts"></a>合并的警报

对于 Azure 工作负荷备份解决方案（例如 SQL 和 SAP HANA），系统可以非常频繁地生成日志备份（根据策略，最高可达每 15 分钟 1 次）。 因此，也可能会出现日志备份失败也很频繁（高达每 15 分钟一次）的情况。 在这种情况下，如果每次失败都引发一次警报，最终用户将会不堪其扰。 因此，系统会在第一次失败时发送警报，以后由于同一根本原因而失败时则不会生成警报。 将在第一个警报中更新失败计数。 但如果该警报被用户停用，则下一次失败会触发另一警报，系统会将其视为该情况的第一个警报。 这是 Azure 备份针对 SQL 和 SAP HANA 备份执行警报合并的方式。

### <a name="exceptions-when-an-alert-is-not-raised"></a>未引发警报时生成异常

在一些例外情况下，失败时不会引发警报。 它们具有以下特点：

- 用户显式取消了正在运行的作业
- 作业失败，因为另一个备份作业正在进行（在此情况下，无需采取任何措施，因为只需等待前一个作业完成即可）
- VM 备份作业失败，因为备份的 Azure VM 不再存在
- [合并的警报](#consolidated-alerts)

之所以设计上述异常，是因为我们知道，这些操作的结果（主要是用户触发的操作）会立即在门户/PS/CLI 客户端中显示。 因此，用户会立即了解相关情况，不需要通知。

### <a name="alert-types"></a>警报类型

根据警报严重性，可以定义三种类型的警报：

- **严重**：原则上，发生任何备份或恢复失败（计划的或用户触发的）都会导致生成警报，并显示为严重警报以及破坏性操作（例如删除备份）。
- **警告**：如果备份操作成功但出现了几条警告，则会将这些警报列为“警告”警报。 “警告”警报目前仅适用于 Azure 备份代理备份。
- **信息性**：目前，Azure 备份服务不会生成任何信息性警报。

## <a name="notification-for-backup-alerts"></a>备份警报的通知

> [!NOTE]
> 只能通过 Azure 门户配置通知。 不支持使用 PS/CLI/REST API/Azure 资源管理器模板。

一旦引发警报，用户就会收到通知。 Azure 备份通过电子邮件提供内置通知机制。 可以指定在生成警报时接收通知的个人电子邮件地址或通讯组列表。 还可以选择是要接收每个警报的通知，还是将这些警报分组成按小时摘要，然后接收通知。

![屏幕截图：RS 保管库内置电子邮件通知](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

配置通知后，你会收到一封欢迎电子邮件或简介电子邮件。 由此可以确认，在引发警报时，Azure 备份可向这些地址发送电子邮件。<br>

如果将频率设置为“每小时摘要”，然后引发了警报，但在一小时内解决了该警报，那么，后续的每小时摘要中不会包括该警报。

> [!NOTE]
>
> - 如果执行了破坏性操作（例如“停止保护并删除数据”），那么，即使未针对恢复服务保管库配置通知，也会引发警报，并向订阅所有者、管理员和共同管理员发送电子邮件。
> - 若要针对成功的作业配置通知，请使用 [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)。

## <a name="inactivating-alerts"></a>停用警报

若要停用/解决某个活动警报，可以选择与要停用的警报相对应的列表项。 这将打开一个屏幕，其中会显示有关警报的详细信息，顶部有一个“停用”按钮。 选择此按钮会将警报的状态更改为“非活动”。 还可以通过以下方式停用警报：右键单击与警报对应的列表项并选择“停用”。

![屏幕截图：“备份中心”警报停用](media/backup-azure-monitoring-laworkspace/vault-alert-inactivate.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Azure 备份的 Azure Monitor 警报（预览）

Azure 备份还通过 Azure Monitor 提供警报，使用户能够在包括备份在内的不同 Azure 服务间获得一致的警报管理体验。 使用 Azure Monitor 警报可将警报路由到 Azure Monitor 支持的任何通知通道，例如电子邮件、ITSM、Webhook、逻辑应用等。

Azure 备份目前已提供两种主要类型的内置警报：

* 安全警报：在删除了备份数据或禁用了保管库的软删除功能等情况下，安全警报（严重性为“严重性 0”）将会触发并显示在 Azure 门户中，或者可以通过其他客户端（PowerShell、CLI 和 REST API）使用这些警报。 默认会生成安全警报，且无法将其关闭。 但是，可以控制要对其触发通知（例如电子邮件通知）的场景。 有关如何配置通知的详细信息，请参阅[操作规则](../azure-monitor/alerts/alerts-action-rules.md)。
* 作业失败警报：对于备份失败和还原失败等情况，Azure 备份通过 Azure Monitor 提供内置警报（严重性为“严重性 1”）。 不同于安全警报，可以选择关闭针对作业失败场景的 Azure Monitor 警报。 例如，若已通过 Log Analytics 为作业失败配置自定义警报规则，且不需要为每个作业失败触发内置警报。 默认情况下，作业失败警报关闭。 有关更多详细信息，请参阅[有关针对这些场景打开警报的部分](#turning-on-azure-monitor-alerts-for-job-failure-scenarios)。
 
下表汇总了当前通过 Azure Monitor 提供的各种备份警报（预览版）和支持的工作负载/保管库类型：

| 警报类别 | 警报名称 | 支持的工作负载类型/保管库类型 | **说明** | 
| ------------------ | -------------  | ------------------------------------------ | -------- |
| 安全性 | 删除备份数据 | <li> Azure 虚拟机 <br><br> <li> Azure VM 中的 SQL（非 AG 方案） <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 备份代理 <br><br> <li> DPM <br><br> <li> Azure 备份服务器 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure Blob <br><br> <li> Azure 托管磁盘  | 用户停止备份并删除备份数据时，将触发此警报（注：若已为保管库禁用软删除功能，则不会收到“删除备份数据”警报） |
| 安全性 | 即将清除 | <li> Azure 虚拟机 <br><br> <li> Azure VM 中的 SQL（非 AG 方案） <br><br> <li> Azure VM 中的 SAP HANA | 对于支持软删除的所有工作负载，当某个项的备份数据在 2 天后就会被 Azure 备份服务永久清除时，将触发此警报 | 
| 安全性 | 清除完成 | <li> Azure 虚拟机 <br><br> <li> Azure VM 中的 SQL（非 AG 方案） <br><br> <li> Azure VM 中的 SAP HANA | 删除备份数据 |
| 安全性 | 为保管库禁用了软删除 | 恢复服务保管库 | 当 Azure 备份服务永久删除了某个项的软删除备份数据时，将触发此警报 | 
| 作业 | 备份失败 | <li> Azure 虚拟机 <br><br> <li> Azure VM 中的 SQL（非 AG 方案） <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 备份代理 <br><br> <li> Azure 文件 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure 托管磁盘 | 发生备份作业失败时，将触发此警报。 针对备份失败的警报默认已关闭。 有关更多详细信息，请参阅[有关针对此场景打开警报的部分](#turning-on-azure-monitor-alerts-for-job-failure-scenarios)。 | 
| 作业 | 还原失败 | <li> Azure 虚拟机 <br><br> <li> Azure VM 中的 SQL（非 AG 方案） <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 备份代理 <br><br> <li> Azure 文件 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure Blob <br><br> <li> Azure 托管磁盘| 发生还原作业失败时，将触发此警报。 针对还原失败的警报默认已关闭。 有关更多详细信息，请参阅[有关针对此场景打开警报的部分](#turning-on-azure-monitor-alerts-for-job-failure-scenarios)。 | 

### <a name="turning-on-azure-monitor-alerts-for-job-failure-scenarios"></a>打开针对作业失败场景的 Azure Monitor 警报

若要选择打开针对备份失败和还原失败场景的 Azure Monitor 警报，请执行以下步骤：

1. 导航至 Azure 门户，然后搜索“预览功能”。

    ![屏幕截图：查看门户预览功能](media/backup-azure-monitoring-laworkspace/portal-preview-features.png)

2. 你可以查看所有能够打开的预览功能的列表。

    * 如果要接收备份到“恢复服务”保管库的工作负载的作业失败警报，请选择与 Microsoft.RecoveryServices 提供程序（第 3 列）对应的名为 EnableAzureBackupJobFailureAlertsToAzureMonitor 的标志。
    * 如果要接收备份到“备份”保管库的工作负载的作业失败警报，请选择与 Microsoft.DataProtection 提供程序（第 3 列）对应的名为 EnableAzureBackupJobFailureAlertsToAzureMonitor 的标志。

    ![屏幕截图：警报预览注册](media/backup-azure-monitoring-laworkspace/alert-preview-feature-flags.png)

3. 使用“注册”为订阅启用此功能。
    > [!NOTE]
    > 注册可能需要长达 24 小时才能生效。 若要为多个订阅启用此功能，请在屏幕顶部选择相关订阅，并重复上述流程。 如果初始注册后已在订阅中创建新资源，则还建议重新注册预览标志，以继续接收警报。

### <a name="viewing-fired-alerts-in-the-azure-portal"></a>在 Azure 门户中查看已触发的警报 

针对某个保管库触发警报后，在 Azure 门户导航至“备份中心”即可查看该警报。 在“概述”选项卡中可以看到按严重性划分的活动警报汇总。 其中显示有两种类型的警报：

* 数据源警报：与正在备份的特定数据源关联的警报（例如，VM 备份或还原失败、删除数据库备份数据等），见“数据源警报”部分。 
* 全局警报：未与特定数据源关联的警报（例如，禁用保管库软删除功能），见“全局警报”部分。 

以上每种警报类型又进一步划分为“安全”和“已配置”警报。  目前，安全警报包括删除备份数据或禁用保管库软删除（针对上节详述的适用工作负载）的场景。 已配置警报包括备份失败和还原失败的场景，因为只有在预览门户注册此功能后才会触发这些警报。

![屏幕截图：查看“备份中心”警报](media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alerts.png)

单击任意数字（或“警报”菜单项）会打开通过相关已应用筛选器触发的所有活动警报的列表。 可以根据各种属性（例如订阅、资源组、保管库、严重性、状态等）进行筛选。 可以单击任一警报获取有关该警报的更多详细信息，例如受影响的数据源、警报说明和建议的操作，等等。

![屏幕截图：查看警报详细信息](media/backup-azure-monitoring-laworkspace/backup-center-alert-details.png) 

可以单击“更改警报状态”将警报的状态更改为“已确认”或“已关闭”  。

![屏幕截图：更改警报状态](media/backup-azure-monitoring-laworkspace/backup-center-change-alert-state.png) 

> [!NOTE]
> - “备份中心”当前仅显示基于 Azure 的工作负载的警报。 若要查看针对本地资源的警报，请导航至恢复服务保管库，然后单击“警报”菜单项。
> - “备份中心”仅显示 Azure Monitor 警报。 由较旧警报解决方案（通过恢复服务保管库中的[备份警报](#backup-alerts-in-recovery-services-vault)选项卡访问）引发的警报没有显示在“备份中心”。
有关 Azure Monitor 警报的详细信息，请参阅 [Azure 中的警报概述](../azure-monitor/alerts/alerts-overview.md)。

### <a name="configuring-notifications-for-alerts"></a>配置警报的通知

若要配置 Azure Monitor 警报通知，必须创建操作规则。 以下步骤说明如何创建一个操作规则，以将电子邮件通知发送到给定的电子邮件地址。 可以按照类似的说明将这些警报路由到其他通知通道，例如 ITSM、Webhook、逻辑应用等。

1. 在 Azure 门户导航至“备份中心”。 单击“警报”菜单项并选择“管理操作” 。

    ![屏幕截图：管理“备份中心”操作](media/backup-azure-monitoring-laworkspace/backup-center-manage-actions.png) 

2. 导航到“操作规则(预览版)”并单击“新建操作规则” 。

    ![屏幕截图：新创建操作规则](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-rule.png) 

3. 选择该操作规则的应用范围。 可以针对订阅内的所有资源应用该操作规则。 还可以选择性地对警报应用筛选器，例如，仅为特定严重性的警报生成通知。

    ![屏幕截图：设置操作规则范围](media/backup-azure-monitoring-laworkspace/azure-monitor-action-rule-scope.png) 

4. 创建操作组。 操作组是警报通知应发送到的目标，例如某个电子邮件地址。
 
    ![屏幕截图：新创建操作组](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-group.png) 

5. 在“基本信息”选项卡上，选择操作组的名称，以及要在其下创建该操作组的订阅和资源组。

    ![屏幕截图：操作组基本属性](media/backup-azure-monitoring-laworkspace/azure-monitor-action-groups-basic.png) 

6. 在“通知”选项卡中选择“电子邮件/短信/推送/语音”并输入收件人电子邮件 ID。 

    ![屏幕截图：设置通知属性](media/backup-azure-monitoring-laworkspace/azure-monitor-email.png) 

7. 依次单击“查看+创建”、“创建”以部署该操作组 。

8. 最后，保存操作规则。

[详细了解 Azure Monitor 中的操作规则](../azure-monitor/alerts/alerts-action-rules.md)


## <a name="next-steps"></a>后续步骤

[使用 Azure Monitor 监视 Azure 备份工作负荷](backup-azure-monitoring-use-azuremonitor.md)