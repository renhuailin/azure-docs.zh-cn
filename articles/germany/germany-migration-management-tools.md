---
title: 将 Azure 管理工具从 Azure 德国迁移到全球 Azure
description: 本文介绍如何将 Azure 管理工具从 Azure 德国迁移到全球 Azure。
ms.topic: article
ms.date: 06/22/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurecli
ms.openlocfilehash: 157dded56e3323ed6389f84bd173ba435e8bdc5b
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "117029001"
---
# <a name="migrate-management-tool-resources-to-global-azure"></a>将管理工具资源迁移到全球 Azure

[!INCLUDE [closure info](../../includes/germany-closure-info.md)]

本文中的信息可帮助你将 Azure 管理工具从 Azure 德国迁移到全球 Azure。

## <a name="traffic-manager"></a>流量管理器

Azure 流量管理器可帮助你顺利完成迁移。 但是，不能将在 Azure 德国创建的流量管理器配置文件迁移到全球 Azure。 （在迁移过程中，你会将流量管理器终结点迁移到目标环境，因此无论如何都需要更新流量管理器配置文件。）

当流量管理器仍在源环境中运行时，你可以使用它在目标环境中定义其他终结点。 当流量管理器在新环境中运行时，你仍然可以定义尚未在源环境中迁移的终结点。 此方案称为[蓝绿方案](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)。 此方案涉及以下步骤：

1. 在全球 Azure 中创建新的流量管理器配置文件。
1. 在 Azure 德国定义终结点。
1. 将 DNS CNAME 记录更改为新的流量管理器配置文件。
1. 关闭旧的流量管理器配置文件。
1. 迁移和配置终结点。 对于 Azure 德国的每个终结点：
   1. 将终结点迁移到全球 Azure。
   1. 更改流量管理器配置文件以使用新的终结点。

参考信息：

- 通过完成[流量管理器教程](../traffic-manager/index.yml)，学习更多新知识。
- 查看[流量管理器概述](../traffic-manager/traffic-manager-overview.md)。
- 了解如何[创建流量管理器配置文件](../traffic-manager/quickstart-create-traffic-manager-profile.md)。
- 阅读[蓝绿方案](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)。

## <a name="azure-backup"></a>Azure 备份

Azure 备份服务提供简单、安全且经济高效的解决方案来备份数据，并从 Microsoft Azure 云恢复数据。 现在可以通过 PowerShell cmdlet 将备份数据从德国中部 (GEC) 和德国东北部 (GNE) 移至德国中西部 (GWC)。

### <a name="prerequisite-for-moving-hybrid-workloads"></a>移动混合工作负载的先决条件

移动操作开始后，现有保管库中的备份将停止。 因此，在开始从区域移动备份数据之前，务必要在 GWC 的新保管库中保护混合工作负载(Data Protection Manager (DPM) 服务器/Azure 备份服务器 (MABS)/Microsoft Azure 恢复服务 (MARS) 的数据。
若要开始在新保管库中保护数据，请执行以下操作：

1. 在 GWC 创建一个新的保管库 (VaultN)。
1. 将 DPM 服务器/MABS/MARS 代理重新注册到 VaultN。
1. 分配策略并开始备份。

初始备份是完整副本，之后是增量备份。

>[!Important]
>- 在启动备份数据移动操作之前，请确保已完成 VaultN 中的第一次完整备份。
>- 对于 DPM/MABS，请将原始保管库中的密码保存在一个安全位置，因为你需要相同的密码从目标保管库还原数据。 如果没有原始密码，则无法从源保管库还原。

### <a name="step-1-download-the-resources"></a>步骤 1：下载资源

下载并安装所需的资源。

1. [下载](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.3)最新版本的 PowerShell (PowerShell 7)。
1. 使用 Azure Cloud Shell 中提供的 Az.RecoveryServices 模块版本 4.2.0。
1. 将所有 MARS 代理[更新](https://aka.ms/azurebackup_agent)到最新版本。
1. 验证密码。 如果需要重新生成密码，请按照[验证步骤](https://support.microsoft.com/topic/mandatory-update-for-azure-backup-for-microsoft-azure-recovery-services-agent-411e371c-aace-e134-de6b-bf9fda48026e#section-3)进行操作。

### <a name="step-2-create-a-target-vault-in-gwc"></a>步骤 2：在 GWC 创建目标保管库

在 GWC 创建目标保管库（保管库 2）。 若要了解如何创建保管库，请参阅[创建和配置恢复服务保管库](../backup/backup-create-rs-vault.md)。

>[!Note]
>- 确保保管库没有受保护的项。
>- 确保目标保管库具有必要的冗余 - 本地冗余存储 (LRS) 或异地冗余存储 (GRS)。

### <a name="step-3---use-powershell-to-trigger-backup-data-move"></a>步骤 3：- 使用 PowerShell 触发备份数据移动

#### <a name="get-the-source-vault-from-gne-or-gec"></a>从 GNE 或 GEC 获取源保管库

运行以下 cmdlet：

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$srcVault = Get-AzRecoveryServicesVault -name “srcVault” -ResourceGroupName “TestSourceRG”`

>[!Note]
>- `srcVault` = 源保管库
>- `TestSourceRG` = 源资源组

#### <a name="get-the-target-vault-in-gwc"></a>获取 GWC 的目标保管库

运行以下 cmdlet：

1. `Connect-AzAccount`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$trgVault = Get-AzRecoveryServicesVault -name “targetVault” -ResourceGroupName “TestTargetRG”`

>[!Note]
>- `targetVault` = 目标保管库
>- `TestTargetRG` = 测试资源组

#### <a name="perform-validation"></a>执行验证
 
运行以下 cmdlet：

1. `$validated = $false`
1. `$validated = Test-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault`

#### <a name="initializeprepare-ds-move"></a>初始化/准备 DS 移动

运行以下 cmdlet：

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -SubscriptionName $srcSub`
1. ```azurepowershell
   if($validated) {
           $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault
                       }
   ```
1. `$corr`

#### <a name="trigger-ds-move"></a>触发 DS 移动

运行以下 cmdlet：

1. `Connect-AzAccount`
1. `Set-AzContext -SubscriptionName $trgSub`
1. `Copy-AzRecoveryServicesVault - CorrelationIdForDataMove $corr -SourceVault $srcVault -TargetVault $trgVault -Force`

可以使用 `Get-AzRecoveryServicesBackupJob` cmdlet 监视该操作。

>[!Note] 
>- 在备份数据移动操作期间，所有备份项都将转变为临时状态。 在此状态下，不会创建新的恢复点 (RP)，也不会清理旧 RP。
>- 由于 GEC 和 GNE 启用了此功能，因此建议对小型保管库执行这些步骤并验证移动操作。 成功后，对所有保管库执行这些步骤。
>- 除了为整个保管库触发备份数据移动外，移动发生在每个容器（VM、DPM 和 MABS 服务器以及 MARS 代理）中。 在“作业”部分中跟踪每个容器的移动进度。 

 ![监视移动作业的进度。](./media/germany-migration-management-tools/track-move-jobs.png)

在移动操作期间，源保管库上会阻止以下操作：

- 新的计划备份
- 停止备份并删除数据。
- 删除数据
- 恢复备份
- 修改策略

### <a name="step-4-check-the-status-of-the-move-job"></a>步骤 4：检查移动作业的状态

备份数据移动操作发生在每个容器中。 对于 Azure VM 备份，VM 备份被视为容器。 为了指示备份数据移动操作的进度，系统为每个容器创建了一个作业。

若要监视作业，请运行以下 cmdlet：

1. `Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `$Jobs = Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `Get-AzRecoveryServicesBackupJobDetail -Job $Jobs[0] -VaultId $trgVault.ID`
1. `$JobDetails.ErrorDetails`

### <a name="step-5-post-move-operations"></a>步骤 5：移动后操作

将所有容器的备份数据移动到目标保管库的操作完成后，就不需要对 VM 备份执行进一步操作。



#### <a name="verify-the-movement-of-containers-is-complete"></a>验证是否已完成容器的移动

若要检查源保管库中的所有容器是否已移至目标保管库，请转到目标保管库并检查该保管库中的所有容器。

运行以下 cmdlet，列出从源保管库移动到目标保管库的所有 VM：

```azurepowershell
Get-AzRecoveryServicesBackupContainer -BackupManagementType “AzureVM” -VaultId $trgVault.ID
```

#### <a name="verify-the-movement-of-policies-is-complete"></a>验证是否已完成策略的移动

备份数据成功移动到新区域后，所有应用于源保管库中 Azure VM 备份项的策略都将应用于目标保管库。

若要验证所有策略是否已从源保管库移至目标保管库，请转到目标保管库并运行以下 cmdlet，获取所有已移动策略的列表：

```azurepowershell
Get-AzRecoveryServicesBackupProtectionPolicy -VaultId $trgVault.ID
```

这些策略在移动操作后继续应用于备份数据，以便继续对移动后的恢复点进行生命周期管理。

为避免突然清理多个恢复点（可能在移动过程中过期或在移动过程后立即过期），旧恢复点 (RP) 的清理将在移动后暂停 10 天。 在此期间，你无需为旧 RP 产生的额外数据付费。

>[!Important]
>如果需要从这些旧 RP 进行恢复，请在备份数据移动后的这 10 天内立即恢复它们。 一旦此安全期结束，应用于每个备份项的策略就会生效，并强制清理旧的 RP。

#### <a name="restore-operations"></a>还原操作

**还原 Azure 虚拟机**

对于 Azure 虚拟机，可以从目标保管库中的恢复点进行还原。

#### <a name="configure-mars-agent"></a>配置 MARS 代理

1. 重新注册到目标保管库。
1. 从恢复点还原。
1. 将 Post Recovery 重新注册到新保管库 (VaultN) 并恢复备份。

>[!Note]
>当 MARS 代理注册到目标保管库时，不会进行新的备份。

#### <a name="configure-dpmmabs"></a>配置 DPM/MABS

推荐

使用外部 DPM 方法执行还原。 有关详细信息，请参阅[从 Azure 备份服务器恢复数据](../backup/backup-azure-alternate-dpm-server.md)。

>[!Note]
>- 不支持原始位置恢复 (OLR)。
>- 对于所有已注册的计算机，备份将继续在 VaultN 中进行。

**其他选项**

对于原始位置恢复 (OLR)：

1. 将 DPM 服务器/MABS 重新注册到目标保管库。
1. 执行还原操作。
1. 将 DPM 服务器/MABS 重新注册回新的保管库。

>[!Note]
>DPM 的使用限制： <br><br> <ul><li>将 DPM 服务器连接到目标保管库时，所有注册到 DPM 服务器的计算机的备份操作都会停止。</li><li>在还原后将 DPM 服务器重新注册到新保管库后，将在恢复备份之前进行一致性检查（完成相同检查所需的时间取决于数据量）。</li></ul>

### <a name="error-codes"></a>错误代码

#### <a name="usererrorconflictingdatamoveonvault"></a>UserErrorConflictingDataMoveOnVault 

**消息：** 保管库上当前正在运行另一个数据移动操作。 

**场景：** 你正在源保管库上尝试数据移动操作，而其他数据移动操作已在同一源保管库上运行。

**建议的操作：** 等待当前数据移动操作完成，然后重试。

#### <a name="usererroroperationnotallowedduringdatamove"></a>UserErrorOperationNotAllowedDuringDataMove

**消息：** 不允许执行此操作，因为正在执行数据移动操作。 

**场景：** 当数据移动操作正在进行时，源保管库中不允许进行以下操作：
 
- 停止备份并保留数据 
- 停止备份并删除数据。 
- 删除备份数据。 
- 恢复备份 
- 修改策略。

**建议的操作：** 等待数据移动操作完成，然后重试。 [详细了解](#azure-backup)支持的操作。

#### <a name="usererrornocontainersfoundfordatamove"></a>UserErrorNoContainersFoundForDataMove 

**消息：** 此保管库中没有支持数据移动操作的容器。 

**场景：** 此消息在以下情况下显示：

- 源保管库没有任何容器。 
- 源保管库只有不受支持的容器。 
- 源保管库包含之前移动到某个目标保管库的所有容器，并且你已在 API 中传递了 IgnoreMoved = true。

**建议的操作：** [了解](#azure-backup)支持数据移动的容器。

#### <a name="usererrordatamovenotsupportedatcontainerlevel"></a>UserErrorDataMoveNotSupportedAtContainerLevel 

**消息：** 容器级别不支持数据移动操作。 

**场景：** 你选择了容器级别的数据移动操作。 

**建议的操作：** 尝试执行保管库级别的数据移动操作。

### <a name="usererrordatamovenotallowedcontainer-registrationinprogress"></a>UserErrorDataMoveNotAllowedContainer RegistrationInProgress 

**消息：** 不允许执行数据移动操作，因为源保管库中正在运行容器注册操作。 

**场景：** 当你尝试移动数据时，源保管库中正在进行容器注册操作。 

**建议的操作：** 一段时间后尝试数据移动操作。

#### <a name="usererrordatamovenotallowedtargetvaultnotempty"></a>UserErrorDataMoveNotAllowedTargetVaultNotEmpty 

**消息：** 不允许执行数据移动操作，因为目标保管库已经注册了一些容器。 

**场景：** 所选的目标保管库已经注册了一些容器。 

**建议的操作：** 在空的目标保管库上尝试数据移动操作。

#### <a name="usererrorunsupportedsourceregionfordatamove"></a>UserErrorUnsupportedSourceRegionForDataMove 

**消息：** 此区域不支持数据移动操作。 

**场景：** 源区域无效。

**建议的操作：** 检查[支持数据移动的区域列表](#azure-backup)。

#### <a name="usererrorunsupportedtargetregionfordatamove"></a>UserErrorUnsupportedTargetRegionForDataMove 

**消息：** 不支持将数据移动到此区域。

**场景：** 目标区域 ID 无效。 

**建议的操作：** 检查[支持数据移动的区域列表](#azure-backup)。


#### <a name="usererrordatamovetargetvaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveTargetVaultWithPrivate EndpointNotSupported 

**消息：** 无法移动数据，因为选定的目标保管库具有专用终结点。 

**场景：** 目标保管库中启用了专用终结点。 

**建议的操作：** 删除专用终结点并重试移动操作。 [详细了解](#azure-backup)支持的操作。

### <a name="usererrordatamovesourcevaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveSourceVaultWithPrivate EndpointNotSupported 

**消息：** 无法移动数据，因为选定的源保管库具有专用终结点。

**场景：** 源保管库中启用了专用终结点。

**建议的操作：** 删除专用终结点并重试移动操作。 [详细了解](../backup/private-endpoints.md#deleting-private-endpoints)支持的操作。

#### <a name="usererrordatamovesourcevaultwithcmk-notsupported"></a>UserErrorDataMoveSourceVaultWithCMK NotSupported 

**消息：** 无法移动数据，因为选定的源保管库启用了加密。 

**场景：** 源保管库中启用了客户管理的密钥 (CMK)。

**建议的操作：** [了解](#azure-backup)支持的操作。

#### <a name="usererrordatamovetargetvaultwithcmknotsupported"></a>UserErrorDataMoveTargetVaultWithCMKNotSupported 

**消息：** 无法移动数据，因为选定的目标保管库启用了加密。 

**场景：** 目标保管库中启用了客户管理的密钥 (CMK)

**建议的操作：** [了解](#azure-backup)支持的操作。

## <a name="scheduler"></a>计划程序

Azure 计划程序即将停用。 若要创建计划作业，可以改用全球 Azure 中的 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。

参考信息：

- 通过完成 [Azure 逻辑应用教程](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)了解更多信息。
- 查看 [Azure 逻辑应用概述](../logic-apps/logic-apps-overview.md)。

## <a name="network-watcher"></a>网络观察程序

目前不支持将 Azure 网络观察程序实例从 Azure 德国迁移到全球 Azure。 建议在全球 Azure 中创建和配置新的网络观察程序实例。 然后，比较新旧环境之间的结果。 

参考信息：

- 通过完成[网络观察程序教程](../network-watcher/index.yml)，学习更多新知识。
- 查看[网络观察程序概述](../network-watcher/network-watcher-monitoring-overview.md)。
- 了解[网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md)。
- 阅读[连接监视器](../network-watcher/connection-monitor.md)。

## <a name="site-recovery"></a>Site Recovery

你无法将当前的 Azure Site Recovery 设置迁移到全球 Azure。 必须在全球 Azure 中设置新的 Site Recovery 解决方案。

有关 Site Recovery 的详细信息，以及了解如何将 VM 从 Azure 德国迁移到全球 Azure，请参阅[如何使用 Site Recovery](./germany-migration-compute.md#compute-iaas)。

通过完成以下分步教程，学习更多新知识：

- [Azure 到 Azure 的灾难恢复](../site-recovery/azure-to-azure-about-networking.md)
- [VMware 到 Azure 的灾难恢复](../site-recovery/site-recovery-deployment-planner.md)
- [Hyper-V 到 Azure 的灾难恢复](../site-recovery/hyper-v-deployment-planner-overview.md)

## <a name="azure-policies"></a>Azure 策略

不能直接将策略从 Azure 德国迁移到全球 Azure。 在迁移过程中，已分配策略的范围通常会发生变化。 当目标环境中的订阅不同时（就像在此方案中一样）更是如此。 但是，你可以保留策略定义并在全球 Azure 中重复使用它们。

在 Azure CLI 中运行以下命令，以列出当前环境中的所有策略。

> [!NOTE]
> 在运行以下命令之前，请务必在 Azure CLI 中切换到 AzureGermanCloud 环境。


```azurecli
az policy definition list --query '[].{Type:policyType,Name:name}' --output table
```

仅导出“PolicyType”值为“自定义”的策略。 将 policyRule 导出到文件。 以下示例将自定义策略“仅允许德国中部”（简短版本：`allowgconly`）导出到当前文件夹中的文件： 

```azurecli
az policy definition show --name allowgconly --output json --query policyRule > policy.json
```

导出文件将如以下示例所示：

```json
{
  "if": {
    "not": {
      "equals": "germanycentral",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

接下来，切换到全球 Azure 环境。 通过编辑文件来修改策略规则。 例如，将 `germanycentral` 更改为 `westeurope`。

```json
{
  "if": {
    "not": {
      "equals": "westeurope",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

创建新策略：

```azurecli
cat policy.json |az policy definition create --name "allowweonly" --rules @-
```

你现在有一个名为 `allowweonly` 的新策略。 该策略仅允许将西欧作为区域。

根据需要将该策略分配给新环境中的范围。 可以通过运行以下命令来记录 Azure 德国的旧分配：

```azurecli
az policy assignment list
```

参考信息：

- 通过完成 [Azure 策略教程](../governance/policy/tutorials/create-and-manage.md)，学习更多新知识。
- 了解如何[使用 Azure CLI 查看策略](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-azure-cli)或[使用 PowerShell 查看策略](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-powershell)。
- 了解如何[使用 Azure CLI 创建策略定义](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-azure-cli)或[使用 PowerShell 创建策略定义](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-powershell)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="where-can-i-move-the-backup-data"></a>我可以将备份数据移动到哪里？

你可以将备份数据从位于德国中部 (GEC) 和德国东北部 (GNE) 的恢复服务保管库 (RSV) 移动到德国中西部 (GWC)。

### <a name="what-backup-data-can-i-move"></a>我可以移动哪些备份数据？

从 2021 年 6 月 21 日起，你可以将以下工作负载的备份数据从一个区域移动到另一个区域：

- Azure 虚拟机
- 混合工作负载
- 使用 Microsoft Azure 恢复服务 (MARS) 代理的文件/文件夹备份
- Data Protection Manager (DPM) 服务器
- Azure 备份服务器 (MABS)

### <a name="how-can-i-move-backup-data-to-another-region"></a>如何将备份数据移动到另一个区域？

为了确保现有区域中的数据不会丢失，Azure 备份启用了从 GEC 和 GNE 到 GWC 的备份数据移动。

进行迁移时，将在 GEC 和 GNE 停止备份。 因此，在开始迁移操作之前，必须保护新区域中的工作负载。

### <a name="what-to-do-if-the-backup-data-move-operation-fails"></a>如果备份数据移动操作失败怎么办？

由于以下错误情况，备份数据移动操作可能会失败：

| 错误消息    | 原因 |
| --- | --- |
| 请提供一个空的目标保管库。 目标保管库不应有任何备份项或备份容器。 | 你选择的目标保管库已经有一些受保护的项。 |
| 只允许将 Azure 备份数据移动到受支持的目标区域。 | 你选择的目标保管库来自不支持移动的区域。 |

你需要通过使用新的空目标保管库运行相同的命令（如下所示），从头开始重试备份，或者可以通过用标志指示来重试并从源保管库中移动失败的项。

```azurepowershell
   if($validated) {
                              $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault -RetryOnlyFailed
                      }
```

### <a name="is-there-a-cost-involved-in-moving-this-backup-data"></a>移动此备份数据是否需要付费？

否。 将备份数据从一个区域移动到另一个区域不会产生额外费用。 Azure 备份承担跨区域移动数据的费用。 移动操作完成后，只有 10 天的无计费周期。 在此期限过后，将在目标保管库中开始计费。

### <a name="if-i-face-issues-in-moving-backup-data-whom-should-i-contact"></a>如果在移动备份数据时遇到问题，我应该联系谁？

对于将备份数据从 GEC 或 GNE 移动到 GWC 时出现的任何问题，请发送电子邮件到 [GESupportAzBackup@microsoft.com](mailto:GESupportAzBackup@microsoft.com)。

## <a name="next-steps"></a>后续步骤

了解有关迁移以下服务类别中的资源的工具、技术和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [介质](./germany-migration-media.md)
