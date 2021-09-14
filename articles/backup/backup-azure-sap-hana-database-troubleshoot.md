---
title: 排查 SAP HANA 数据库备份错误
description: 介绍如何排查在使用 Azure 备份对 SAP HANA 数据库进行备份时可能发生的常见错误。
ms.topic: troubleshooting
ms.date: 05/31/2021
ms.openlocfilehash: 1af52116a08344d36a9364ac9e3b4f468e0abb83
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451954"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>排查 Azure 上的 SAP HANA 数据库备份问题

本文针对在 Azure 虚拟机上备份 SAP HANA 数据库时出现的问题提供故障排除信息。 有关目前支持的 SAP HANA 备份方案的详细信息，请参阅[方案支持](sap-hana-backup-support-matrix.md#scenario-support)。

## <a name="prerequisites-and-permissions"></a>先决条件和权限

在继续备份之前，请参阅[先决条件](tutorial-backup-sap-hana-db.md#prerequisites)和[注册前脚本的功能](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)部分。

## <a name="common-user-errors"></a>常见用户错误

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **错误消息**      | `Azure Backup does not have required role  privileges to carry out Backup and Restore operations`    |
| ---------------------- | ------------------------------------------------------------ |
| 可能的原因    | 如果备份用户 (AZUREWLBACKUPHANAUSER) 未分配 SAP_INTERNAL_HANA_SUPPORT 角色或角色可能已被覆盖，则所有操作都将失败并出现此错误。                          |
| **建议的操作** | 下载[预注册脚本](https://aka.ms/scriptforpermsonhana)并在 SAP HANA 实例上运行该脚本，或手动将 SAP_INTERNAL_HANA_SUPPORT 角色分配给备份用户 (AZUREWLBACKUPHANAUSER)。<br><br>**注意**<br><br>如果使用 HANA 2.0 SPS04 Rev 46 及更高版本，则不会发生此错误，因为在这些 HANA 版本中已弃用 SAP_INTERNAL_HANA_SUPPORT 角色。 |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| **错误消息**      | `Failed to connect to HANA system`                        |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因    | <ul><li>与 HANA 实例的连接失败</li><li>系统 DB 脱机</li><li>租户 DB 脱机</li><li>备份用户 (AZUREWLBACKUPHANAUSER) 没有足够的权限/特权。</li></ul> |
| **建议的操作** | 请检查系统是否已启动并正在运行。 如果数据库已启动并正在运行，请确保通过下载[预注册脚本](https://aka.ms/scriptforpermsonhana)并在 SAP HANA 实例上运行该脚本来设置所需的权限。 |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| **错误消息**      | `The specified SAP HANA instance is either invalid or can't be found`  |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因    | <ul><li>指定的 SAP HANA 实例无效或无法找到。</li><li>无法备份单个 Azure VM 上的多个 SAP HANA 实例。</li></ul> |
| **建议的操作** | <ul><li>确保只有一个 HANA 实例在 Azure VM 上运行。</li><li>使用正确的 SAP HANA 实例从“发现 DB”窗格运行脚本（也可以在[此处](https://aka.ms/scriptforpermsonhana)找到此脚本）来解决此问题。</li></ul> |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| **错误消息**      | `Backup log chain is broken`                                    |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因    | 有多种原因可以触发 HANA LSN 日志链中断，包括：<ul><li>Azure 存储调用无法提交备份。</li><li>租户 DB 脱机。</li><li>扩展升级已终止正在进行中的备份作业。</li><li>备份期间无法连接到 Azure 存储。</li><li>SAP HANA 在备份过程中回滚了事务。</li><li>备份已完成，但尚未在 HANA 系统中成功更新目录。</li><li>从 Azure 备份的角度来看，备份失败，但从 HANA 的角度来看备份成功 - 日志备份/目录目标可能已从 backint 更新到文件系统，或者 backint 可执行文件可能已更改。</li></ul> |
| **建议的操作** | 为解决此问题，Azure 备份会触发自动修复完整备份。 虽然此自动修复备份正在进行中，但 HANA 触发的所有日志备份都将失败，并出现 OperationCancelledBecauseConflictingAutohealOperationRunningUserError。 自动修复完整备份完成后，日志和所有其他备份将开始按预期正常工作。<br>如果在 24 小时内未看到自动修复完整备份触发或任何成功的备份（完整/差异/增量），请联系 Microsoft 支持部门。</br> |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| **错误消息**      | `SDC to MDC upgrade detected.`                                   |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因    | 将 SDC 系统升级到 MDC 时，备份失败并出现此错误。 |
| **建议的操作** | 若要排查和解决问题，请参阅 [SDC 到 MDC 的升级](#sdc-to-mdc-upgrade-with-a-change-in-sid)。 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| **错误消息**      | `Backups will fail with this error when the Backint Configuration is incorrectly updated.`                       |
| ------------------ | ------------------------------------------------------------ |
| 可能的原因    | 配置保护流期间 Azure 备份更新的 backint 配置由客户更改/更新。 |
| **建议的操作** | 检查是否设置了以下 (backint) 参数：<br><ul><li>[catalog_backup_using_backint:true]</li><li>[enable_accumulated_catalog_backup:false]</li><li>[parallel_data_backup_backint_channels:1]</li><li>[log_backup_timeout_s:900)]</li><li>[backint_response_timeout:7200]</li></ul>如果在主机级别存在基于 backint 的参数，请将其删除。 但是，如果参数不存在于主机级别，而在数据库级别进行了手动修改，请确保在上面设置了数据库级别值。 或者，从 Azure 门户运行[停止保护并保留备份数据](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)，然后选择“恢复备份”。 |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|**错误消息**  | `The source and target systems for restore are incompatible.`  |
|---------|---------|
|可能的原因   | 当源和目标 HANA 数据库以及系统不兼容时，还原流失败并出现此错误。 |
|建议的操作   |   确保还原方案不属于以下可能的不兼容还原：<br> **案例 1：** 在还原过程中无法重命名 SYSTEMDB。<br>**案例 2：** 源 - SDC 和目标 - MDC：源数据库无法还原为目标 SYSTEMDB 或租户 DB。 <br> **案例 3：** 源 - MDC 和目标 - SDC：源数据库（SYSTEMDB 或租户 DB）无法还原到目标。<br>若要了解详细信息，请参阅 [SAP 支持启动板](https://launchpad.support.sap.com)中的说明 1642148。 |

### <a name="usererrorhanapodoesnotexist"></a>UserErrorHANAPODoesNotExist

**错误消息** | `Database configured for backup does not exist.`
--------- | --------------------------------
可能的原因 | 如果删除了已为备份配置的数据库，则此数据库上的所有计划备份和临时备份都将失败。
**建议的操作** | 验证数据库是否已删除。 重新创建数据库或[停止保护](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)（保留或不保留数据）数据库。

### <a name="usererrorinsufficientprivilegeofdatabaseuser"></a>UserErrorInsufficientPrivilegeOfDatabaseUser

**错误消息** |    `Azure Backup does not have enough privileges to carry out Backup and Restore operations.`
---------- | ---------
可能的原因 | 没有给预注册脚本创建的备份用户 (AZUREWLBACKUPHANAUSER) 分配以下一个或多个角色：<ul><li>对于 MDC，分配 DATABASE ADMIN 和 BACKUP ADMIN（适用于 HANA 2.0 SPS05 及更高版本）以在还原期间创建新数据库。</li><li>对于 SDC，分配 BACKUP ADMIN 以在还原期间创建新数据库。</li><li>分配 CATALOG READ 以读取备份目录。</li><li>分配 SAP_INTERNAL_HANA_SUPPORT 以访问一些专用表。 仅对于 HANA 2.0 SPS04 Rev 46 之前的 SDC 和 MDC 版本是必需的。 对于 HANA 2.0 SPS04 Rev 46 和更高版本不是必需的，因为我们现在会通过 HANA 团队提供的修补程序从公共表中获取所需的信息。</li></ul>
**建议的操作** | 若要解决此问题，请将所需角色和权限手动添加到备份用户 (AZUREWLBACKUPHANAUSER)，或下载预注册脚本并在 [SAP HANA 实例](https://aka.ms/scriptforpermsonhana)上运行该脚本。

### <a name="usererrordatabaseuserpasswordexpired"></a>UserErrorDatabaseUserPasswordExpired

**错误消息** | `Database/Backup user's password expired.`
----------- | -----------
可能的原因 | 预注册脚本创建的数据库/备份用户不会设置密码的到期时间。 但是，如果更改了该设置，则可能会出现此错误。
**建议的操作** | 下载[预注册脚本](https://aka.ms/scriptforpermsonhana)并在 SAP HANA 实例上运行该脚本，以解决此问题。

### <a name="usererrorinconsistentssfs"></a>UserErrorInconsistentSSFS

**错误消息** | `SAP HANA error`
------------ | ----------
可能的原因 | 从 SAP HANA 引擎收到的安全存储文件系统 (SSFS) 不一致错误。
**建议的操作** | 请与 SAP HANA 团队合作来解决此问题。 若要了解详细信息，请参阅 SAP 说明 0002097613。

### <a name="usererrorcannotconnecttoazureactivedirectoryservice"></a>UserErrorCannotConnectToAzureActiveDirectoryService

**错误消息** | `Unable to connect to the AAD service from the HANA system.`
--------- | --------
可能的原因 | 作为备份扩展插件服务帐户的防火墙或代理设置不允许与 AAD 建立出站连接。
**建议的操作** | 修复防火墙或代理设置，使到 AAD 的出站连接成功。

### <a name="usererrormisconfiguredsslcastore"></a>UserErrorMisConfiguredSslCaStore

**错误消息** | `Misconfigured CA store`
-------- | -------
可能的原因 | 备份扩展的插件主机进程无法访问根 CA 存储（对于 SLES，其位置为 /var/lib/ca-certificates/ca-bundle.pem）。
**建议的操作** | 通过使用 `chmod o+r` 还原原始权限来修复 CA 存储问题。  然后重启插件主机服务，使备份和还原成功。

### <a name="usererrorbackupfailedasremedialbackupinprogress"></a>UserErrorBackupFailedAsRemedialBackupInProgress

**错误消息** | `Remedial Backup in progress.`
---------- | -------
可能的原因 | Azure 备份会触发补救性完整备份来处理 LSN 日志链中断。 虽然此补救性完整备份正在进行，但通过门户/CLI 触发的备份（完整/差异/增量）失败并出现此错误。
**建议的操作** | 请等待补救性完整备份成功完成，然后再触发其他备份。

### <a name="operationcancelledbecauseconflictingoperationrunningusererror"></a>OperationCancelledBecauseConflictingOperationRunningUserError

**错误消息** | `Conflicting operation in progress.`
----------- | -------------
可能的原因 | 通过门户/CLI/本机 HANA 客户端触发完整/差异/增量备份，而另一个完整/差异/增量备份已在进行中。
**建议的操作** | 请在触发新的完整/增量备份之前先等待活动备份作业完成。

### <a name="operationcancelledbecauseconflictingautohealoperationrunning-usererror"></a>OperationCancelledBecauseConflictingAutohealOperationRunning UserError

**错误消息** | `Auto-heal Full backup in progress.`
------- | -------
可能的原因 | Azure 备份触发自动修复完整备份，以解决 UserErrorHANALSNValidationFailure。 虽然此自动修复备份正在进行中，但 HANA 触发的所有日志备份都将失败，并出现 OperationCancelledBecauseConflictingAutohealOperationRunningUserError。<br>自动修复完整备份完成后，日志和所有其他备份将开始按预期正常工作。</br>
**建议的操作** | 请在触发新的完整/增量备份之前先等待自动修复完整备份完成。

### <a name="usererrorhanaprescriptnotrun"></a>UserErrorHanaPreScriptNotRun

**错误消息** | `Pre-registration script not run.`
--------- | --------
可能的原因 | 用于设置环境的 SAP HANA 预注册脚本尚未运行。
**建议的操作** | 下载[预注册脚本](https://aka.ms/scriptforpermsonhana)并在 SAP HANA 实例上运行该脚本。


### <a name="usererrortargetpoexistsoverwritenotspecified"></a>UserErrorTargetPOExistsOverwriteNotSpecified

**错误消息** | `Target database cannot be overwritten for Restore.`
------- | -------
可能的原因 | 目标数据库存在，但无法覆盖。 门户/CLI 上的还原流中未设置强制覆盖。
**建议的操作** | 请使用所选的“强制覆盖”选项来还原数据库，或将数据库还原为其他目标数据库。

### <a name="usererrorrecoverysysscriptfailedtotriggerrestore"></a>UserErrorRecoverySysScriptFailedToTriggerRestore

**错误消息** | `RecoverySys.py could not be run successfully to restore System DB.`
-------- | ---------
可能的原因 | 导致系统 DB 还原失败的可能原因包括：<ul><li>Azure 备份无法在 HANA 计算机上找到 Recoverysys.py。 当 HANA 环境未正确设置时，会发生这种情况。</li><li>Recoverysys.py 存在，但触发此脚本无法调用 HANA 来执行还原。</li><li>Recoverysys.py 已成功调用 HANA 来执行还原，但 HANA 无法还原。</li></ul>
**建议的操作** | <ul><li>对于问题 1，请与 SAP HANA 团队合作来解决此问题。</li><li>对于问题 2 和 3，请通过在 sid-adm 提示符中运行 HDSetting.sh 命令来查看日志跟踪。 例如，/usr/sap/SID/HDB00/HDBSetting.sh。</li></ul>与 SAP HANA 团队共享这些调查结果来解决问题。

### <a name="usererrordbnamenotincorrectformat"></a>UserErrorDBNameNotInCorrectFormat

**错误消息** | `Restored database name not in correct format.`
--------- | --------
可能的原因 | 你提供的已还原数据库名称未采用可接受的/预期的格式。
**建议的操作** | 请确保还原的数据库名称以字母开头，并且不应包含数字或下划线以外的任何符号。<br>名称最多只能包含 127 个字符，不能以 "\_SYS_\" 开头。

### <a name="usererrordefaultsidadmdirectorychanged"></a>UserErrorDefaultSidAdmDirectoryChanged

**错误消息** | `Default sid-adm directory changed.`
------- | -------
可能的原因 | 默认 sid-adm 目录已更改，HDBSetting.sh 在此默认目录中不可用 。
**建议的操作** | 如果 HXE 是 SID，请确保以 sid-adm 用户身份将环境变量 HOME 设置为 /usr/sap/HXE/home。

### <a name="usererrorhdbsettingsscriptnotfound"></a>UserErrorHDBsettingsScriptNotFound

**错误消息** | `HDBSetting.sh file cannot be found.`
--------- | -------
可能的原因 | 由于 &lt;sid&gt;adm 用户环境找不到 HDBsettings.sh 文件来触发还原，系统数据库还原失败 。
**建议的操作** | 请与 SAP HANA 团队合作来解决此问题。<br><br>如果 HXE 是 SID，请确保以 sid-adm 用户身份将环境变量 HOME 设置为 /usr/sap/HXE/home。

## <a name="restore-checks"></a>还原检查

### <a name="single-container-database-sdc-restore"></a>单容器数据库 (SDC) 还原

在将 HANA 的单容器数据库 (SDC) 还原到另一台 SDC 计算机时请小心提供输入。 数据库名称应以小写字母提供，并在括号中追加“sdc”。 HANA 实例将以大写字母显示。

假设备份了 SDC HANA 实例“H21”。 备份项页会将备份项名称显示为“h21(sdc)”。 如果尝试将此数据库还原到另一个目标 SDC（如 H11），则需要提供以下输入。

![还原的 SDC 数据库名称](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

请注意以下几点：

- 默认情况下会使用备份项名称填充还原的数据库名称。 在本例中为 h21(sdc)。
- 选择 H11 作为目标不会自动更改已还原的数据库名称。 **应将其编辑为 h11(sdc)** 。 对于 SDC，还原的数据库名称将是小写字母形式的目标实例 ID，并且在括号中追加了“sdc”。
- 由于 SDC 只能包含一个数据库，因此还需要选择相应的复选框，以允许使用恢复点数据替代现有的数据库数据。
- 在 Linux 中，此项输入区分大小写。 因此请小心保留大小写。

### <a name="multiple-container-database-mdc-restore"></a>多容器数据库 (MDC) 还原

在 HANA 的多容器数据库中，标准配置是 SYSTEMDB 加上一个或多个租户数据库。 还原整个 SAP HANA 实例意味着还原 SYSTEMDB 和租户数据库。 需要先还原 SYSTEMDB，然后继续还原租户数据库。 还原系统数据库实质上旨在替代所选目标上的系统信息。 这种还原还会替代目标实例中与 BackInt 相关的信息。 因此，在将系统数据库还原到目标实例后，请再次运行注册前脚本。 只有这样，随后的租户 DB 还原才会成功。

## <a name="back-up-a-replicated-vm"></a>备份复制的 VM

### <a name="scenario-1"></a>方案 1

使用 Azure Site Recovery 或 Azure VM 备份复制原始 VM。 新 VM 是为模拟旧的 VM 而构建的。 也就是说，设置完全相同。 （这是因为原始 VM 已被删除，并且已通过 VM 备份或 Azure Site Recovery 完成了还原）。

此方案可包含两种可能的情况。 了解如何在这两种情况下备份复制的 VM：

1. 创建的新 VM 与已删除的 VM 同名，并且位于已删除的 VM 所属的资源组和订阅中。

    - 此扩展已在 VM 中，但对所有服务都不可见
    - 运行预注册脚本
    - 在 Azure 门户中为同一计算机重新注册扩展（“备份” -> “查看详细信息”->“选择相关 Azure VM”->“重新注册”） 
    - 然后，现有的已备份数据库（源自已删除的 VM）应该会成功开始备份

2. 创建的新 VM 会出现以下两种情况之一：

    - 名称与已删除的 VM 不同
    - 名称与已删除的 VM 相同，但资源组或订阅不同于已删除的 VM

    如果出现上述情况，请执行以下步骤：

    - 此扩展已在 VM 中，但对所有服务都不可见
    - 运行预注册脚本
    - 如果发现并保护新数据库，你将在门户中看到重复的活动数据库。 为避免出现这种情况，请对旧数据库[停止保护并保留数据](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)。 然后继续执行其余步骤。
    - 发现数据库以启用备份
    - 对这些数据库启用备份
    - 现有的已备份数据库（源自已删除的 VM）将继续存储在保管库中（根据相应策略保留其备份）

### <a name="scenario-2"></a>方案 2

使用 Azure Site Recovery 或 Azure VM 备份复制原始 VM。 新 VM 是用模板内容构建的。 这是一个新的 VM，具有新的 SID。

请按照以下步骤对新 VM 启用备份：

- 此扩展已在 VM 上，但对所有服务都不可见
- 运行预注册脚本。 根据新 VM 的 SID，可能会出现两种情况：
  - 原始 VM 和新 VM 具有相同的 SID。 预注册脚本将成功运行。
  - 原始 VM 和新 VM 具有不同的 SID。 预注册脚本将失败。 请联系支持人员以获取此方案的帮助。
- 发现要备份的数据库
- 对这些数据库启用备份

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>在同一 VM 上升级 SDC 版本或 MDC 版本

要升级 OS、更改 SDC 版本或更改 MDC 版本，但不会导致 SID 更改，可以按以下方式处理：

- 确保新的 OS 版本、SDC 或 MDC 版本当前[受 Azure 备份支持](sap-hana-backup-support-matrix.md#scenario-support)
- 对数据库[停止保护并保留数据](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 执行升级或更新
- 重新运行预注册脚本。 通常，升级过程可能会删除[必需的角色](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)。 运行注册前脚本可帮助验证所有所需角色。
- 再次恢复对数据库的保护

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>从 SDC 升级到 MDC（不更改 SID）

要从 SDC 升级到 MDC 而不导致 SID 更改，可以按如下方式进行处理：

- 确保新的 MDC 版本当前[受 Azure 备份支持](sap-hana-backup-support-matrix.md#scenario-support)
- 对旧的 SDC 数据库[停止保护并保留数据](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 执行升级。 完成后，HANA 系统现在便是包含一个系统 DB 和多个租户 DB 的 MDC
- 重新运行[预注册脚本](https://aka.ms/scriptforpermsonhana)
- 在 Azure 门户中为同一计算机重新注册扩展（“备份” -> “查看详细信息”->“选择相关 Azure VM”->“重新注册”） 
- 针对同一 VM 选择“重新发现 DB”。 此操作应将步骤 3 中的新 DB 显示为 SYSTEMDB 和租户 DB，而不是 SDC
- 旧的 SDC 数据库将继续位于保管库中，并根据相应策略保留旧的备份数据
- 为这些数据库配置备份

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>从 SDC 升级到 MDC（更改 SID）

要从 SDC 升级到 MDC 并更改 SID，可以按如下方式进行处理：

- 确保新的 MDC 版本当前[受 Azure 备份支持](sap-hana-backup-support-matrix.md#scenario-support)
- 对旧的 SDC 数据库 **停止保护并保留数据**
- 执行升级。 完成后，HANA 系统现在便是包含一个系统 DB 和多个租户 DB 的 MDC
- 重新运行具有正确详细信息（新 SID 和 MDC）的[预注册脚本](https://aka.ms/scriptforpermsonhana)。 由于 SID 发生了更改，你可能会遇到阻碍脚本成功运行的问题。 如果遇到问题，请联系 Azure 备份支持部门。
- 在 Azure 门户中为同一计算机重新注册扩展（“备份” -> “查看详细信息”->“选择相关 Azure VM”->“重新注册”） 
- 针对同一 VM 选择“重新发现 DB”。 此操作应将步骤 3 中的新 DB 显示为 SYSTEMDB 和租户 DB，而不是 SDC
- 旧的 SDC 数据库将继续位于保管库中，并根据相应策略保留旧的备份数据
- 为这些数据库配置备份

## <a name="re-registration-failures"></a>重新注册失败

在触发重新注册操作之前，请检查是否存在以下一种或多种症状：

- VM 上所有操作（例如备份、还原和配置备份）都失败，并出现以下某一错误代码：WorkloadExtensionNotReachable、UserErrorWorkloadExtensionNotInstalled、WorkloadExtensionNotPresent、WorkloadExtensionDidntDequeueMsg。
- 如果备份项的“备份状态”区域显示“无法访问”，请排除可能导致相同状态的所有其他原因 ：

  - 缺少对 VM 执行与备份相关的操作的权限
  - VM 已关闭，因此无法进行备份
  - 网络问题

由于下面的一个或多个原因，可能会出现这些症状：

- 扩展从门户中删除或卸载。
- VM 通过就地磁盘还原及时还原。
- 该 VM 已关闭较长时间，因此其上的扩展配置已过期。
- VM 被删除，并且在已删除 VM 所在的资源组中创建了另一个具有相同名称的 VM。

在上述方案中，我们建议你对 VM 触发重新注册操作。

## <a name="next-steps"></a>后续步骤

- 请查看有关如何在 Azure VM 上备份 SAP HANA 数据库的[常见问题解答](./sap-hana-faq-backup-azure-vm.yml)。
