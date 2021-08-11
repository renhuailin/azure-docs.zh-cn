---
title: 将 VMware VM 迁移到 Azure（使用无代理方法）- PowerShell
description: 了解如何通过 PowerShell 使用 Azure Migrate 运行 VMware VM 的无代理迁移。
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 05/11/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 018b11d53cf201de41f0f6ff9bc4f1f5c7488d7a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113765325"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>将 VMware VM 迁移到 Azure（使用无代理方法）- PowerShell

在本文中，你将了解如何使用 Azure PowerShell，通过无代理方法，为 [Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)迁移发现的 VMware VM。

学习如何：

> [!div class="checklist"]
> * 在 Azure Migrate 项目中检索发现的 VMware VM。
> * 开始复制 VM。
> * 更新用于复制 VM 的属性。
> * 监视复制。
> * 运行测试迁移，确保一切按预期正常进行。
> * 运行完整的 VM 迁移。

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="1-prerequisites"></a>1.先决条件

开始学习本教程之前，应做好以下准备：

1. 在开始本教程之前完成[教程：使用服务器评估发现 VMware VM](tutorial-discover-vmware.md)，将 Azure 和 VMware 做好迁移准备。
2. 在开始本教程之前完成[教程：评估 VMware VM 以迁移到 Azure VM](./tutorial-assess-vmware-azure-vm.md)，然后再将这些虚拟机迁移到 Azure。
3. [安装 Az PowerShell 模块](/powershell/azure/install-az-ps)

## <a name="2-install-azure-migrate-powershell-module"></a>2.安装 Azure Migrate PowerShell 模块

Azure Migrate PowerShell 模块作为 Azure PowerShell (`Az`) 的一部分提供。 运行 `Get-InstalledModule -Name Az.Migrate` 命令，检查计算机上是否已安装 Azure Migrate PowerShell 模块。  

## <a name="3-sign-in-to-your-microsoft-azure-subscription"></a>3.登录到 Microsoft Azure 订阅

使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 登录到 Azure 订阅。

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-your-azure-subscription"></a>选择 Azure 订阅

使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet 获取你有权访问的 Azure 订阅的列表。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择要用于 Azure Migrate 项目的 Azure 订阅。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="4-retrieve-the-azure-migrate-project"></a>4.检索 Azure Migrate 项目

Azure Migrate 项目用于存储从正在评估或迁移的环境中收集的发现、评估和迁移元数据。
在项目中，你可以跟踪发现的资产、协调评估并执行迁移。

应已创建 Azure Migrate 项目，这是先决条件的一部分。 使用 [Get-AzMigrateProject](/powershell/module/az.migrate/get-azmigrateproject) cmdlet 检索 Azure Migrate 项目的详细信息。 需要指定 Azure Migrate 项目的名称 (`Name`) 和 Azure Migrate 项目的资源组名称 (`ResourceGroupName`)。

```azurepowershell-interactive
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name MyResourceGroup
```
```azurepowershell-interactive
# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name MyMigrateProject -ResourceGroupName $ResourceGroup.ResourceGroupName
```
```azurepowershell-interactive
# View Azure Migrate project details
Write-Output $MigrateProject
```

## <a name="5-retrieve-discovered-vms-in-an-azure-migrate-project"></a>5.在 Azure Migrate 项目中检索发现的 VM

Azure Migrate 使用轻型 [Azure Migrate 设备](migrate-appliance-architecture.md)。 作为先决条件的一部分，应已将 Azure Migrate 设备部署为 VMware VM。

若要在 Azure Migrate 项目中检索特定的 VMware VM，请指定 Azure Migrate 项目的名称 (`ProjectName`)、Azure Migrate 项目的资源组 (`ResourceGroupName`) 和 VM 名称 (`DisplayName`)。


```azurepowershell-interactive
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName MyTestVM | Format-Table DisplayName, Name, Type

# View discovered server details
Write-Output $DiscoveredServer
```

我们将迁移此 VM，作为本教程的一部分内容。

还可以使用 (`ProjectName`) 和 (`ResourceGroupName`) 参数检索 Azure Migrate 项目中的所有 VMware VM。

```azurepowershell-interactive
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```

如果在 Azure Migrate 项目中有多个设备，则可以使用 (`ProjectName`)、(`ResourceGroupName`) 和 (`ApplianceName`) 参数检索使用特定 Azure Migrate 设备发现的所有 VM。

```azurepowershell-interactive
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName MyMigrateAppliance

```

## <a name="6-initialize-replication-infrastructure"></a>6.初始化“复制基础结构”

[Azure Migrate:服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)利用多个 Azure 资源迁移 VM。 “服务器迁移”在与项目相同的资源组中提供以下资源。

- **服务总线**：服务器迁移使用服务总线将复制业务流程消息发送到设备。
- **网关存储帐户**：服务器迁移使用网关存储帐户存储有关所要复制的 VM 的状态信息。
- **日志存储帐户**：Azure Migrate 设备将 VM 的复制日志上传到日志存储帐户。 Azure Migrate 将复制信息应用到副本托管磁盘。
- **Key Vault**：Azure Migrate 设备使用 Key Vault 管理服务总线的连接字符串，以及复制中使用的存储帐户的访问密钥。

在 Azure Migrate 项目中复制第一个 VM 之前，请运行以下命令来预配复制基础结构。 此命令将预配并配置上述资源，以便你可以开始迁移 VMware VM。

> [!NOTE]
> 一个 Azure Migrate 项目只支持迁移到一个 Azure 区域。 运行此脚本后，就不能更改要将 VMware VM 迁移到的目标区域。
> 如果在 Azure Migrate 项目中配置新设备，需要运行 `Initialize-AzMigrateReplicationInfrastructure` 命令。

在本文中，我们将初始化复制基础结构，以便可以将 VM 迁移到 `Central US` 区域。

```azurepowershell-interactive
# Initialize replication infrastructure for the current Migrate project
Initialize-AzMigrateReplicationInfrastructure -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject. Name -Scenario agentlessVMware -TargetRegion "CentralUS" 

```

## <a name="7-replicate-vms"></a>7.复制 VM

完成发现并初始化复制基础结构后，可以开始将 VMware VM 复制到 Azure。 最多可以同时运行 500 个复制。

可以按如下所示指定复制属性。

**参数** | **类型** | **说明**
--- | --- | ---
 目标订阅和资源组 |  必需 | 通过使用 (`TargetResourceGroupId`) 参数提供资源组 ID，指定 VM 应迁移到的订阅和资源组。
 目标虚拟网络和子网 | 必需 | 分别使用 (`TargetNetworkId`) 和 (`TargetSubnetName`) 参数指定 VM 应迁移到的 Azure 虚拟网络的 ID 和子网的名称。
 计算机 ID | 必需 | 指定需要复制和迁移的已发现计算机的 ID。 使用 (`InputObject`) 为复制指定发现的 VM 对象。  
 目标 VM 名称 | 必需 | 使用 (`TargetVMName`) 参数指定要创建的 Azure VM 的名称。 
 目标 VM 大小 | 必需 | 使用 (`TargetVMSize`) 参数指定要用于复制 VM 的 Azure VM 大小。 例如，要将 VM 迁移到 Azure 中的 D2_v2 VM，请将 (`TargetVMSize`) 的值指定为“Standard_D2_v2”。 
 许可证 | 必需 | 若要为活动软件保障或 Windows Server 订阅涵盖的 Windows Server 计算机使用 Azure 混合权益，请将 (`LicenseType`) 参数的值指定为“WindowsServer”。 否则，请将值指定为“NoLicenseType”。 
 OS 磁盘 | 必需 | 指定载有操作系统引导加载程序和安装程序的磁盘的唯一标识符。 要使用的磁盘 ID 是使用 [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) cmdlet 检索到的磁盘的唯一标识符 (UUID) 属性。
 磁盘类型 | 必需 | 指定要创建的负载均衡器的名称。 
 基础结构冗余 | 可选 | 指定基础结构冗余选项，如下所示。 <br/><br/> - 可用性区域 - 将迁移的计算机固定到区域中的特定可用性区域。 使用此选项可跨可用性区域分配形成多节点应用程序层的服务器。 仅当为迁移选择的目标区域支持可用性区域时，此选项才可用。 若要使用可用性区域，请为 (`TargetAvailabilityZone`) 参数指定可用性区域值。 <br/> - 可用性集 - 将迁移的计算机放入可用性集。 若要使用此选项，所选的目标资源组必须具有一个或多个可用性集。 若要使用可用性集，请为 (`TargetAvailabilitySet`) 参数指定可用性集 ID。 
 启动诊断存储帐户 | 可选 | 若要使用启动诊断存储帐户，请为 (`TargetBootDiagnosticStorageAccount`) 参数指定 ID。 <br/> - 用于启动诊断的存储帐户应位于要将 VM 迁移到的同一订阅中。 <br/> - 默认情况下，没有为此参数设置值。 



### <a name="replicate-vms-with-all-disks"></a>使用所有磁盘复制 VM

在本教程中，我们将复制发现的 VM 的所有磁盘，并为 Azure 中的 VM 指定一个新名称。 我们将发现的服务器的第一个磁盘指定为 OS 磁盘，并将所有磁盘作为标准 HDD 进行迁移。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup
```

```azurepowershell-interactive
# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork
```
```azurepowershell-interactive
# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType Standard_LRS -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2
```
```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>使用所选磁盘复制 VM

还可以选择性地复制发现的 VM 的磁盘，方法是使用 [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) cmdlet ，并将其作为 [New-AzMigrateServerReplication](/powershell/module/az.migrate/new-azmigrateserverreplication) cmdlet 中 (`DiskToInclude`) 参数的输入。 还可以使用 [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) cmdlet 为要复制的每个磁盘指定不同的目标磁盘类型。

为 [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) cmdlet 的以下参数指定值。

- **DiskId** - 指定要迁移的磁盘的唯一标识符。 要使用的磁盘 ID 是使用 [Get-AzMigrateDiscoveredServer](/powershell/module/az.migrate/get-azmigratediscoveredserver) cmdlet 检索到的磁盘的唯一标识符 (UUID) 属性。
- **IsOSDisk** - 如果要迁移的磁盘是 VM 的操作系统磁盘，则指定“true”，否则为“false”。
- **DiskType** - 指定要在 Azure 中使用的磁盘类型。

在下面的示例中，我们将只复制发现的 VM 的两个磁盘。 我们将指定 OS 磁盘，并为要复制的每个磁盘使用不同的磁盘类型。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。

```azurepowershell-interactive
# View disk details of the discovered server
Write-Output $DiscoveredServer.Disk
```

```azurepowershell-interactive
# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType StandardSSD_LRS -IsOSDisk true
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType Premium_LRS -IsOSDisk false

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk
```

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup
```

```azurepowershell-interactive
# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork
```

```azurepowershell-interactive
# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2
```

```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="8-monitor-replication"></a>8.监视复制

复制按如下方式进行：

- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 在初始复制期间，会创建一个 VM 快照。 该快照中的磁盘数据将复制到 Azure 中的副本托管磁盘。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。

使用 [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) cmdlet 跟踪复制的状态。



```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM
```
```azurepowershell-interactive
# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

可以跟踪输出中的“迁移状态”和“迁移状态说明”属性 。

- 对于初始复制，“迁移状态”和“迁移状态说明”属性的值将分别为 `InitialSeedingInProgress` 和 `Initial replication` 。
- 对于增量复制，“迁移状态”和“迁移状态说明”属性的值将分别为 `Replicating` 和 `Ready to migrate` 。
- 迁移完成后，“迁移状态”和“迁移状态说明”属性的值将分别为 `Migration succeeded` 和 `Migrated` 。

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

有关复制进度的详细信息，请运行以下 cmdlet。

```azurepowershell-interactive
Write-Output $replicatingserver.ProviderSpecificDetail
```

可以使用输出中的“初始种子设定进度百分比”属性跟踪初始复制进度。

```Output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

复制按如下方式进行：

- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 在初始复制期间，会创建一个 VM 快照。 该快照中的磁盘数据将复制到 Azure 中的副本托管磁盘。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。

## <a name="9-retrieve-the-status-of-a-job"></a>9.检索作业的状态

可以通过使用 [Get-AzMigrateJob](/powershell/module/az.migrate/get-azmigratejob) cmdlet 来监视作业的状态。

```azurepowershell-interactive
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="10-update-properties-of-a-replicating-vm"></a>10.更新复制 VM 的属性

[Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)允许更改“复制 VM”的目标属性，例如名称、大小、资源组、NIC 配置等。

可以为 VM 更新以下属性。


**参数** | **类型** | **说明**
--- | --- | ---
VM 名称 | 可选 | 使用 [`TargetVMName`] 参数指定要创建的 Azure VM 的名称。 
VM 大小 | 可选 | 使用 [`TargetVMSize`] 参数指定要用于复制 VM 的 Azure VM 大小。 例如，要将 VM 迁移到 Azure 中的 D2_v2 VM，请将 [`TargetVMSize`] 的值指定为 `Standard_D2_v2`。
虚拟网络 | 可选 | 使用 [`TargetNetworkId`] 参数指定 VM 应迁移到的 Azure 虚拟网络的 ID。 
资源组 | 可选 | 可以使用 [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping) cmdlet 指定 IC 配置。 然后对象的输入将传递给 [Set-AzMigrateServerReplication](/powershell/module/az.migrate/set-azmigrateserverreplication) cmdlet 中的 [`NicToUpdate`] 参数。 <br/><br/> - 更改 IP 分配 - 若要为 NIC 指定静态 IP，请使用 [`TargetNicIP`] 参数提供要用作 VM 静态 IP 的 IPv4 地址。 若要为 NIC 动态分配 IP，请提供 `auto` 作为 TargetNicIP 参数的值。 <br/> - 为 [`TargetNicSelectionType`] 参数使用值 `Primary`、`Secondary` 或 `DoNotCreate` 来指定 NIC 应该是主要的、次要的还是不在迁移的 VM 上创建。 只能将一个 NIC 指定为 VM 的主要的 NIC。 <br/> - 要使 NIC 成为主要 NIC，还需要指定其他应设为次要的 NIC 或不在迁移的 VM 上创建的 NIC。 <br/> - 若要更改 NIC 的子网，请使用 [`TargetNicSubnet`] 参数指定子网的名称。
网络接口 | 可选 | 使用 [`TargetVMName`] 参数指定要创建的 Azure VM 的名称。 
可用性区域 | 可选 | 若要使用可用性区域，请为 [`TargetAvailabilityZone`] 参数指定可用性区域值。 
可用性集 | 可选 | 若要使用可用性集，请为 [`TargetAvailabilitySet`] 参数指定可用性集 ID。 


[Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。

```azurepowershell-interactive
# List replicating VMs and filter the result for selecting a replicating VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -MachineName MyTestVM
```

```azurepowershell-interactive
# Retrieve all properties of a replicating VM 
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```

在下面的示例中，我们将更新 NIC 配置，方法是将第一个 NIC 作为主要的 NIC 并为其分配一个静态 IP。 我们将为迁移丢弃第二个 NIC，并更新目标 VM 的名称和大小。

```azurepowershell-interactive
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP ###.###.###.### -TargetNicSelectionType Primary
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType DoNotCreate

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2
```
```azurepowershell-interactive
# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize Standard_DS13_v2 -TargetVMName MyMigratedVM -NicToUpdate $NicMapping
```
```azurepowershell-interactive
# Track job status to check for completion
while (($UpdateJob.State -eq 'InProgress') -or ($UpdateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $UpdateJob.State
```



## <a name="11-run-a-test-migration"></a>11.运行测试迁移

增量复制开始后，在开始以 Azure 为目标的完整迁移之前，可以针对 VM 运行测试迁移。 强烈建议在迁移之前，针对每台计算机至少进行一次迁移测试。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。

- 运行测试迁移可检查迁移是否按预期方式工作。 测试迁移不会影响本地计算机，该计算机仍在运行并继续复制。
- 测试迁移通过使用复制的数据创建一个 Azure VM 来模拟迁移（通常是迁移到 Azure 订阅中的非生产 VNet）。
- 可以使用复制的测试 Azure VM 来验证迁移、执行应用测试，并解决完整迁移之前出现的任何问题。

通过使用 [`TestNetworkID`] 参数指定虚拟网络的 ID，来选择要用于测试的 Azure 虚拟网络。

```azurepowershell-interactive
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork
```
```azurepowershell-interactive
# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id
```
```azurepowershell-interactive
# Track job status to check for completion
while (($TestMigrationJob.State -eq 'InProgress') -or ($TestMigrationJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $TestMigrationJob.State
```

测试完成后，请使用 [Start-AzMigrateTestMigrationCleanup](/powershell/module/az.migrate/start-azmigratetestmigrationcleanup) cmdlet 清理测试迁移。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。

```azurepowershell-interactive
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer
```
```azurepowershell-interactive
# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
# Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $CleanupTestMigrationJob.State
```

## <a name="12-migrate-vms"></a>12.迁移 VM

验证测试迁移是否按预期方式工作后，可以使用以下 cmdlet 迁移复制服务器。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。

如果不想关闭源服务器，则不要使用 [`TurnOffSourceServer`] 参数。

```azurepowershell-interactive
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer
```
```azurepowershell-interactive
# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="13-complete-the-migration"></a>13.完成迁移

1. 完成迁移后，停止本地计算机的复制，并使用以下 cmdlet 清除 VM 的复制状态信息。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。

   ```azurepowershell-interactive
   # Stop replication for a migrated server
   $StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer
   ```
   ```azurepowershell-interactive
   # Track job status to check for completion
   while (($StopReplicationJob.State -eq 'InProgress') -or ($StopReplicationJob.State -eq 'NotStarted')){
           #If the job hasn't completed, sleep for 10 seconds before checking the job status again
           sleep 10;
           $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
   }
   # Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
   Write-Output $StopReplicationJob.State
   ```

1. 如果计算机具有 Linux OS，请在迁移的计算机上安装 [Linux](../virtual-machines/extensions/agent-linux.md) 代理。 在迁移期间，我们会自动为 Windows VM 安装 VM 代理。
1. 执行任何迁移后的应用调整，例如更新数据库连接字符串和 Web 服务器配置。
1. 对 Azure 中当前运行的迁移应用程序执行最终的应用程序和迁移验收测试。
1. 将流量交接到已迁移的 Azure VM 实例。
1. 从本地 VM 清单中删除本地 VM。
1. 从本地备份中删除本地 VM。
1. 更新所有内部文档，以显示新的位置和 Azure VM 的 IP 地址。

## <a name="14-post-migration-best-practices"></a>14.迁移后的最佳做法

- 为提高恢复能力，请执行以下操作：
    - 使用 Azure 备份服务备份 Azure VM 以保证数据安全。 [了解详细信息](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 将 Azure VM 复制到次要区域以保证工作负荷运行且持续可用。 [了解详细信息](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- 为提高安全性，请执行以下操作：
    - 使用 [Azure 安全中心 - 适时管理](../security-center/security-center-just-in-time.md)锁定和限制入站流量访问。
    - 使用[网络安全组](../virtual-network/network-security-groups-overview.md)限制流入管理终结点的网络流量。
    - 部署[Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)以帮助保护磁盘，并保护数据以防被盗和未经授权的访问。
    - 详细了解[保护 IaaS 资源的安全](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，并访问[Azure 安全中心](https://azure.microsoft.com/services/security-center/)。
- 为了便于监视和管理，请执行以下操作：
-  考虑部署[Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)以监视资源使用率和支出。
