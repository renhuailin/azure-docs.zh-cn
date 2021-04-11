---
title: Azure Monitor 日志上的系统函数
description: 使用系统函数在 Azure Monitor 日志上编写自定义查询
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: acb45e6ad0250a1f8d10377fdd509e40051f25b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564902"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Azure Monitor 日志上的系统函数

Azure 备份提供了一组函数，这些函数称为系统函数或解决方案函数，默认情况下，可以在 Log Analytics (LA) 工作区中使用这些函数。
 
这些函数可以对 LA 中[原始 Azure 备份表](./backup-azure-reports-data-model.md)中的数据进行操作，并返回格式化数据，帮助你利用简单的查询轻松检索所有与备份相关的实体的信息。 用户可以将参数传递给这些函数，以筛选这些函数返回的数据。 

建议使用系统函数在 LA 工作区中查询备份数据以创建自定义报告，因为这些函数提供了许多好处，如以下部分中所述。

## <a name="benefits-of-using-system-functions"></a>使用系统函数的优点

* 简化查询过程：使用函数有助于减少查询过程中所需的联接数。 默认情况下，函数返回“平展”架构，该架构包含与被查询实体（备份实例、作业、保管库等）相关的所有信息。 例如，如果需要获得按备份项名称及其关联容器筛选的成功备份作业的列表，直接调用 _AzureBackup_getJobs() 函数即可为你提供每个作业的所有此信息。 另一方面，直接查询原始表需要在 [AddonAzureBackupJobs](./backup-azure-reports-data-model.md#addonazurebackupjobs) 和 [CoreAzureBackup](./backup-azure-reports-data-model.md#coreazurebackup) 表之间执行多个联接。

* 实现从旧诊断事件的顺畅转换：使用系统函数有助于实现从[旧诊断事件](./backup-azure-diagnostic-events.md#legacy-event)（AzureDiagnostics 模式下的 AzureBackupReport）到[特定于资源的事件](./backup-azure-diagnostic-events.md#diagnostics-events-available-for-azure-backup-users)的顺畅转换。 Azure 备份提供的所有系统函数都允许你指定一个参数，该参数允许你选择函数应仅从特定于资源的表中查询数据，还是可以同时从旧表和特定于资源的表中查询数据（包含重复数据删除记录）。
    * 如果已成功迁移到特定于资源的表，则可以选择在函数进行查询时排除旧表。
    * 如果当前正在进行迁移，且在旧表中包含一些需要进行分析的数据，则可以选择包含旧表。 转换完成后，如果不再需要旧表中的数据，只需在查询时更新传递给函数的参数值，即可排除旧表。
    * 如果仍旧只使用旧表，那么在选择通过同一参数包含旧表时，这些函数仍然有效。 但是，建议尽早[切换到特定于资源的表](./backup-azure-diagnostic-events.md#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace)。

* 减少自定义查询中断的可能性：如果 Azure 备份通过引入对基础 LA 表架构的改进以适应未来的报告场景，则函数的定义也将随之更新，以考虑架构的更改。 因此，如果使用系统函数创建自定义查询，即使表的基础架构发生更改，查询也不会中断。

> [!NOTE]
> 系统函数由 Microsoft 提供维护，且用户无法编辑其定义。 如果需要可编辑的函数，可以在 LA 中创建[保存的函数](../azure-monitor/logs/functions.md)。

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Azure 备份提供的系统函数的类型

* 核心函数：这些函数有助于查询任何关键 Azure 备份实体，如备份实例、保管库、策略、作业和计费实体。 例如，_AzureBackup_getBackupInstances 函数可返回最近一天（协调世界时）在环境中存在的所有备份实例的列表。 本文总结了其中每种核心函数对应的参数和返回的架构。

* 趋势函数：这些函数可返回与备份相关的实体（例如，备份实例、计费组）的历史记录，并允许你获取有关这些实体的关键指标（例如，计数、已使用的存储）的每日、每周和每月趋势信息。 本文总结了其中每种趋势函数对应的参数和返回的架构。

> [!NOTE]
> 目前，系统函数可返回最近一天（协调世界时）的数据。 不会返回目前未满一天的数据。 因此，如果希望检索当天的记录，需要使用原始 LA 表。


## <a name="list-of-system-functions"></a>系统函数列表

### <a name="core-functions"></a>核心函数

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults()

此函数返回 Azure 环境中与 LA 工作区关联的所有恢复服务保管库的列表。

**Parameters**

| **参数名称** | **说明** | **必需？** | **示例值** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 仅在以下情况下将此参数与 RangeEnd 参数一起使用：需要获取从 RangeStart 到 RangeEnd 的时间段内所有与保管库相关的记录。 默认情况下，RangeStart 和 RangeEnd 的值为 null，这将使函数仅检索每个保管库的最新记录。 | N | “2021-03-03 00:00:00” |
| RangeEnd     | 仅在以下情况下将此参数与 RangeStart 参数一起使用：需要获取从 RangeStart 到 RangeEnd 的时间段内所有与保管库相关的记录。 默认情况下，RangeStart 和 RangeEnd 的值为 null，这将使函数仅检索每个保管库的最新记录。 | N |“2021-03-10 00:00:00”|
| VaultSubscriptionList   | 此参数用于筛选存在备份数据的一组特定订阅的函数输出。 指定以逗号分隔的订阅 ID 列表作为此函数的参数有助于仅检索指定订阅中的那些保管库。 默认情况下，此参数的值为“*”，这使函数能够跨所有订阅搜索记录。 | N | “00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111”|
| VaultLocationList     | 此参数用于筛选存在备份数据的一组特定区域的函数输出。 指定以逗号分隔的区域列表作为此函数的参数有助于仅检索指定区域中的那些保管库。 默认情况下，此参数的值为“*”，这使函数能够跨所有区域搜索记录。 | N | “eastus,westus”|
| VaultList    |此参数用于筛选一组特定保管库的函数输出。 指定以逗号分隔的保管库名称列表作为此函数的参数有助于检索仅与指定保管库相关的记录。 默认情况下，此参数的值为“*”，这使函数能够跨所有保管库搜索记录。 | N |“vault1,vault2,vault3”|
| VaultTypeList     | 此参数用于筛选与特定保管库类型相关的记录的函数输出。 目前唯一受支持的保管库类型为“Microsoft.RecoveryServices/vaults”，这是此参数的默认值 | N | “Microsoft.RecoveryServices/vaults”|
| ExcludeLegacyEvent  | 此参数用于选择是否在旧 AzureDiagnostics 表中查询数据。 如果此参数的值为 false，则函数同时查询 AzureDiagnostics 表和特定于资源的表中的数据。 如果此参数的值为 true，则函数仅查询特定于资源的表中的数据。 默认值为 true。 | N | 是 |

**返回的字段**

| **字段名称** | **说明** |
| -------------- | --------------- |
| UniqueId | 用于指示保管库唯一 ID 的主键 |
| ID | 保管库的 Azure 资源管理器 (ARM) ID |
| 名称 | 保管库的名称 |
| SubscriptionId | 存在保管库的订阅 ID |
| 位置 | 存在保管库的位置 |
| VaultStore_StorageReplicationType | 与保管库关联的存储复制类型 |
| 标记 | 保管库的标记 |
| TimeGenerated | 记录的时间戳 |
| 类型 |  保管库的类型，即“Microsoft.RecoveryServices/vaults”|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies()

此函数可返回在 Azure 环境中使用的备份策略的列表，以及有关每个策略的详细信息，例如，数据源类型、存储复制类型等。

**Parameters**

| **参数名称** | **说明** | **必需？** | **示例值** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 仅在以下情况下将此参数与 RangeStart 参数一起使用：需要获取从 RangeStart 到 RangeEnd 的时间段内所有与策略相关的记录。 默认情况下，RangeStart 和 RangeEnd 的值为 null，这将使函数仅检索每个策略的最新记录。 | N | “2021-03-03 00:00:00” |
| RangeEnd     | 仅在以下情况下将此参数与 RangeStart 参数一起使用：需要获取从 RangeStart 到 RangeEnd 的时间段内所有与策略相关的记录。 默认情况下，RangeStart 和 RangeEnd 的值为 null，这将使函数仅检索每个策略的最新记录。 | N |“2021-03-10 00:00:00”|
| VaultSubscriptionList   | 此参数用于筛选存在备份数据的一组特定订阅的函数输出。 指定以逗号分隔的订阅 ID 列表作为此函数的参数有助于仅检索指定订阅中的那些策略。 默认情况下，此参数的值为“*”，这使函数能够跨所有订阅搜索记录。 | N | “00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111”|
| VaultLocationList     | 此参数用于筛选存在备份数据的一组特定区域的函数输出。 指定以逗号分隔的区域列表作为此函数的参数有助于仅检索指定区域中的那些策略。 默认情况下，此参数的值为“*”，这使函数能够跨所有区域搜索记录。 | N | “eastus,westus”|
| VaultList    |此参数用于筛选一组特定保管库的函数输出。 指定以逗号分隔的保管库名称列表作为此函数的参数有助于检索仅与指定保管库相关的策略记录。 默认情况下，此参数的值为“*”，这使函数能够跨所有保管库搜索策略记录。 | N |“vault1,vault2,vault3”|
| VaultTypeList     | 此参数用于筛选与特定保管库类型相关的记录的函数输出。 目前唯一受支持的保管库类型为“Microsoft.RecoveryServices/vaults”，这是此参数的默认值。 | N | “Microsoft.RecoveryServices/vaults”|
| ExcludeLegacyEvent  | 此参数用于选择是否在旧 AzureDiagnostics 表中查询数据。 如果此参数的值为 false，则函数同时查询 AzureDiagnostics 表和特定于资源的表中的数据。 如果此参数的值为 true，则函数仅查询特定于资源的表中的数据。 默认值为 true。 | N | 是 |
| BackupSolutionList | 此参数用于筛选 Azure 环境中使用的一组特定备份解决方案的函数输出。 例如，如果将“Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM”指定为此参数的值，则函数仅返回与使用 Azure 虚拟机备份、Azure VM 备份中的 SQL 或用 DPM 备份到 Azure 备份的项相关的记录。 默认情况下，此参数的值为“*”，这使函数能够返回与备份报表支持的所有备份解决方案相关的记录（支持的值为“Azure Virtual Machine Backup”、“SQL in Azure VM Backup”、“SAP HANA in Azure VM Backup”、“Azure Storage (Azure Files) Backup”、“Azure Backup Agent”、“DPM”、“Azure Backup Server”或以逗号分隔的任意这些值的组合）。 | N | “Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent” |

**返回的字段**

| **字段名称** | **说明** |
| -------------- | --------------- |
| UniqueId | 用于指示策略唯一 ID 的主键 |
| ID | 策略的 Azure 资源管理器 (ARM) ID |
| 名称 | 策略的名称 |
| 备份解决方案 | 策略与之关联的备份解决方案。 例如，“Azure VM Backup”、“SQL in Azure VM Backup”等。 |
| TimeGenerated | 记录的时间戳 |
| VaultUniqueId | 引用与策略关联的保管库的外键 |
| VaultResourceId | 与策略关联的保管库的 Azure 资源管理器 (ARM) ID |
| VaultName | 与策略关联的保管库的名称 |
| VaultTags | 与策略关联的保管库的标记 |
| VaultLocation | 与策略关联的保管库的位置 |
| VaultSubscriptionId | 与策略关联的保管库的订阅 ID |
| VaultStore_StorageReplicationType | 与策略关联的保管库的存储复制类型 |
| VaultType | 保管库的类型，即“Microsoft.RecoveryServices/vaults” |
| ExtendedProperties | 策略的附加属性 |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs()

此函数可返回在指定时间范围内触发的所有与备份和还原相关的作业的列表，以及有关每个作业的详细信息，例如作业状态、作业持续时间、传输的数据等。

**Parameters**

| **参数名称** | **说明** | **必需？** | **示例值** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 在以下情况下将此参数与 RangeEnd 参数一起使用：检索从 RangeStart 到 RangeEnd 的时间段内启动的所有作业的列表。 | Y | “2021-03-03 00:00:00” |
| RangeEnd     | 在以下情况下将此参数与 RangeStart 参数一起使用：检索从 RangeStart 到 RangeEnd 的时间段内启动的所有作业的列表。 | Y |“2021-03-10 00:00:00”|
| VaultSubscriptionList   | 此参数用于筛选存在备份数据的一组特定订阅的函数输出。 指定以逗号分隔的订阅 ID 列表作为此函数的参数有助于仅检索指定订阅中与保管库关联的那些作业。 默认情况下，此参数的值为“*”，这使函数能够跨所有订阅搜索记录。 | N | “00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111”|
| VaultLocationList     | 此参数用于筛选存在备份数据的一组特定区域的函数输出。 指定以逗号分隔的区域列表作为此函数的参数有助于仅检索指定区域中与保管库关联的那些作业。 默认情况下，此参数的值为“*”，这使函数能够跨所有区域搜索记录。 | N | “eastus,westus”|
| VaultList    | 此参数用于筛选一组特定保管库的函数输出。 指定以逗号分隔的保管库名称列表作为此函数的参数有助于检索仅与指定保管库相关的作业。 默认情况下，此参数的值为“*”，这使函数能够跨所有保管库搜索作业。 | N |“vault1,vault2,vault3”|
| VaultTypeList     | 此参数用于筛选与特定保管库类型相关的记录的函数输出。 目前唯一受支持的保管库类型为“Microsoft.RecoveryServices/vaults”，这是此参数的默认值。 | N | “Microsoft.RecoveryServices/vaults”|
| ExcludeLegacyEvent  | 此参数用于选择是否在旧 AzureDiagnostics 表中查询数据。 如果此参数的值为 false，则函数同时查询 AzureDiagnostics 表和特定于资源的表中的数据。 如果此参数的值为 true，则函数仅查询特定于资源的表中的数据。 默认值为 true。 | N | 是 |
| BackupSolutionList | 此参数用于筛选 Azure 环境中使用的一组特定备份解决方案的函数输出。 例如，如果将“Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM”指定为此参数的值，则函数仅返回与使用 Azure 虚拟机备份、Azure VM 备份中的 SQL 或用 DPM 备份到 Azure 备份的项相关的记录。 默认情况下，此参数的值为“*”，这使函数能够返回与备份报表支持的所有备份解决方案相关的记录（支持的值为“Azure Virtual Machine Backup”、“SQL in Azure VM Backup”、“SAP HANA in Azure VM Backup”、“Azure Storage (Azure Files) Backup”、“Azure Backup Agent”、“DPM”、“Azure Backup Server”或以逗号分隔的任意这些值的组合）。 | N | “Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent” |
| JobOperationList | 此参数用于筛选特定作业类型的函数输出。 例如，“Backup”或“Restore”。 默认情况下，此参数的值为“*”，这使函数能够同时搜索备份作业和还原作业。 | N | “Backup” | 
| JobStatusList | 此参数用于筛选特定作业状态的函数输出。 例如，“Completed”、“Failed”等。 默认情况下，此参数的值为“*”，这使函数能够搜索所有作业，而不考虑状态。 | N | “Failed,CompletedWithWarnings” |
| JobFailureCodeList | 此参数用于筛选特定失败代码的函数输出。 默认情况下，此参数的值为“*”，这使函数能够搜索所有作业，而不考虑失败代码。 | N | “Success”
| DatasourceSetName | 此参数用于筛选特定父资源的函数输出。 例如，要返回属于虚拟机“testvm”的 SQL in Azure VM 备份实例，请指定“testvm”作为此参数的值。 默认情况下，此值为“*”，这使函数能够跨所有备份实例搜索记录。 | N | “testvm” |
| BackupInstanceName | 此参数用于按名称搜索特定备份实例上的作业。 默认情况下，此值为“*”，这使函数能够跨所有备份实例搜索记录。 | N | “testvm” |
| ExcludeLog | 此参数用于排除函数返回的日志作业（有助于提高查询性能）。 默认情况下，此参数的值为 true，这使函数能够排除日志作业。 | N | 是


**返回的字段**

| **字段名称** | **说明** |
| -------------- | --------------- |
| UniqueId | 用于指示作业唯一 ID 的主键 |
| OperationCategory | 正在执行的操作的类别。 例如，“Backup”、“Restore” |
| 操作 | 正在执行的操作的详细信息。 例如，“Log”（用于日志备份）|
| 状态 | 作业的状态。 例如，“Completed”、“Failed”、“CompletedWithWarnings” |
| ErrorTitle | 作业的失败代码 |
| StartTime | 启动作业的日期和时间 |
| DurationInSecs | 作业持续时间（秒） |
| DataTransferredInMBs | 作业传输的数据 (MB) |
| RestoreJobRPDateTime | 要恢复的恢复点的创建日期和时间 |
| RestoreJobRPLocation | 要恢复的恢复点的存储位置 |
| BackupInstanceUniqueId | 引用与作业关联的备份实例的外键 |
| BackupInstanceId | 与作业关联的备份实例的 Azure 资源管理器 (ARM) ID |
| BackupInstanceFriendlyName | 与作业关联的备份实例的名称 |
| DatasourceResourceId | 与作业关联的基础数据源的 Azure 资源管理器 (ARM) ID。 例如，“VM 的 Azure 资源管理器 (ARM) ID” |
| DatasourceFriendlyName | 与作业关联的基础数据源的友好名称 |
| DatasourceType | 与作业关联的数据源的类型。 例如，“Microsoft.Compute/virtualMachines” |
| BackupSolution | 作业与之关联的备份解决方案。 例如，“Azure VM Backup”、“SQL in Azure VM Backup”等。 |
| DatasourceSetResourceId | 数据源父资源的 Azure 资源管理器 (ARM) ID（如果适用）。 例如，对于 SQL in Azure VM 数据源，此字段将包含存在 SQL 数据库的 VM 的 Azure 资源管理器 (ARM) ID |
| DatasourceSetType | 数据源父资源的类型（如果适用）。 例如，对于 SAP HANA in Azure VM 数据源，此字段将为“Microsoft.Compute/virtualMachines”，因为父资源为 Azure VM |
| VaultResourceId | 与作业关联的保管库的 Azure 资源管理器 (ARM) ID |
| VaultUniqueId | 引用与作业关联的保管库的外键 |
| VaultName | 与作业关联的保管库的名称 |
| VaultTags | 与作业关联的保管库的标记 |
| VaultSubscriptionId | 与作业关联的保管库的订阅 ID |
| VaultLocation | 与作业关联的保管库的位置 |
| VaultStore_StorageReplicationType | 与作业关联的保管库的存储复制类型 |
| VaultType | 保管库的类型，即“Microsoft.RecoveryServices/vaults” |
| TimeGenerated | 记录的时间戳 |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances()

此函数用于返回与恢复服务保管库关联的备份实例的列表，以及有关每个备份实例的详细信息，如云存储使用量、关联的策略等。

**Parameters**

| **参数名称** | **说明** | **必需？** | **示例值** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 仅在以下情况下将此参数与 RangeEnd 参数一起使用：需要获取从 RangeStart 到 RangeEnd 的时间段内所有与备份实例相关的记录。 默认情况下，RangeStart 和 RangeEnd 的值为 null，这将使函数仅检索每个备份实例的最新记录。 | N | “2021-03-03 00:00:00” |
| RangeEnd     | 仅在以下情况下将此参数与 RangeStart 参数一起使用：需要获取从 RangeStart 到 RangeEnd 的时间段内所有与备份实例相关的记录。 默认情况下，RangeStart 和 RangeEnd 的值为 null，这将使函数仅检索每个备份实例的最新记录。 | N |“2021-03-10 00:00:00”|
| VaultSubscriptionList   | 此参数用于筛选存在备份数据的一组特定订阅的函数输出。 指定以逗号分隔的订阅 ID 列表作为此函数的参数有助于仅检索指定订阅中的那些备份实例。 默认情况下，此参数的值为“*”，这使函数能够跨所有订阅搜索记录。 | N | “00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111”|
| VaultLocationList     | 此参数用于筛选存在备份数据的一组特定区域的函数输出。 指定以逗号分隔的区域列表作为此函数的参数有助于仅检索指定区域中的那些备份实例。 默认情况下，此参数的值为“*”，这使函数能够跨所有区域搜索记录。 | N | “eastus,westus”|
| VaultList    |此参数用于筛选一组特定保管库的函数输出。 指定以逗号分隔的保管库名称列表作为此函数的参数有助于检索仅与指定保管库相关的备份实例记录。 默认情况下，此参数的值为“*”，这使函数能够跨所有保管库搜索备份实例记录。 | N |“vault1,vault2,vault3”|
| VaultTypeList     | 此参数用于筛选与特定保管库类型相关的记录的函数输出。 目前唯一受支持的保管库类型为“Microsoft.RecoveryServices/vaults”，这是此参数的默认值。 | N | “Microsoft.RecoveryServices/vaults”|
| ExcludeLegacyEvent  | 此参数用于选择是否在旧 AzureDiagnostics 表中查询数据。 如果此参数的值为 false，则函数同时查询 AzureDiagnostics 表和特定于资源的表中的数据。 如果此参数的值为 true，则函数仅查询特定于资源的表中的数据。 默认值为 true。 | N | 是 |
| BackupSolutionList | 此参数用于筛选 Azure 环境中使用的一组特定备份解决方案的函数输出。 例如，如果将“Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM”指定为此参数的值，则函数仅返回与使用 Azure 虚拟机备份、Azure VM 备份中的 SQL 或用 DPM 备份到 Azure 备份的项相关的记录。 默认情况下，此参数的值为“*”，这使函数能够返回与备份报表支持的所有备份解决方案相关的记录（支持的值为“Azure Virtual Machine Backup”、“SQL in Azure VM Backup”、“SAP HANA in Azure VM Backup”、“Azure Storage (Azure Files) Backup”、“Azure Backup Agent”、“DPM”、“Azure Backup Server”或以逗号分隔的任意这些值的组合）。 | N | “Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent” |
| ProtectionInfoList | 此参数用于选择是仅包含受到积极保护的那些备份实例，还是也包含已停止受到保护的那些实例以及初始备份挂起的实例。 支持的值为“Protected”、“ProtectionStopped”、“InitialBackupPending”或以逗号分隔的这些值的任意组合。 默认情况下，此值为“*”，这使函数能够搜索所有备份实例，而不考虑保护详细信息。 | N | “Protected” |
| DatasourceSetName | 此参数用于筛选特定父资源的函数输出。 例如，要返回属于虚拟机“testvm”的 SQL in Azure VM 备份实例，请指定“testvm”作为此参数的值。 默认情况下，此值为“*”，这使函数能够跨所有备份实例搜索记录。 | N | “testvm” |
| BackupInstanceName | 此参数用于按名称搜索特定备份实例。 默认情况下，此值为“*”，这使函数能够搜索所有备份实例。 | N | “testvm” |
| DisplayAllFields | 此参数用于选择是否仅检索函数返回的字段的子集。 如果此参数的值为 false，则函数将从函数输出中消除与存储和保留点相关的信息。 将此函数用作较大查询中的中间步骤，且需要通过消除不需要进行分析的列来优化查询性能时，此函数非常有用。 默认情况下，此参数的值为 true，这使函数能够返回与备份实例相关的所有字段。 | N | 是 |

**返回的字段**

| **字段名称** | **说明** |
| -------------- | --------------- |
| UniqueId | 用于指示备份实例唯一 ID 的主键 |
| ID | 备份实例的 Azure 资源管理器 (ARM) ID |
| FriendlyName | 备份实例的友好名称 |
| ProtectionInfo | 有关备份实例保护设置的信息。 例如，“protection configured”、“protection stopped”、“initial backup pending” |
| LatestRecoveryPoint | 与备份实例关联的最新恢复点的日期和时间 |
| OldestRecoveryPoint | 与备份实例关联的最早恢复点的日期和时间 |
| SourceSizeInMBs | 备份实例的前端大小 (MB) |
| VaultStore_StorageConsumptionInMBs | 保管库标准层中备份实例使用的云存储总量 |
| DataSourceFriendlyName | 对应于备份实例的数据源的友好名称 |
| BackupSolution | 备份实例与之关联的备份解决方案。 例如，“Azure VM Backup”、“SQL in Azure VM Backup”等。 |
| DatasourceType | 对应于备份实例的数据源的类型。 例如，“Microsoft.Compute/virtualMachines” |
| DatasourceResourceId | 对应于备份实例的基础数据源的 Azure 资源管理器 (ARM) ID。 例如，“VM 的 Azure 资源管理器 (ARM) ID” |
| DatasourceSetFriendlyName | 数据源父资源的友好名称（如果适用）。 例如，对于 SQL in Azure VM 数据源，此字段将包含存在 SQL 数据库的 VM 的名称 |
| DatasourceSetResourceId | 数据源父资源的 Azure 资源管理器 (ARM) ID（如果适用）。 例如，对于 SQL in Azure VM 数据源，此字段将包含存在 SQL 数据库的 VM 的 Azure 资源管理器 (ARM) ID |
| DatasourceSetType | 数据源父资源的类型（如果适用）。 例如，对于 SAP HANA in Azure VM 数据源，此字段将为“Microsoft.Compute/virtualMachines”，因为父资源为 Azure VM |
| PolicyName | 与备份实例关联的策略名称 |
| PolicyUniqueId | 引用与备份实例关联的策略的外键  |
| `PolicyId` | 与备份实例关联的策略的 Azure 资源管理器 (ARM) ID |
| VaultResourceId | 与备份实例关联的保管库的 Azure 资源管理器 (ARM) ID |
| VaultUniqueId | 引用与备份实例关联的保管库的外键 |
| VaultName | 与备份实例关联的保管库的名称 |
| VaultTags | 与备份实例关联的保管库的标记 |
| VaultSubscriptionId | 与备份实例关联的保管库的订阅 ID |
| VaultLocation | 与备份实例关联的保管库的位置 |
| VaultStore_StorageReplicationType | 与备份实例关联的保管库的存储复制类型 |
| VaultType | 保管库的类型，即“Microsoft.RecoveryServices/vaults” |
| TimeGenerated | 记录的时间戳 |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups()

此函数可返回所有与备份相关的计费实体（计费组）的列表，以及有关关键计费组件的信息（如前端大小和云存储总量）。

**Parameters**

| **参数名称** | **说明** | **必需？** | **示例值** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 仅在以下情况下将此参数与 RangeEnd 参数一起使用：需要获取从 RangeStart 到 RangeEnd 的时间段内所有与计费组相关的记录。 默认情况下，RangeStart 和 RangeEnd 的值为 null，这将使函数仅检索每个计费组的最新记录。 | N | “2021-03-03 00:00:00” |
| RangeEnd     | 仅在以下情况下将此参数与 RangeStart 参数一起使用：需要获取从 RangeStart 到 RangeEnd 的时间段内所有与计费组相关的记录。 默认情况下，RangeStart 和 RangeEnd 的值为 null，这将使函数仅检索每个计费组的最新记录。 | N |“2021-03-10 00:00:00”|
| VaultSubscriptionList   | 此参数用于筛选存在备份数据的一组特定订阅的函数输出。 指定以逗号分隔的订阅 ID 列表作为此函数的参数有助于仅检索指定订阅中的那些计费组。 默认情况下，此参数的值为“*”，这使函数能够跨所有订阅搜索记录。 | N | “00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111”|
| VaultLocationList     | 此参数用于筛选存在备份数据的一组特定区域的函数输出。 指定以逗号分隔的区域列表作为此函数的参数有助于仅检索指定区域中的那些计费组。 默认情况下，此参数的值为“*”，这使函数能够跨所有区域搜索记录。 | N | “eastus,westus”|
| VaultList    |此参数用于筛选一组特定保管库的函数输出。 指定以逗号分隔的保管库名称列表作为此函数的参数有助于检索仅与指定保管库相关的备份实例记录。 默认情况下，此参数的值为“*”，这使函数能够跨所有保管库搜索计费组记录。 | N |“vault1,vault2,vault3”|
| VaultTypeList     | 此参数用于筛选与特定保管库类型相关的记录的函数输出。 目前唯一受支持的保管库类型为“Microsoft.RecoveryServices/vaults”，这是此参数的默认值。 | N | “Microsoft.RecoveryServices/vaults”|
| ExcludeLegacyEvent  | 此参数用于选择是否在旧 AzureDiagnostics 表中查询数据。 如果此参数的值为 false，则函数同时查询 AzureDiagnostics 表和特定于资源的表中的数据。 如果此参数的值为 true，则函数仅查询特定于资源的表中的数据。 默认值为 true。 | N | 是 |
| BackupSolutionList | 此参数用于筛选 Azure 环境中使用的一组特定备份解决方案的函数输出。 例如，如果将“Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM”指定为此参数的值，则函数仅返回与使用 Azure 虚拟机备份、Azure VM 备份中的 SQL 或用 DPM 备份到 Azure 备份的项相关的记录。 默认情况下，此参数的值为“*”，这使函数能够返回与备份报表支持的所有备份解决方案相关的记录（支持的值为“Azure Virtual Machine Backup”、“SQL in Azure VM Backup”、“SAP HANA in Azure VM Backup”、“Azure Storage (Azure Files) Backup”、“Azure Backup Agent”、“DPM”、“Azure Backup Server”或以逗号分隔的任意这些值的组合）。 | N | “Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent” |
| BillingGroupName | 此参数用于按名称搜索特定计费组。 默认情况下，此值为“*”，这使函数能够搜索所有计费组。 | N | “testvm” |

**返回的字段**

| **字段名称** | **说明** |
| -------------- | --------------- |
| UniqueId | 用于指示计费组唯一 ID 的主键 |
| FriendlyName | 计费组的友好名称 |
| 名称 | 计费组的名称 |
| 类型 | 计费组的类型。 例如，“ProtectedContainer”或“BackupItem” |
| SourceSizeInMBs | 计费组的前端大小 (MB) |
| VaultStore_StorageConsumptionInMBs | 保管库标准层中计费组使用的云存储总量 |
| BackupSolution | 计费组与之关联的备份解决方案。 例如，“Azure VM Backup”、“SQL in Azure VM Backup”等。 |
| VaultResourceId | 与计费组关联的保管库的 Azure 资源管理器 (ARM) ID |
| VaultUniqueId | 引用与计费组关联的保管库的外键 |
| VaultName | 与计费组关联的保管库的名称 |
| VaultTags | 与计费组关联的保管库的标记 |
| VaultSubscriptionId | 与计费组关联的保管库的订阅 ID |
| VaultLocation | 与计费组关联的保管库的位置 |
| VaultStore_StorageReplicationType | 与计费组关联的保管库的存储复制类型 |
| VaultType | 保管库的类型，即“Microsoft.RecoveryServices/vaults” |
| TimeGenerated | 记录的时间戳 |
| ExtendedProperties | 计费组的其他属性 |

### <a name="trend-functions"></a>趋势函数

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends()

此函数可返回每个备份实例的历史记录，使你能够在多个粒度级别查看与备份实例计数和存储使用量相关的每日、每周和每月的关键趋势。

**Parameters**

| **参数名称** | **说明** | **必需？** | **示例值** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 在以下情况下将此参数与 RangeEnd 参数一起使用：检索从 RangeStart 到 RangeEnd 的时间段内所有与备份实例相关的记录。 | Y | “2021-03-03 00:00:00” |
| RangeEnd     | 在以下情况下将此参数与 RangeStart 参数一起使用：检索从 RangeStart 到 RangeEnd 的时间段内所有与备份实例相关的记录。 | Y |“2021-03-10 00:00:00”|
| VaultSubscriptionList   | 此参数用于筛选存在备份数据的一组特定订阅的函数输出。 指定以逗号分隔的订阅 ID 列表作为此函数的参数有助于仅检索指定订阅中的那些备份实例。 默认情况下，此参数的值为“*”，这使函数能够跨所有订阅搜索记录。 | N | “00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111”|
| VaultLocationList     | 此参数用于筛选存在备份数据的一组特定区域的函数输出。 指定以逗号分隔的区域列表作为此函数的参数有助于仅检索指定区域中的那些备份实例。 默认情况下，此参数的值为“*”，这使函数能够跨所有区域搜索记录。 | N | “eastus,westus”|
| VaultList    |此参数用于筛选一组特定保管库的函数输出。 指定以逗号分隔的保管库名称列表作为此函数的参数有助于检索仅与指定保管库相关的备份实例记录。 默认情况下，此参数的值为“*”，这使函数能够跨所有保管库搜索备份实例记录。 | N |“vault1,vault2,vault3”|
| VaultTypeList     | 此参数用于筛选与特定保管库类型相关的记录的函数输出。 目前唯一受支持的保管库类型为“Microsoft.RecoveryServices/vaults”，这是此参数的默认值。 | N | “Microsoft.RecoveryServices/vaults”|
| ExcludeLegacyEvent  | 此参数用于选择是否在旧 AzureDiagnostics 表中查询数据。 如果此参数的值为 false，则函数同时查询 AzureDiagnostics 表和特定于资源的表中的数据。 如果此参数的值为 true，则函数仅查询特定于资源的表中的数据。 默认值为 true。 | N | 是 |
| BackupSolutionList | 此参数用于筛选 Azure 环境中使用的一组特定备份解决方案的函数输出。 例如，如果将“Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM”指定为此参数的值，则函数仅返回与使用 Azure 虚拟机备份、Azure VM 备份中的 SQL 或用 DPM 备份到 Azure 备份的项相关的记录。 默认情况下，此参数的值为“*”，这使函数能够返回与备份报表支持的所有备份解决方案相关的记录（支持的值为“Azure Virtual Machine Backup”、“SQL in Azure VM Backup”、“SAP HANA in Azure VM Backup”、“Azure Storage (Azure Files) Backup”、“Azure Backup Agent”、“DPM”、“Azure Backup Server”或以逗号分隔的任意这些值的组合）。 | N | “Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent” |
| ProtectionInfoList | 此参数用于选择是仅包含受到积极保护的那些备份实例，还是也包含已停止受到保护的那些实例以及初始备份挂起的实例。 支持的值为“Protected”、“ProtectionStopped”、“InitialBackupPending”或以逗号分隔的这些值的任意组合。 默认情况下，此值为“*”，这使函数能够搜索所有备份实例，而不考虑保护详细信息。 | N | “Protected” |
| DatasourceSetName | 此参数用于筛选特定父资源的函数输出。 例如，要返回属于虚拟机“testvm”的 SQL in Azure VM 备份实例，请指定“testvm”作为此参数的值。 默认情况下，此值为“*”，这使函数能够跨所有备份实例搜索记录。 | N | “testvm” |
| BackupInstanceName | 此参数用于按名称搜索特定备份实例。 默认情况下，此值为“*”，这使函数能够搜索所有备份实例。 | N | “testvm” |
| DisplayAllFields | 此参数用于选择是否仅检索函数返回的字段的子集。 如果此参数的值为 false，则函数将从函数输出中消除与存储和保留点相关的信息。 将此函数用作较大查询中的中间步骤，且需要通过消除不需要进行分析的列来优化查询性能时，此函数非常有用。 默认情况下，此参数的值为 true，这使函数能够返回与备份实例相关的所有字段。 | N | 是 |
| AggregationType | 此参数用于指定应检索数据的时间粒度。 如果此参数的值为“Daily”，则函数将每天返回每个备份实例的记录，使你能够分析存储使用量和备份实例计数的每日趋势。 如果此参数的值为“Weekly”，则函数将每周返回每个备份实例的记录，使你能够分析每周趋势。 同样，可以指定“Monthly”以分析每月趋势。 默认值为“Daily”。 如果在更大的时间范围内查看数据，建议使用“Weekly”或“Monthly”，以提高查询性能并便于趋势分析。 | N | “Weekly” |

**返回的字段**

| **字段名称** | **说明** |
| -------------- | --------------- |
| UniqueId | 用于指示备份实例唯一 ID 的主键 |
| ID | 备份实例的 Azure 资源管理器 (ARM) ID |
| FriendlyName | 备份实例的友好名称 |
| ProtectionInfo | 有关备份实例保护设置的信息。 例如，“protection configured”、“protection stopped”、“initial backup pending” |
| LatestRecoveryPoint | 与备份实例关联的最新恢复点的日期和时间 |
| OldestRecoveryPoint | 与备份实例关联的最早恢复点的日期和时间 |
| SourceSizeInMBs | 备份实例的前端大小 (MB) |
| VaultStore_StorageConsumptionInMBs | 保管库标准层中备份实例使用的云存储总量 |
| DataSourceFriendlyName | 对应于备份实例的数据源的友好名称 |
| BackupSolution | 备份实例与之关联的备份解决方案。 例如，“Azure VM Backup”、“SQL in Azure VM Backup”等。 |
| DatasourceType | 对应于备份实例的数据源的类型。 例如，“Microsoft.Compute/virtualMachines” |
| DatasourceResourceId | 对应于备份实例的基础数据源的 Azure 资源管理器 (ARM) ID。 例如，“VM 的 Azure 资源管理器 (ARM) ID” |
| DatasourceSetFriendlyName | 数据源父资源的友好名称（如果适用）。 例如，对于 SQL in Azure VM 数据源，此字段将包含存在 SQL 数据库的 VM 的名称 |
| DatasourceSetResourceId | 数据源父资源的 Azure 资源管理器 (ARM) ID（如果适用）。 例如，对于 SQL in Azure VM 数据源，此字段将包含存在 SQL 数据库的 VM 的 Azure 资源管理器 (ARM) ID |
| DatasourceSetType | 数据源父资源的类型（如果适用）。 例如，对于 SAP HANA in Azure VM 数据源，此字段将为“Microsoft.Compute/virtualMachines”，因为父资源为 Azure VM |
| PolicyName | 与备份实例关联的策略名称 |
| PolicyUniqueId | 引用与备份实例关联的策略的外键  |
| `PolicyId` | 与备份实例关联的策略的 Azure 资源管理器 (ARM) ID |
| VaultResourceId | 与备份实例关联的保管库的 Azure 资源管理器 (ARM) ID |
| VaultUniqueId | 引用与备份实例关联的保管库的外键 |
| VaultName | 与备份实例关联的保管库的名称 |
| VaultTags | 与备份实例关联的保管库的标记 |
| VaultSubscriptionId | 与备份实例关联的保管库的订阅 ID |
| VaultLocation | 与备份实例关联的保管库的位置 |
| VaultStore_StorageReplicationType | 与备份实例关联的保管库的存储复制类型 |
| VaultType | 保管库的类型，即“Microsoft.RecoveryServices/vaults” |
| TimeGenerated | 记录的时间戳 |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends()

此函数返回每个计费实体的历史记录，使你能够在多个粒度级别查看与前端大小计数和存储使用量相关的每日、每周和每月的关键趋势。

**Parameters**

| **参数名称** | **说明** | **必需？** | **示例值** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | 在以下情况下将此参数与 RangeEnd 参数一起使用：检索从 RangeStart 到 RangeEnd 的时间段内所有与计费组相关的记录。 | Y | “2021-03-03 00:00:00” |
| RangeEnd     | 在以下情况下将此参数与 RangeStart 参数一起使用：检索从 RangeStart 到 RangeEnd 的时间段内所有与计费组相关的记录。 | Y |“2021-03-10 00:00:00”|
| VaultSubscriptionList  | 此参数用于筛选存在备份数据的一组特定订阅的函数输出。 指定以逗号分隔的订阅 ID 列表作为此函数的参数有助于仅检索指定订阅中的那些计费组。 默认情况下，此参数的值为“*”，这使函数能够跨所有订阅搜索记录。 | N | “00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111”|
| VaultLocationList     | 此参数用于筛选存在备份数据的一组特定区域的函数输出。 指定以逗号分隔的区域列表作为此函数的参数有助于仅检索指定区域中的那些计费组。 默认情况下，此参数的值为“*”，这使函数能够跨所有区域搜索记录。 | N | “eastus,westus”|
| VaultList    |此参数用于筛选一组特定保管库的函数输出。 指定以逗号分隔的保管库名称列表作为此函数的参数有助于检索仅与指定保管库相关的备份实例记录。 默认情况下，此参数的值为“*”，这使函数能够跨所有保管库搜索计费组记录。 | N |“vault1,vault2,vault3”|
| VaultTypeList     | 此参数用于筛选与特定保管库类型相关的记录的函数输出。 目前唯一受支持的保管库类型为“Microsoft.RecoveryServices/vaults”，这是此参数的默认值。 | N | “Microsoft.RecoveryServices/vaults”|
| ExcludeLegacyEvent  | 此参数用于选择是否在旧 AzureDiagnostics 表中查询数据。 如果此参数的值为 false，则函数同时查询 AzureDiagnostics 表和特定于资源的表中的数据。 如果此参数的值为 true，则函数仅查询特定于资源的表中的数据。 默认值为 true。 | N | 是 |
| BackupSolutionList | 此参数用于筛选 Azure 环境中使用的一组特定备份解决方案的函数输出。 例如，如果将“Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM”指定为此参数的值，则函数仅返回与使用 Azure 虚拟机备份、Azure VM 备份中的 SQL 或用 DPM 备份到 Azure 备份的项相关的记录。 默认情况下，此参数的值为“*”，这使函数能够返回与备份报表支持的所有备份解决方案相关的记录（支持的值为“Azure Virtual Machine Backup”、“SQL in Azure VM Backup”、“SAP HANA in Azure VM Backup”、“Azure Storage (Azure Files) Backup”、“Azure Backup Agent”、“DPM”、“Azure Backup Server”或以逗号分隔的任意这些值的组合）。 | N | “Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent” |
| BillingGroupName | 此参数用于按名称搜索特定计费组。 默认情况下，此值为“*”，这使函数能够搜索所有计费组。 | N | “testvm” |
| AggregationType | 此参数用于指定应检索数据的时间粒度。 如果此参数的值为“Daily”，则函数将每天返回每个计费组的记录，使你能够分析存储使用量和前端大小的每日趋势。 如果此参数的值为“Weekly”，则函数将每周返回每个备份实例的记录，使你能够分析每周趋势。 同样，可以指定“Monthly”以分析每月趋势。 默认值为“Daily”。 如果在更大的时间范围内查看数据，建议使用“Weekly”或“Monthly”，以提高查询性能并便于趋势分析。 | N | “Weekly” |

**返回的字段**

| **字段名称** | **说明** |
| -------------- | --------------- |
| UniqueId | 用于指示计费组唯一 ID 的主键 |
| FriendlyName | 计费组的友好名称 |
| 名称 | 计费组的名称 |
| 类型 | 计费组的类型。 例如，“ProtectedContainer”或“BackupItem” |
| SourceSizeInMBs | 计费组的前端大小 (MB) |
| VaultStore_StorageConsumptionInMBs | 保管库标准层中计费组使用的云存储总量 |
| BackupSolution | 计费组与之关联的备份解决方案。 例如，“Azure VM Backup”、“SQL in Azure VM Backup”等。 |
| VaultResourceId | 与计费组关联的保管库的 Azure 资源管理器 (ARM) ID |
| VaultUniqueId | 引用与计费组关联的保管库的外键 |
| VaultName | 与计费组关联的保管库的名称 |
| VaultTags | 与计费组关联的保管库的标记 |
| VaultSubscriptionId | 与计费组关联的保管库的订阅 ID |
| VaultLocation | 与计费组关联的保管库的位置 |
| VaultStore_StorageReplicationType | 与计费组关联的保管库的存储复制类型 |
| VaultType | 保管库的类型，即“Microsoft.RecoveryServices/vaults” |
| TimeGenerated | 记录的时间戳 |
| ExtendedProperties | 计费组的其他属性 |

## <a name="sample-queries"></a>示例查询

下面的这些示例查询可以帮助你开始使用系统函数。

- 给定时间范围内所有失败的 Azure VM 备份作业

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- 给定时间范围内的所有 SQL 日志备份作业

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- VM“testvm”使用的备份存储的每周趋势

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>后续步骤
[详细了解备份报告](./configure-reports.md)