---
title: 将 VMware VM 迁移到 Azure（使用无代理方法）- PowerShell
description: 了解如何通过 PowerShell 使用 Azure Migrate 运行 VMware VM 的无代理迁移。
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 10/1/2020
ms.author: rahugup
ms.openlocfilehash: 3cd8d29a498a6a00fa8fff679afc969b339934b1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494335"
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

## <a name="prerequisites"></a>先决条件

开始学习本教程之前，应做好以下准备：

1. [完成发现教程](tutorial-discover-vmware.md)来准备 Azure 和 VMware，以便进行迁移。
2. 建议先完成第二篇教程以[评估 VMware VM](./tutorial-assess-vmware-azure-vm.md)，然后再将它们迁移到 Azure。
3. 设置 Azure PowerShell `Az` 模块。 如需安装或升级 Azure PowerShell，请遵循此 [Azure PowerShell 安装和配置指南](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>安装 Azure Migrate PowerShell 模块

Azure Migrate PowerShell 模块以预览版提供。 需要使用以下命令安装 PowerShell 模块。 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>登录到 Microsoft Azure 订阅

使用 `Connect-AzAccount` cmdlet 登录到你的 Azure 订阅。

```azurepowershell
Connect-AzAccount
```

选择 Azure 订阅。 使用 `Get-AzSubscription` cmdlet 获取你有权访问的 Azure 订阅的列表。 使用 `Set-AzContext` cmdlet 选择要用于 Azure Migrate 项目的 Azure 订阅。

```azurepowershell
Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
```

## <a name="retrieve-the-azure-migrate-project"></a>检索 Azure Migrate 项目

Azure Migrate 项目用于存储从正在评估或迁移的环境中收集的发现、评估和迁移元数据。
在项目中，你可以跟踪发现的资产、协调评估并执行迁移。

应已创建 Azure Migrate 项目，这是先决条件的一部分。 使用 `Get-AzMigrateProject` cmdlet 检索 Azure Migrate 项目的详细信息。 需要指定 Azure Migrate 项目的名称 (`Name`) 和 Azure Migrate 项目的资源组名称 (`ResourceGroupName`)。

```azurepowershell
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name "MyResourceGroup"

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name "MyMigrateProject" -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
$MigrateProject | ConvertTo-JSON
```

## <a name="retrieve-discovered-vms-in-an-azure-migrate-project"></a>在 Azure Migrate 项目中检索发现的 VM

Azure Migrate 使用轻型 [Azure Migrate 设备](migrate-appliance-architecture.md)。 作为先决条件的一部分，应已将 Azure Migrate 设备部署为 VMware VM。

若要在 Azure Migrate 项目中检索特定的 VMware VM，请指定 Azure Migrate 项目的名称 (`ProjectName`)、Azure Migrate 项目的资源组 (`ResourceGroupName`) 和 VM 名称 (`DisplayName`)。 

> [!NOTE]
> VM 名称 (`DisplayName`) 参数值区分大小写。

```azurepowershell
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName "MyTestVM"

# View discovered server details
$DiscoveredServer | ConvertTo-JSON
```
我们将迁移此 VM，作为本教程的一部分内容。

还可以使用 `ProjectName` 和 `ResourceGroupName` 参数检索 Azure Migrate 项目中的所有 VMware VM。

```azurepowershell
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName 
```
如果在 Azure Migrate 项目中有多个设备，则可以使用 `ProjectName`、`ResourceGroupName` 和 `ApplianceName` 参数检索使用特定 Azure Migrate 设备发现的所有 VM。 

```azurepowershell
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName "MyMigrateAppliance" |Format-Table DisplayName, Name, Type

```

## <a name="initialize-replication-infrastructure"></a>初始化“复制基础结构”

[Azure Migrate:服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)利用多个 Azure 资源迁移 VM。 “服务器迁移”在与项目相同的资源组中提供以下资源。

- **服务总线**：服务器迁移使用服务总线将复制业务流程消息发送到设备。
- **网关存储帐户**：服务器迁移使用网关存储帐户存储有关所要复制的 VM 的状态信息。
- **日志存储帐户**：Azure Migrate 设备将 VM 的复制日志上传到日志存储帐户。 Azure Migrate 将复制信息应用到副本托管磁盘。
- **Key Vault**：Azure Migrate 设备使用 Key Vault 管理服务总线的连接字符串，以及复制中使用的存储帐户的访问密钥。

在复制 Azure Migrate 项目中的第一个 VM 之前，请运行以下脚本来预配复制基础结构。 此脚本预配并配置上述资源，以便你可以开始迁移 VMware VM。

> [!NOTE]
> 一个 Azure Migrate 项目只支持迁移到一个 Azure 区域。 运行此脚本后，就不能更改要将 VMware VM 迁移到的目标区域。
> 如果在 Azure Migrate 项目中配置新设备，需要运行 `Initialize-AzMigrateReplicationInfrastructure` 脚本。 

在本文中，我们将初始化复制基础结构，以便可以将 VM 迁移到 `Central US` 区域。 可以从 GitHub 存储库[下载文件](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles)，或者使用以下代码段运行它。 

```azurepowershell
# Download the script from Azure Migrate GitHub repository 
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion "CentralUS" 
```


## <a name="replicate-vms"></a>复制 VM

完成发现并初始化复制基础结构后，可以开始将 VMware VM 复制到 Azure。 最多可以同时运行 300 个复制。

可以按如下所示指定复制属性。

- **目标订阅和资源组** - 通过使用 `TargetResourceGroupId` 参数提供资源组 ID，指定 VM 应该迁移到的订阅和资源组。 
- **目标虚拟网络和子网** - 分别使用 `TargetNetworkId` 和 `TargetSubnetName` 参数指定 Azure 虚拟网络的 ID 和 VM 应迁移到的子网的名称。 
- **目标 VM 名称** - 使用 `TargetVMName` 参数指定要创建的 Azure VM 的名称。
- **目标 VM 大小** - 使用 `TargetVMSize` 参数指定要用于复制 VM 的 Azure VM 大小。 例如，要将 VM 迁移到 Azure 中的 D2_v2 VM 虚拟机，请将 `TargetVMSize` 的值指定为“Standard_D2_v2”。  
- **许可证** - 若要为活动软件保障或 Windows Server 订阅涵盖的 Windows Server 计算机使用 Azure 混合权益，请将 `LicenseType` 参数的值指定为“WindowsServer”。 否则，请将 `LicenseType` 参数的值指定为“NoLicenseType”。
- **OS 磁盘** - 指定载有操作系统引导加载程序和安装程序的磁盘的唯一标识符。 要使用的磁盘 ID 是使用 `Get-AzMigrateServer` cmdlet 检索到的磁盘的唯一标识符 (UUID) 属性。
- **磁盘类型** - 指定 `DiskType` 参数的值，如下所示。
    - 若要使用高级托管磁盘，请指定“Premium_LRS”作为 `DiskType` 参数的值。 
    - 若要使用标准 SSD 盘，请指定“StandardSSD_LRS”作为 `DiskType` 参数的值。 
    - 若要使用标准 HDD 磁盘，请指定“Standard_LRS”作为 `DiskType` 参数的值。 
- **基础结构冗余** - 指定基础结构冗余选项，如下所示。 
    - 可用性区域，将迁移的计算机固定到区域中的特定可用性区域。 使用此选项可跨可用性区域分配形成多节点应用程序层的服务器。 仅当为迁移选择的目标区域支持可用性区域时，此选项才可用。 若要使用可用区域，请为 `TargetAvailabilityZone` 参数指定可用区域值。
    - 可用性集，将迁移的计算机放入可用性集。 若要使用此选项，所选的目标资源组必须具有一个或多个可用性集。 若要使用可用性集，请为 `TargetAvailabilitySet` 参数指定可用性集 ID。 

### <a name="replicate-vms-with-all-disks"></a>使用所有磁盘复制 VM
在本教程中，我们将复制发现的 VM 的所有磁盘，并为 Azure 中的 VM 指定一个新名称。 我们将发现的服务器的第一个磁盘指定为 OS 磁盘，并将所有磁盘作为标准 HDD 进行迁移。 OS 磁盘是包含操作系统引导加载程序和安装程序的磁盘。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。 

```azurepowershell
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType "Standard_LRS" -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>使用所选磁盘复制 VM
还可以通过使用 `New-AzMigrateDiskMapping` cmdlet 选择性地复制发现的 VM 的磁盘，并将其作为 `New-AzMigrateServerReplication` cmdlet 中的 `DiskToInclude` 参数的输入。 还可以使用 `New-AzMigrateDiskMapping` cmdlet 为要复制的每个磁盘指定不同的目标磁盘类型。 

为 `New-AzMigrateDiskMapping` cmdlet 的以下参数指定值。

- **DiskId** - 指定要迁移的磁盘的唯一标识符。 要使用的磁盘 ID 是使用 `Get-AzMigrateServer` cmdlet 检索到的磁盘的唯一标识符 (UUID) 属性。  
- **IsOSDisk** - 如果要迁移的磁盘是 VM 的操作系统磁盘，则指定“true”，否则为“false”。
- **DiskType** - 指定要在 Azure 中使用的磁盘类型。 

在下面的示例中，我们将只复制发现的 VM 的两个磁盘。 我们将指定 OS 磁盘，并为要复制的每个磁盘使用不同的磁盘类型。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。 

```azurepowershell
# View disk details of the discovered server
$DiscoveredServer.Disk | ConvertTo-JSON

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType "StandardSSD_LRS" -IsOSDisk "true"
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType "Premium_LRS" -IsOSDisk "false"

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk 

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="monitor-replication"></a>监视复制 

复制按如下方式进行：

- “开始复制”作业成功完成后，计算机将开始初始复制到 Azure。
- 在初始复制期间，会创建一个 VM 快照。 该快照中的磁盘数据将复制到 Azure 中的副本托管磁盘。
- 初始复制完成后，增量复制将会开始。 对本地磁盘所做的增量更改会定期复制到 Azure 中的副本磁盘。

使用 `Get-AzMigrateServerReplication` cmdlet 跟踪复制的状态。 

> [!NOTE]
> 发现的 VM ID 和复制的 VM ID 是两个不同的惟一标识符。 这两个标识符都可用于检索复制服务器的详细信息。  

### <a name="monitor-replication-using-discovered-vm-identifier"></a>使用发现的 VM 标识符监视复制
```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>使用复制 VM 标识符监视复制

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```

可以跟踪输出中的“迁移状态”和“迁移状态说明”属性。 
- 对于初始复制，迁移状态和迁移状态说明属性的值将分别为“InitialSeedingInProgress”和“初始复制”。 
- 在增量复制期间，迁移状态和迁移状态说明属性的值将分别为“正在复制”和“已可迁移”。
- 完成迁移后，迁移状态和迁移状态说明属性的值将分别为“已成功迁移”和“已迁移”。

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

```azurepowershell
$replicatingserver.ProviderSpecificDetail | convertto-json
```
可以使用输出中的“初始种子设定进度百分比”属性跟踪初始复制进度。

```output
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

## <a name="retrieve-the-status-of-a-job"></a>检索作业的状态

可以通过使用 `Get-AzMigrateJob` cmdlet 来监视作业的状态。 

```azurepowershell
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="update-properties-of-a-replicating-vm"></a>更新复制 VM 的属性

[Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)允许更改“复制 VM”的目标属性，例如名称、大小、资源组、NIC 配置等。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。 

```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic 
```
可以为 VM 更新以下属性。

- **VM 名称** - 使用 `TargetVMName` 参数指定要创建的 Azure VM 的名称。
- **VM 大小** - 使用 `TargetVMSize` 参数指定要用于复制 VM 的 Azure VM 大小。 例如，要将 VM 迁移到 Azure 中的 D2_v2 VM 虚拟机，请将 `TargetVMSize` 的值指定为“Standard_D2_v2”。  
- **虚拟网络** - 通过使用 `TargetNetworkId` 参数指定 VM 应迁移到的 Azure 虚拟网络的 ID。 
- **资源组** - 通过使用 `TargetResourceGroupId` 参数提供资源组 ID，指定 VM 应该迁移到的资源组的 ID。
- **网络接口** - 可以使用 `New-AzMigrateNicMapping` cmdlet 指定 NIC 配置。 然后将 `Set-AzMigrateServerReplication` cmdlet 中 `NicToUpdate` 参数的输入传递给该对象。 

    - **更改 IP 分配** - 若要为 NIC 指定静态 IP，请使用 `TargetNicIP` 参数提供要用作 VM 静态 IP 的 IPv4 地址。 若要为 NIC 动态分配 IP，请提供“auto”作为 `TargetNicIP` 参数的值。
    - 为 `TargetNicSelectionType` 参数使用值“Primary”、“Secondary”或“DoNotCreate”来指定 NIC 应该是主要的、次要的还是不在迁移的虚拟机上创建。 只能将一个 NIC 指定为 VM 的主要的 NIC。 
    - 要使 NIC 成为主要 NIC，还需要指定其他应设为次要的 NIC 或不在迁移的 VM 上创建的 NIC。  
    - 若要更改 NIC 的子网，请使用 `TargetNicSubnet` 参数指定子网的名称。

 - **可用性区域** - 若要使用可用区域，请为 `TargetAvailabilityZone` 参数指定可用区域值。
 - **可用性集** - 若要使用可用性集，请为 `TargetAvailabilitySet` 参数指定可用性集 ID。

在下面的示例中，我们将更新 NIC 配置，方法是将第一个 NIC 作为主要的 NIC 并为其分配一个静态 IP。 我们将为迁移丢弃第二个 NIC，并更新目标 VM 的名称和大小。 

```azurepowershell
# Specify the NIC properties to be updated for a replicating VM. 
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP "xxx.xxx.xxx.xxx" -TargetNicSelectionType "Primary"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType "DoNotCreate"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize "Standard_DS13_v2" -TargetVMName "MyMigratedVM" -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq "InProgress") -or ($UpdateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $UpdateJob.State
```

还可以列出 Azure Migrate 项目中的所有复制服务器，然后使用复制 VM 标识符更新 VM 属性。

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```


## <a name="run-a-test-migration"></a>运行测试迁移

增量复制开始后，在开始以 Azure 为目标的完整迁移之前，可以针对 VM 运行测试迁移。 强烈建议在迁移之前，针对每台计算机至少进行一次迁移测试。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。 

- 运行测试迁移可检查迁移是否按预期方式工作。 测试迁移不会影响本地计算机，该计算机仍在运行并继续复制。 
- 测试迁移通过使用复制的数据创建一个 Azure VM 来模拟迁移（通常是迁移到 Azure 订阅中的非生产 VNet）。
- 可以使用复制的测试 Azure VM 来验证迁移、执行应用测试，并解决完整迁移之前出现的任何问题。

通过使用 `TestNetworkID` 参数指定虚拟网络的 ID，选择要用于测试的 Azure 虚拟网络。

```azurepowershell
# Retrieve the Azure virtual network created for testing 
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq "InProgress") -or ($TestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TestMigrationJob.State
```

测试完成后，请使用 `Start-AzMigrateTestMigrationCleanup` cmdlet 清理测试迁移。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。 

```azurepowershell
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $CleanupTestMigrationJob.State
```

## <a name="migrate-vms"></a>迁移 VM

验证测试迁移是否按预期方式工作后，可以使用以下 cmdlet 迁移复制服务器。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。 

如果不想关闭源服务器，则不要使用 `TurnOffSourceServer` 参数。

```azurepowershell
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer 

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="complete-the-migration"></a>完成迁移

1. 完成迁移后，停止本地计算机的复制，并使用以下 cmdlet 清除 VM 的复制状态信息。 cmdlet 将返回一个作业，跟踪该作业可监视操作的状态。 

```azurepowershell
# Stop replication for a migrated server
$StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer 

# Track job status to check for completion
while (($StopReplicationJob.State -eq "InProgress") -or ($StopReplicationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $StopReplicationJob.State
```

2. 在已迁移的计算机上安装 Azure VM [Windows](../virtual-machines/extensions/agent-windows.md) 或 [Linux](../virtual-machines/extensions/agent-linux.md) 代理。
3. 执行任何迁移后的应用调整，例如更新数据库连接字符串和 Web 服务器配置。
4. 对 Azure 中当前运行的迁移应用程序执行最终的应用程序和迁移验收测试。
5. 将流量交接到已迁移的 Azure VM 实例。
6. 从本地 VM 清单中删除本地 VM。
7. 从本地备份中删除本地 VM。
8. 更新所有内部文档，以显示新的位置和 Azure VM 的 IP 地址。 

## <a name="post-migration-best-practices"></a>迁移后的最佳做法

- 为提高恢复能力，请执行以下操作：
    - 使用 Azure 备份服务备份 Azure VM 以保证数据安全。 [了解详细信息](../backup/quick-backup-vm-portal.md)。
    - 使用 Site Recovery 将 Azure VM 复制到次要区域以保证工作负荷运行且持续可用。 [了解详细信息](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- 为提高安全性，请执行以下操作：
    - 使用 [Azure 安全中心 - 适时管理](../security-center/security-center-just-in-time.md)锁定和限制入站流量访问。
    - 使用[网络安全组](../virtual-network/network-security-groups-overview.md)限制流入管理终结点的网络流量。
    - 部署[Azure 磁盘加密](../security/fundamentals/azure-disk-encryption-vms-vmss.md)以帮助保护磁盘，并保护数据以防被盗和未经授权的访问。
    - 详细了解[保护 IaaS 资源的安全](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)，并访问[Azure 安全中心](https://azure.microsoft.com/services/security-center/)。
- 为了便于监视和管理，请执行以下操作：
-  考虑部署[Azure 成本管理](../cost-management-billing/cloudyn/overview.md)以监视资源使用率和支出。