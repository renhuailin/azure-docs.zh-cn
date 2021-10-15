---
title: 部署 Azure 磁盘池（预览版）
description: 了解如何部署 Azure 磁盘池。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/29/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 72a25b6bc51732ac9b598cbcb6b45f9ac84fc21b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351063"
---
# <a name="deploy-an-azure-disk-pool-preview"></a>部署 Azure 磁盘池（预览版）

本文介绍了如何部署和配置 Azure 磁盘池（预览版）。 在部署磁盘池之前，请阅读[概念](disks-pools.md)和[规划](disks-pools-planning.md)这两篇文章。

要使磁盘池正常工作，必须完成以下步骤：
- 针对预览版注册你的订阅。
- 将一个子网委托给你的磁盘池。
- 为磁盘池的资源提供程序分配用于管理磁盘资源的、基于角色的访问控制 (RBAC) 权限。
- 创建磁盘池。
    - 向磁盘池中添加磁盘。


## <a name="prerequisites"></a>先决条件

若要成功部署磁盘池，你必须具备：

- 要添加到磁盘池的一组托管磁盘。
- 一个虚拟网络，其中包含为磁盘池部署的专用子网。

如果要使用 Azure PowerShell 模块，请安装 [6.1.0 或更高版本](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true)。

如果要使用 Azure CLI，请安装[最新版本](/cli/azure/disk-pool)。

## <a name="register-your-subscription-for-the-preview"></a>针对预览版注册你的订阅

注册你对 Microsoft.StoragePool 提供程序的订阅，以便能够创建和使用磁盘池。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在 Azure 门户菜单中，搜索并选择“订阅”。
1. 选择要用于磁盘池的订阅。
1. 在左侧菜单中的“设置”下，选择“资源提供程序”。  
1. 找到资源提供程序 Microsoft.StoragePool 并选择“注册”。

注册订阅后，你可以部署磁盘池。

## <a name="delegate-subnet-permission"></a>委托子网权限

要使你的磁盘池能够与客户端计算机一起工作，你必须将一个子网委托给 Azure 磁盘池。 创建磁盘池时，需要指定一个虚拟网络和委托的子网。 你可以创建一个新子网或使用现有子网，并将其委托给 Microsoft.StoragePool/diskPools 资源提供程序。

1. 转到 Azure 门户中的虚拟网络窗格，选择要用于该磁盘池的虚拟网络。
1. 从虚拟网络窗格中选择“子网”并选择“+子网”。
1. 通过在“添加子网”窗格中完成以下必填字段来创建新子网：- 子网委托：选择 Microsoft.StoragePool

有关子网委托的详细信息，请参阅[添加或删除子网委托](../virtual-network/manage-subnet-delegation.md)。

## <a name="assign-storagepool-resource-provider-permissions"></a>分配 StoragePool 资源提供程序权限

磁盘必须满足以下要求才能在磁盘池中使用：

- 必须已向 StoragePool 资源提供程序分配一个 RBAC 角色，该角色包含对磁盘池中的每个托管磁盘的读取和写入权限。
- 必须是磁盘池所在的可用性区域中的高级 SSD 磁盘或超级磁盘。
    - 对于超级磁盘，磁盘扇区的大小必须为 512 字节。
- 必须是 maxShares 值至少为 2 的共享磁盘。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 搜索并选择包含磁盘的资源组，或者搜索并选择每个磁盘本身。
1. 选择“访问控制(IAM)”。
1. 选择“添加角色分配(预览版)”，然后在角色列表中选择“磁盘池操作员”。

    如果你愿意，你可以改为创建自己的自定义角色。 磁盘池的自定义角色必须具有以下 RBAC 权限才能工作：Microsoft.Compute/disks/write 和 Microsoft.Compute/disks/read。

1. 对于“将访问权限分配给”，请选择“用户、组或服务主体”。 
1. 选择“+ 选择成员”，然后搜索“StoragePool 资源提供程序”，选择它并保存。

## <a name="create-a-disk-pool"></a>创建磁盘池

为了优化性能，请在客户端所在的可用性区域中部署磁盘池。 如果要为 Azure VMware 解决方案云部署磁盘池，并且在确定可用性区域方面需要指导，请填写[此表单](https://aka.ms/DiskPoolCollocate)。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 搜索并选择“磁盘池”。
1. 选择“+添加”以创建新的磁盘池。
1. 填写请求的详细信息，选择将使用磁盘池的客户端所在的区域和可用性区域。
1. 选择已委托给 StoragePool 资源提供程序的子网及其关联的虚拟网络。
1. 选择“下一步”，向磁盘池中添加磁盘。

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool.png" alt-text="屏幕截图显示了用于创建磁盘池的“基本信息”窗格。":::

### <a name="add-disks"></a>添加磁盘

#### <a name="prerequisites"></a>先决条件

若要添加磁盘，它必须满足以下要求：

- 必须是磁盘池所在的可用性区域中的高级 SSD 磁盘或超级磁盘。
    - 目前，高级 SSD 只能在门户中添加。 超级磁盘必须通过 Azure PowerShell 模块或 Azure CLI 添加。
    - 对于超级磁盘，磁盘扇区的大小必须为 512 字节。
- 必须是 maxShares 值至少为 2 的共享磁盘。
- 你必须向磁盘池的资源提供程序授予 RBAC 权限，用于管理你计划添加的磁盘。

如果磁盘满足这些要求，则可以通过在磁盘池窗格中选择“+添加磁盘”将其添加到磁盘池。

:::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-disks-blade.png" alt-text="屏幕截图显示了用于创建磁盘池和添加突出显示的磁盘的磁盘窗格。":::

### <a name="enable-iscsi"></a>启用 iSCSI

1. 选择“iSCSI”窗格。
1. 选择“启用 iSCSI”。
1. 输入 iSCSI 目标的名称。系统将基于此名称生成 iSCSI 目标 IQN。
    - 如果希望禁用单个磁盘的 iSCSI 目标，请在该磁盘的“状态”下选择“禁用”。 
    - ACL 模式默认设置为“动态”。 若要将你的磁盘池用作 Azure VMware 解决方案的存储解决方案，必须将 ACL 模式设置为“动态”。
1. 选择“查看 + 创建”。

    :::image type="content" source="media/disks-pools-deploy/create-a-disk-pool-iscsi-blade.png" alt-text="屏幕截图显示了用于创建磁盘池的 iSCSI 窗格。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

提供的脚本执行以下操作：
- 安装创建和使用磁盘池时所需的模块。
- 创建磁盘并为其分配 RBAC 权限。 如果已执行此操作，则可以注释掉脚本的这些节。
- 创建一个磁盘池并向其中添加磁盘。
- 创建并启用 iSCSI 目标。

在运行该脚本之前，请将其中的变量替换为你自己的变量。 如果你已填写超级磁盘表单，则还需要修改它以使用现有的超级磁盘。

```azurepowershell
# Install the required module for Disk Pool
Install-Module -Name Az.DiskPool -RequiredVersion 0.1.1 -Repository PSGallery

# Sign in to the Azure account and setup the variables
$subscriptionID = "<yourSubID>"
Set-AzContext -Subscription $subscriptionID
$resourceGroupName= "<yourResourceGroupName>"
$location = "<desiredRegion>"
$diskName = "<desiredDiskName>"
$availabilityZone = "<desiredAvailabilityZone>"
$subnetId='<yourSubnetID>'
$diskPoolName = "<desiredDiskPoolName>"
$iscsiTargetName = "<desirediSCSITargetName>" # This will be used to generate the iSCSI target IQN name
$lunName = "<desiredLunName>"

# You can skip this step if you have already created the disk and assigned proper RBAC permission to the resource group the disk is deployed to
$diskconfig = New-AzDiskConfig -Location $location -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -zone $availabilityZone -MaxSharesCount 2
$disk = New-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -Disk $diskconfig
$diskId = $disk.Id
$scopeDef = "/subscriptions/" + $subscriptionId + "/resourceGroups/" + $resourceGroupName
$rpId = (Get-AzADServicePrincipal -SearchString "StoragePool Resource Provider").id

New-AzRoleAssignment -ObjectId $rpId -RoleDefinitionName "Virtual Machine Contributor" -Scope $scopeDef

# Create a Disk Pool
New-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $subnetId -AvailabilityZone $availabilityZone -SkuName Standard
$diskpool = Get-AzDiskPool -ResourceGroupName $resourceGroupName -Name $DiskPoolName

# Add disks to the Disk Pool
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskId
$lun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $diskId -Name $lunName

# Create an iSCSI Target and expose the disks as iSCSI LUNs
New-AzDiskPoolIscsiTarget -DiskPoolName $diskPoolName -Name $iscsiTargetName -ResourceGroupName $resourceGroupName -Lun $lun -AclMode Dynamic

Write-Output "Print details of the iSCSI target exposed on Disk Pool"

Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName | fl
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

提供的脚本执行以下操作：
- 安装创建和使用磁盘池时所需的扩展。
- 创建磁盘并为其分配 RBAC 权限。 如果已执行此操作，则可以注释掉脚本的这些节。
- 创建一个磁盘池并向其中添加磁盘。
- 创建并启用 iSCSI 目标。

在运行该脚本之前，请将其中的变量替换为你自己的变量。 如果你已填写超级磁盘表单，则还需要修改它以使用现有的超级磁盘。

```azurecli
# Add disk pool CLI extension
az extension add -n diskpool

#az extension add -s https://zuhdefault.blob.core.windows.net/cliext/diskpool-0.1.1-py3-none-any.whl

#Select subscription
az account set --subscription "<yourSubscription>"

##Initialize input parameters
resourceGroupName='<yourRGName>'
location='<desiredRegion>'
zone=<desiredZone>
subnetId='<yourSubnetID>'
diskName='<desiredDiskName>'
diskPoolName='<desiredDiskPoolName>'
targetName='<desirediSCSITargetName>'
lunName='<desiredLunName>'

#You can skip this step if you have already created the disk and assigned permission in the prerequisite step. Below is an example for premium disks.
az disk create --name $diskName --resource-group $resourceGroupName --zone $zone --location $location --sku Premium_LRS --max-shares 2 --size-gb 1024

#You can deploy all your disks into one resource group and assign StoragePool Resource Provider permission to the group
storagePoolObjectId=$(az ad sp list --filter "displayName eq 'StoragePool Resource Provider'" --query "[0].objectId" -o json)
storagePoolObjectId="${storagePoolObjectId%"}"
storagePoolObjectId="${storagePoolObjectId#"}"

az role assignment create --assignee-object-id $storagePoolObjectId --role "Virtual Machine Contributor" --resource-group $resourceGroupName

#Create a disk pool 
az disk-pool create --name $diskPoolName \
--resource-group $resourceGroupName \
--location $location \
--availability-zones $zone \
--subnet-id $subnetId \
--sku name="Standard"

#Initialize an iSCSI target. You can have 1 iSCSI target per disk pool
az disk-pool iscsi-target create --name $targetName \
--disk-pool-name $diskPoolName \
--resource-group $resourceGroupName \
--acl-mode Dynamic

#Add the disk to disk pool
diskId=$(az disk show --name $diskName --resource-group $resourceGroupName --query "id" -o json)
diskId="${diskId%"}"
diskId="${diskId#"}"

az disk-pool update --name $diskPoolName --resource-group $resourceGroupName --disks $diskId

#Expose disks added in the Disk Pool as iSCSI Lun 
az disk-pool iscsi-target update --name $targetName \
 --disk-pool-name $diskPoolName \
 --resource-group $resourceGroupName \
 --luns name=$lunName managed-disk-azure-resource-id=$diskId
```
---

## <a name="next-steps"></a>后续步骤

- 如果在部署磁盘池时遇到任何问题，请参阅[排查 Azure 磁盘池（预览版）问题](disks-pools-troubleshoot.md)。
- [将磁盘池附加到 Azure VMware 解决方案主机（预览版）](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md)。
- [管理磁盘池](disks-pools-manage.md)。
