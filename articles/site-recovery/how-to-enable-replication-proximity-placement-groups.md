---
title: 复制邻近放置组中运行的 Azure VM
description: 了解如何使用 Azure Site Recovery 复制邻近放置组中运行的 Azure VM。
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: abcb233665d6c6074ff50d1bb53e553ed9eb99ef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741963"
---
# <a name="replicate-virtual-machines-running-in-a-proximity-placement-group-to-another-region"></a>将邻近放置组中运行的虚拟机复制到另一个区域

本文介绍如何将邻近放置组中运行的 Azure 虚拟机 (VM) 复制、故障转移和故障恢复到次要区域。

[邻近放置组](../virtual-machines/windows/proximity-placement-groups-portal.md)是 Azure 虚拟机中的逻辑分组功能。 可以使用邻近放置组来减少与应用程序关联的 VM 间网络延迟。 

当在相同的邻近放置组中部署 VM 时，它们彼此的物理位置会尽可能接近。 邻近放置组有助于满足对延迟敏感的工作负载的要求。

## <a name="disaster-recovery-with-proximity-placement-groups"></a>邻近放置组中的灾难恢复

在典型场景中，你可能会在邻近放置组中运行虚拟机，以避免在应用程序的各个层之间出现网络延迟。 虽然这种方法可以最大程度减少应用程序的网络延迟，但你可能希望通过使用 Azure Site Recovery 来保护这些应用程序，以应对任何区域级别的故障。 

Site Recovery 将数据从一个 Azure 区域复制到另一个区域。 在故障转移事件中，Site Recovery 可启动灾难恢复 (DR) 区域的计算机。

## <a name="considerations"></a>注意事项

- 最佳做法是将虚拟机故障转移和故障恢复到邻近放置组。 如果存在容量分配限制，导致我们无法在邻近放置组内启动 VM，则故障转移和故障回复仍将执行，但将在邻近放置组外创建 VM。
- 如果将某个可用性集固定到邻近放置组，且可用性集中的 VM 在故障恢复或故障转移时具有分配约束，则系统将在可用性集和邻近放置组之外创建 VM。
- 非托管磁盘不支持适用于邻近放置组的 Site Recovery。

> [!NOTE]
> 对于从 Hyper-V 迁移到 Azure 的方案，Azure Site Recovery 不支持从托管磁盘故障恢复。 不支持从 Azure 中的邻近放置组故障恢复到 Hyper-V。

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-the-azure-portal"></a>通过 Azure 门户为邻近放置组中的 VM 设置灾难恢复

### <a name="azure-to-azure"></a>Azure 到 Azure

可以通过“VM 灾难恢复”页面选择为虚拟机启用复制。 或者，可以转到预先创建的保管库，进入 Site Recovery 部分，然后启用复制。 让我们看看如何通过这两种方法在邻近放置组内设置 Site Recovery VM。

若要在 DR 区域中选择邻近放置组，同时通过基础结构即服务 (IaaS) VM DR 页面启用复制，请执行下列操作：

1. 转到虚拟机。 在左侧窗格中的“操作”下，选择“灾难恢复”。
2. 在“基本信息”选项卡中，选择要将 VM 复制到的 DR 区域。 转到“高级设置”。
3. 可以看到 VM 的邻近放置组，以及用于在 DR 区域中选择邻近放置组的选项。 如果选择使用此默认选项，则 Site Recovery 还会提供一个选项，供你使用其为你创建的新邻近放置组。 
 
   选择所需的邻近放置组。 然后选择“查看 + 开始复制”。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="显示用于启用复制的高级设置的屏幕截图。":::

若要在 DR 区域中选择邻近放置组，同时通过保管库页面启用复制，请执行下列操作：

1. 转到恢复服务保管库，然后转到“Site Recovery”选项卡。
2. 选择“+ 启用 Site Recovery”。 然后，选择“1：在 Azure 虚拟机下启用复制”（在希望复制 Azure VM 的情况下）。 
3. 在“源”选项卡中填写必填字段，然后选择“下一步”。
4. 在“虚拟机”选项卡中选择要为其启用复制的 VM 列表，然后选择“下一步”。
5. 可以看到在 DR 区域中选择邻近放置组的选项。 如果选择使用此默认选项，则 Site Recovery 还会提供一个选项，供你使用其为你创建的新邻近放置组。 

   选择所需的邻近放置组，然后继续启用复制。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="显示用于自定义目标设置的选项的屏幕截图。":::

为 VM 启用复制后，可以轻松更新 DR 区域中选择的邻近放置组：

1. 转到虚拟机。 在左侧窗格中的“操作”下，选择“灾难恢复”。
2. 转到“计算和网络”窗格，选择“编辑”。
3. 可以看到用于编辑多个目标设置（包括目标邻近放置组）的选项。 选择要将 VM 故障转移到的邻近放置组，然后选择“保存”。

### <a name="vmware-to-azure"></a>VMware 到 Azure

为 VM 启用复制后，可以为目标 VM 设置邻近放置组。 请确保根据要求在目标区域中单独创建邻近放置组。 

为 VM 启用复制后，可以轻松更新 DR 区域中选择的邻近放置组：

1. 从保管库中选择虚拟机。 在左侧窗格中的“操作”下，选择“灾难恢复”。
2. 转到“计算和网络”窗格，选择“编辑”。
3. 可以看到用于编辑多个目标设置（包括目标邻近放置组）的选项。 选择要将 VM 故障转移到的邻近放置组，然后选择“保存”。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="显示 VMware 到 Azure 的计算和网络选择的屏幕截图。":::

### <a name="hyper-v-to-azure"></a>Hyper-V 到 Azure

为 VM 启用复制后，可以为目标 VM 设置邻近放置组。 请确保根据要求在目标区域中单独创建邻近放置组。 

为 VM 启用复制后，可以轻松更新 DR 区域中选择的邻近放置组：

1. 从保管库中选择虚拟机。 在左侧窗格中的“操作”下，选择“灾难恢复”。
2. 转到“计算和网络”窗格，选择“编辑”。
3. 可以看到用于编辑多个目标设置（包括目标邻近放置组）的选项。 选择要将 VM 故障转移到的邻近放置组，然后选择“保存”。

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="显示 Hyper-V 到 Azure 的计算和网络选择的屏幕截图。":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>通过 PowerShell 为邻近放置组中的 VM 设置灾难恢复

### <a name="prerequisites"></a>先决条件 

- 确保已有 Azure PowerShell Az 模块。 如需安装或升级 Azure PowerShell，请遵循此 [Azure PowerShell 安装和配置指南](/powershell/azure/install-az-ps)。
- 最小的 Azure PowerShell Az 版本应为 4.1.0。 若要检查当前版本，请使用以下命令：

    ```
    Get-InstalledModule -Name Az
    ```

> [!NOTE]
> 确保你随时可以使用目标邻近放置组的唯一 ID。 使用的命令取决于你是[创建全新邻近放置组](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group)还是[使用现有邻近放置组](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups)。

### <a name="azure-to-azure"></a>Azure 到 Azure

1. [登录帐户并设置订阅](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription)。
2. [获取你计划复制的虚拟机的详细信息。](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated)
3. [创建恢复服务保管库](./azure-to-azure-powershell.md#create-a-recovery-services-vault)并[设置保管库上下文](./azure-to-azure-powershell.md#set-the-vault-context)。
4. 准备保管库以开始复制虚拟机。 此步骤涉及为主区域和恢复区域[创建 Azure Service Fabric 对象](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region)。
5. 为主结构和恢复结构[创建 Site Recovery 保护容器](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric)。
6. [创建复制策略](./azure-to-azure-powershell.md#create-a-replication-policy)。
7. [创建主保护容器和恢复保护容器之间的保护容器映射](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container)，以及[用于故障恢复的保护容器映射](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)。
8. [创建缓存存储帐户](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account)。
9. [创建所需的网络映射](./azure-to-azure-powershell.md#create-network-mappings)。
10. 使用以下 PowerShell cmdlet 复制具有托管磁盘的 Azure 虚拟机：

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    #Make sure to replace the variable $datadiskName with the data disk name.

    #Data disk
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $datadiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

    #Create a list of disk replication configuration objects for the disks of the virtual machine that will be replicated.

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

    为多个数据磁盘启用复制时，请使用以下 PowerShell cmdlet：

    ```azurepowershell
    #Get the resource group that the virtual machine must be created in when it's failed over.
    $RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

    #Specify replication properties for each disk of the VM that will be replicated (create disk replication configuration).
    #Make sure to replace the variable $OSdiskName with the OS disk name.

    #OS Disk
    $OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
    $OSdiskId = $OSdisk.Id
    $RecoveryOSDiskAccountType = $OSdisk.Sku.Name
    $RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

    $OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

    $diskconfigs = @()
    $diskconfigs += $OSDiskReplicationConfig

    #Data disk

    # Add data disks
    Foreach( $disk in $VM.StorageProfile.DataDisks)
    {
        $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
        $dataDiskId1 = $datadisk[0].Id
        $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
        $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
        $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
             -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
             -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
        $diskconfigs += $DataDisk1ReplicationConfig
    }

    #Start replication by creating a replication protected item. Use a GUID for the name of the replication protected item to ensure uniqueness of the name.

    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```

    使用邻近放置组启用区域到区域复制时，用于启动复制的命令将与 PowerShell cmdlet 交换：

    ```azurepowershell
    $TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
    ```

    启动复制操作成功后，虚拟机数据将复制到恢复区域。

    复制过程首先在恢复区域中初始植入虚拟机复制磁盘的副本。 此阶段中称为初始复制阶段。

    初始复制完成后，复制过程将转移到差异同步阶段。 此时，虚拟机受到保护，可对其执行测试故障转移操作。 初始复制完成后，表示虚拟机的复制项的复制状态将进入受保护状态。

    获取虚拟机对应的复制保护项的详细信息，监视该虚拟机的复制状态和复制运行状况：

    ```azurepowershell
    Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
    ```

11. [执行、验证和清理测试故障转移](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover)。
12. [虚拟机故障转移](./azure-to-azure-powershell.md#fail-over-to-azure)。
13. 使用以下 PowerShell cmdlet 重新保护并故障恢复到源区域：

    ```azurepowershell
    #Create a cache storage account for replication logs in the primary region.
    $WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


    #Use the recovery protection container, the new cache storage account in West US, and the source region VM resource group. 
    Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
    ```

14. [禁用复制](./azure-to-azure-powershell.md#disable-replication)。

### <a name="vmware-to-azure"></a>VMware 到 Azure

1. 为到 Azure 的灾难恢复[准备本地 VMware 服务器](./vmware-azure-tutorial-prepare-on-premises.md)。
2. [登录帐户并设置订阅](./vmware-azure-disaster-recovery-powershell.md#log-into-azure)。
3. [设置恢复服务保管库](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault)并[设置保管库上下文](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context)。
4. [验证保管库注册](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration)。
5. [创建复制策略](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy)。
6. [添加 vCenter 服务器并发现虚拟机](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms)，然后[创建存储帐户以进行复制](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication)。
7. 复制 VMware 虚拟机，并使用以下 PowerShell cmdlet 查看详细信息：

   ```azurepowershell
   #Get the target resource group to be used.
   $ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

   #Get the target virtual network to be used.
   $RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

   #Get the protection container mapping for the replication policy named ReplicationPolicy.
   $PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

   #Get the protectable item that corresponds to the virtual machine CentOSVM1.
   $VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

   # Enable replication for virtual machine CentOSVM1 by using the Az.RecoveryServices module 2.0.0 onward to replicate to managed disks.
   # The name specified for the replicated item needs to be unique within the protection container. Use a random GUID to ensure uniqueness.
   $Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
   ```

8. 使用 `Get-ASRReplicationProtectedItem` cmdlet 检查虚拟机的复制状态和复制运行状况：

   ```azurepowershell
   Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
   ```

9. [配置故障转移设置](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings)。
10. [运行测试故障转移](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover)。
11. [故障转移到 Azure](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure)。

### <a name="hyper-v-to-azure"></a>Hyper-V 到 Azure

1. 为到 Azure 的灾难恢复[准备本地 Hyper-V 服务器](./hyper-v-prepare-on-premises-tutorial.md)。
2. [登录 Azure](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account)。
3. [设置保管库](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault)并[设置恢复服务保管库上下文](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context)。
4. [创建 Hyper-V 站点](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site)。
5. [安装提供程序和代理](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent)。
6. [创建复制策略](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy)。
7. 使用以下步骤启用复制： 
    
   a. 检索与要保护的 VM 相对应的可保护项：

      ```azurepowershell
      $VMFriendlyName = "Fabrikam-app"          #Name of the VM
      $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
      ```
   b. 保护 VM。 如果要保护的 VM 有多个附加磁盘，则需使用 `OSDiskName` 参数指定操作系统磁盘：
    
      ```azurepowershell
      $OSType = "Windows"          # "Windows" or "Linux"
      $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId     $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
      ```
   c. 等待 VM 在完成初始复制后进入受保护状态。 此过程可能需要一段时间，具体取决于诸如要复制的数据量和 Azure 的可用上游带宽等因素。 
   
      进入受保护状态后，作业的 `State` 和 `StateDescription` 更新如下： 
    
      ```azurepowershell
      $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
      $DRjob | Select-Object -ExpandProperty State

      $DRjob | Select-Object -ExpandProperty StateDescription
      ```
   d. 更新各种恢复属性（例如 VM 角色大小），并更新故障转移后要将 VM NIC 连接到的 Azure 网络：

      ```azurepowershell
      $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

      $VMFriendlyName = "Fabrikam-App"

      $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

      $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

      $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

      $UpdateJob | Select-Object -ExpandProperty state

      Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
      ```
8. [运行测试故障转移](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover)。


## <a name="next-steps"></a>后续步骤

若要执行 VMware 到 Azure 的重新保护和故障恢复，请参阅[准备重新保护和故障恢复 VMware VM](./vmware-azure-prepare-failback.md)。

若要从 Hyper-V 故障转移到 Azure，请参阅[运行从本地到 Azure 的故障转移](./site-recovery-failover.md)。 若要进行故障恢复，请参阅[为 Hyper-V VM 运行故障恢复](./hyper-v-azure-failback.md)。

有关详细信息，请参阅 [Site Recovery 中的故障转移](site-recovery-failover.md)。
