---
title: 使用 Azure Migrate 服务器迁移将 VMware 虚拟机迁移到具有服务器端加密 (SSE) 和客户管理的密钥 (CMK) 的 Azure
description: 了解如何使用 Azure Migrate 服务器迁移将 VMware VM 迁移到具有服务器端加密 (SSE) 和客户管理的密钥 (CMK) 的 Azure
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/12/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a6a422f461c16acd6fb2f9688aeb4e14b1b52a3
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110692282"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>将 VMware VM 迁移到启用了服务器端加密和客户管理的密钥的 Azure VM

本文介绍如何使用 Azure Migrate 服务器迁移（无代理复制）将 VMware VM 迁移到 Azure 虚拟机，后者具有使用服务器端加密 (SSE) 和客户管理的密钥 (CMK) 进行加密的磁盘。

Azure Migrate 服务器迁移门户体验可用于[通过无代理复制将 VMware VM 迁移到的 Azure。](tutorial-migrate-vmware.md) 门户体验目前不提供在 Azure 中为已复制的磁盘启用 SSE 和 CMK 的功能。 当前只能通过 REST API 实现为复制的磁盘启用 SSE 和 CMK 的功能。 本文介绍如何创建和部署 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)来复制 VMware VM，并在 Azure 中将已复制的磁盘配置为使用 SSE 和 CMK。

本文中的示例使用 [Azure PowerShell](/powershell/azure/new-azureps-module-az) 执行创建和部署资源管理器模板所需的任务。

[详细了解](../virtual-machines/disk-encryption.md)托管磁盘的服务器端加密 (SSE) 和客户管理的密钥 (CMK)。

## <a name="prerequisites"></a>先决条件

- [查看](tutorial-migrate-vmware.md)有关通过无代理复制将 VMware VM 迁移到的 Azure 的教程，以了解工具要求。
- [按照这些说明](./create-manage-projects.md)创建 Azure Migrate 项目并在项目中添加 **Azure Migrate：服务器迁移** 工具。
- [按照这些说明](how-to-set-up-appliance-vmware.md)在本地环境中设置适用于 VMware 的 Azure Migrate 设备，并完成发现。

## <a name="prepare-for-replication"></a>为复制做准备

VM 发现完成后，“服务器迁移”磁贴上的“发现的服务器”行将显示设备发现的 VMware VM 数。

在开始复制 VM 之前，需要准备复制基础结构。

1. 在目标区域中创建一个服务总线实例。 本地 Azure Migrate 设备使用服务总线来与服务器迁移服务进行通信，以协调复制和迁移。
2. 创建用于从复制传输操作日志的存储帐户。
3. 创建 Azure Migrate 设备将复制数据上传到的存储帐户。
4. 创建一个密钥保管库，并将这个密钥保管库配置为在步骤 3 和 4 中创建的存储帐户上管理 blob 访问的共享访问签名令牌。
5. 为在步骤 1 中创建的服务总线生成共享访问签名令牌，并为在上一步创建的密钥保管库中的令牌创建机密。
6. 创建密钥保管库访问策略，以便本地 Azure Migrate 设备（使用设备 AAD 应用）和服务器迁移服务能够访问密钥保管库。
7. 创建复制策略，并使用在上一步中创建的复制基础结构的详细信息来配置服务器迁移服务。

必须在目标 Azure 区域中创建复制基础结构以进行迁移，并在要将 VM 迁移到的目标 Azure 订阅中创建。

当在项目中首次复制 VM 时，服务器迁移门户体验会自动执行此操作，从而简化了复制基础结构的准备工作。 在本文中，我们假设已使用门户体验复制一个或多个 VM，并且已创建复制基础结构。 我们将介绍如何发现现有复制基础结构的详细信息，以及如何使用这些详细信息作为资源管理器模板的输入，该模板将用于设置使用 CMK 进行复制。

### <a name="identifying-replication-infrastructure-components"></a>标识复制基础结构组件

1. 在 Azure 门户上，请转至“资源组”页上，选择在其中创建 Azure Migrate 项目的资源组。
2. 从左侧菜单中选择“部署”，然后搜索以字符串“Microsoft.MigrateV2.VMwareV2EnableMigrate”开头的部署名称。 将会看到一个列表，其中列出了门户体验在此项目中为 VM 设置复制所创建的资源管理器模板。 我们将下载一个这样的模板，用它作为基础为使用 CMK 进行复制准备模板。
3. 如果要下载该模板，请选择与上一步中的字符串模式匹配的任何部署 > 从左侧菜单中选择“模板”> 单击顶部菜单中的“下载”。  在本地保存 template.json 文件。 将在上一步中编辑此模板文件。

## <a name="create-a-disk-encryption-set"></a>创建磁盘加密集

磁盘加密集对象将托管磁盘映射到包含 CMK 的密钥保管库，以用于 SSE。 如果要使用 CMK 复制 VM，将要创建磁盘加密集，并将其作为输入传递给复制操作。

按照[此处](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md)的示例使用 Azure PowerShell 创建磁盘加密集。 请确保在要将 VM 迁移到的目标订阅中创建磁盘加密集，并确保在迁移的目标 Azure 区域中创建磁盘加密集。

磁盘加密集可配置为使用客户管理的密钥对托管磁盘进行加密，或者使用客户管理的密钥和平台密钥进行双重加密。 如果要使用静态双重加密选项，请按[此处](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md)所述配置磁盘加密集。

在下面显示的示例中，磁盘加密集配置为使用客户管理的密钥。

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>获取要迁移的 VMware VM 的详细信息

在此步骤中，将使用 Azure PowerShell 获取需要迁移的 VM 的详细信息。 这些详细信息将用来为复制构造资源管理器模板。 具体而言，有两个相关属性：

- 已发现的 VM 的计算机资源 ID。
- VM 及其磁盘标识符的磁盘列表。

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

复制 SiteId 字符串的值，该字符串对应于通过其发现 VM 的 Azure Migrate 设备。 在上面所示的示例中，SiteId 为“/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite”

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

为要迁移的计算机复制 ResourceId、名称和磁盘 uuid 值。
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>创建用于复制的资源管理器模板

- 在所选编辑器中打开在“标识复制基础结构组件”步骤中下载的资源管理器模板文件。
- 除了类型为“Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems”的资源，从模板中删除所有资源定义
- 如果有多个以上类型的资源定义，请删除得只留一个。 从资源定义中删除所有“dependsOn”属性定义。
- 在此步骤结束时，应该有一个类似于以下示例的文件，并且具有相同的属性集。

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- 编辑资源定义中的“name”属性。 将“name”属性中最后一个“/”后面的字符串替换为“$machine.Name”的值（来自上一步）。
- 将“properties.providerSpecificDetails.vmwareMachineId”属性的值更改为“$machine.ResourceId”的值（来自上一步）。
- 分别将“targetResourceGroupId”、“targetNetworkId”、“targetSubnetName”的值设置为目标资源组 ID、目标虚拟网络资源 ID 和目标子网名称。  
- 将“licenseType”的值设置为“WindowsServer”，为这个 VM 应用 Azure 混合权益。 如果此 VM 不符合 Azure 混合权益的条件，请将“licenseType”的值设置为 NoLicenseType。
- 将“targetVmName”属性的值更改为已迁移 VM 所需的 Azure 虚拟机名称。
- 可选择在“targetVmName”属性下面添加一个名为“targetVmSize”的属性。  将“targetVmSize”属性的值更改为已迁移 VM 所需的 Azure 虚拟机大小。
- “disksToInclude”属性是用于复制的磁盘输入列表，每个列表项都代表一个本地磁盘。 在本地虚拟机上创建与磁盘数相同数量的列表项。 将列表项中的“diskId”属性替换为上一步中标识的磁盘的 uuid。 将 VM 的操作系统磁盘的“isOSDisk”值设置为“true”，将所有其他磁盘设置为“false”。 保留“logStorageAccountId”和“logStorageAccountSasSecretName”属性不变。  将“diskType”值设置为 Azure 托管磁盘类型（“Standard_LRS、Premium_LRS、StandardSSD_LRS”）用于磁盘。 对于需要使用 CMK 加密的磁盘，请添加名为“diskEncryptionSetId”的属性，并将值设置为在“创建磁盘加密集”步骤中创建的磁盘加密集 ($des.Id) 的资源 ID 
- 保存编辑后的模板文件。 对于上面的示例，编辑后的模板文件如下所示：

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>设置复制

现在可以将已编辑的资源管理器模板部署到项目资源组，以便为 VM 设置复制。 了解如何[通过 Azure 资源管理器模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>后续步骤

通过门户体验[监视复制](tutorial-migrate-vmware.md#track-and-monitor)状态，并执行测试迁移和迁移。
