---
title: 使用 PowerShell 备份 Azure 文件共享
description: 本文介绍如何使用 Azure 备份服务和 PowerShell 备份 Azure 文件存储的文件共享。
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 948931764769bc967b88e7942b7e8384b0f93dff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87077004"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>使用 PowerShell 备份 Azure 文件共享

本文介绍如何通过 [Azure 备份](backup-overview.md)恢复服务保管库，使用 Azure PowerShell 来备份 Azure 文件存储的文件共享。

本文介绍如何执行以下操作：

> [!div class="checklist"]
>
> * 设置 PowerShell 并注册恢复服务提供程序。
> * 创建恢复服务保管库。
> * 配置 Azure 文件共享的备份。
> * 运行备份作业。

## <a name="before-you-start"></a>开始之前

* [详细了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
* 查看 Azure 库中的 Az.RecoveryServices [cmdlet 参考](/powershell/module/az.recoveryservices)。
* 查看恢复服务的 PowerShell 对象层次结构：

  ![恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>设置 PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

按如下所述设置 PowerShell：

1. [下载最新版的 Azure PowerShell](/powershell/azure/install-az-ps)。

    > [!NOTE]
    > 备份 Azure 文件共享所需的最低 PowerShell 版本为 Az.RecoveryServices 2.6.0。 使用最新版本或允许的最低版本有助于避免现有脚本出现问题。 使用以下 PowerShell 命令安装最低版本：
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. 使用以下命令查找适用于 Azure 备份的 PowerShell cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 查看 Azure 备份、Azure Site Recovery 和恢复服务保管库的别名和 cmdlet。 下面是可能显示的内容示例。 此 cmdlet 列表并不完整。

    ![恢复服务 cmdlet 列表](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用“Connect-AzAccount”登录到 Azure 帐户。
5. 在出现的网页上，系统会提示输入帐户凭据。

    或者，可以使用 -Credential 参数，将帐户凭据作为参数包含在 Connect-AzAccount cmdlet 中 。

    如果你是代表租户的 CSP 合作伙伴，则需将客户指定为一名租户。 使用其租户 ID 或租户主域名。 例如，Connect-AzAccount -Tenant "fabrikam.com"。

6. 由于一个帐户可以有多个订阅，因此请将要使用的订阅与帐户关联在一起：

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 首次使用 Azure 备份时，请使用 Register-AzResourceProvider cmdlet 将 Azure 恢复服务提供程序注册到订阅中：

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 验证提供程序是否已成功注册：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令输出中，确认 **RegistrationState** 是否更改为 **Registered**。 如果不是，请再次运行 **Register-AzResourceProvider** cmdlet。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是一种资源管理器资源，因此必须将其放在资源组中。 可以使用现有资源组，也可以使用 New-AzResourceGroup cmdlet 创建资源组。 创建资源组时，请指定资源组的名称和位置。

请按照以下步骤创建恢复服务保管库：

1. 如果没有现有的资源组，请使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 新建一个。 此示例是在美国西部区域创建一个新的资源组：

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. 使用 [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) cmdlet 创建保管库。 请为保管库指定与资源组相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>在订阅中查看保管库

若要查看订阅中的所有保管库，请使用 [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)：

```powershell
Get-AzRecoveryServicesVault
```

输出如下所示。 请注意，输出提供了关联的资源组和位置。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>设置保管库上下文

将保管库对象存储在变量中，并设置保管库上下文。

许多 Azure 备份 cmdlet 要求将恢复服务保管库对象用作输入，因此可以方便地在变量中存储保管库对象。

保管库上下文是在保管库中受保护的数据的类型。 使用 [Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) 来设置。 设置上下文后，它将应用于所有后续 cmdlet。

以下示例为 testvault 设置保管库上下文：

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取保管库 ID

我们计划根据 Azure PowerShell 指南弃用保管库上下文的设置。 可以改为存储或提取保管库 ID，并将其传递给相关命令。 因此，如果你尚未设置保管库上下文或想要指定为某个保管库运行的命令，请将保管库 ID 作为 `-vaultID` 传递给所有相关命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>配置备份策略

备份策略指定了备份计划，以及备份恢复点的保留期限。

一个备份策略至少与一个保留策略相关联。 保留策略定义了在将恢复点删除之前将其保留的时限。 可以为备份配置为每日、每周、每月或每年保留。

下面是一些备份策略的 cmdlet：

* 使用 [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) 查看默认的备份策略保留。
* 使用 [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) 查看默认的备份策略计划。
* 使用 [AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)创建新的备份策略。 输入计划和保留策略对象作为输入。

默认情况下，计划策略对象中将定义开始时间。 请使用以下示例将开始时间更改为所需的开始时间。 所需的开始时间应为协调世界时 (UTC)。 下面的示例假设在进行每日备份时，开始时间为 UTC 时间凌晨 1:00。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 只能将开始时间设为 30 分钟的倍数时间。 在上面的示例中，开始时间只能是“01:00:00”或“02:30:00”。 开始时间不能为“01:15:00”。

以下示例将计划策略和保留策略存储在变量中。 然后，它使用这些变量作为新策略 (**NewAFSPolicy**) 的参数。 “NewAFSPolicy”进行每日备份，并将其保留 30 天。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

输出与下面类似：

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>启用备份

定义备份策略后，可以使用该策略为 Azure 文件共享启用保护。

### <a name="retrieve-a-backup-policy"></a>检索备份策略

使用 [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) 提取相关的策略对象。 请使用此 cmdlet 查看与某个工作负载类型关联的策略，或者获取特定策略。

#### <a name="retrieve-a-policy-for-a-workload-type"></a>检索针对某个工作负荷类型的策略

以下示例检索工作负载类型为 **AzureFiles** 的策略：

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

输出与下面类似：

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell 中“BackupTime”字段的时区是 UTC。 在 Azure 门户中显示备份时间时，该时间会根据本地时区进行调整。

#### <a name="retrieve-a-specific-policy"></a>检索特定的策略

以下策略检索名为“dailyafs”的备份策略：

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>启用保护并应用策略

使用 [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 启用保护。 将策略与保管库关联后，系统会根据策略计划触发备份。

以下示例使用策略“dailyafs”，为存储帐户“testStorageAcct”中的 Azure 文件共享“testAzureFileShare” 启用保护：

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

此命令将等待，直到完成配置保护作业，并提供类似于以下示例的输出：

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

有关如何获取存储帐户的文件共享列表的详细信息，请参阅[此文](/powershell/module/az.storage/get-azstorageshare)。

## <a name="important-notice-backup-item-identification"></a>重要说明：备份项标识

本部分概述了对 Azure 文件共享的备份的重要更改，以便为正式发布做准备。

为 Azure 文件共享启用备份时，用户为客户提供了一个文件共享名称作为实体名称，并且创建了一个备份项。 备份项的名称是 Azure 备份服务创建的唯一标识符。 标识符通常是易记名称。 但是，若要处理软删除的情况，可以删除文件共享，并使用同一名称创建另一个文件共享，Azure 文件共享的唯一标识现在为 ID。

若要了解每个项的唯一 ID，请运行带有适用于“backupManagementType”和“WorkloadType”的相关筛选器的“Get-AzRecoveryServicesBackupItem”命令，以获取所有相关项。 然后查看返回的 PowerShell 对象/响应中的名称字段。

建议将项列出，然后从响应的“名称”字段中检索其唯一名称。 通过此值可以筛选参数为“名称”的项。 否则，请使用参数“FriendlyName”检索具有其 ID 的项。

> [!IMPORTANT]
> 请确保 PowerShell 升级到 Azure 文件共享备份的最低版本 (Az.RecoveryServices 2.6.0)。 在此版本中，FriendlyName 筛选器可用于 **Get-AzRecoveryServicesBackupItem** 命令。
>
> 将 Azure 文件共享的名称传递给“FriendlyName”参数。 如果将文件共享的名称传递给“Name”参数，此版本将引发警告以将名称传递到“FriendlyName”参数。
>
> 如果未安装最低版本，则可能导致现有脚本失败。 使用以下 PowerShell 命令安装最低版本：
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>触发按需备份

使用 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) 为受保护的 Azure 文件共享运行按需备份：

1. 使用 [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)，从用于保存备份数据的保管库的容器中检索存储帐户。
2. 如需开始备份作业，请使用 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) 获取有关 Azure 文件共享的信息。
3. 使用 [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) 运行按需备份。

按如下所示运行按需备份：

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

该命令返回一个带有 ID 的作业需要跟踪，如以下示例所示：

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

在备份时使用 Azure 文件共享快照。 通常，该作业在命令返回此输出时完成。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 门户中备份 Azure 文件存储](backup-afs.md)。
* 请参阅 [GitHub 上的示例脚本](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup)，使用 Azure 自动化 Runbook 来计划备份。
