---
title: 快速入门 - 资源管理器模板 VM 备份
description: 了解如何使用 Azure 资源管理器模板备份虚拟机
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/28/2021
ms.custom: mvc,subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 8657ea43abe0808734a5223131a3105414deb385
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113433897"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>快速入门：使用 ARM 模板在 Azure 中备份虚拟机

[Azure 备份](backup-overview.md)可备份本地计算机和应用以及 Azure VM。 本文介绍如何使用 Azure 资源管理器模板（ARM 模板）和 Azure PowerShell 备份 Azure VM。 本快速入门重点介绍部署 ARM 模板以创建恢复服务保管库的过程。 有关开发 ARM 模板的详细信息，请参阅 [Azure 资源管理器文档](../azure-resource-manager/index.yml)和[模板参考](/azure/templates/microsoft.recoveryservices/allversions)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)是一个逻辑容器，用于存储受保护资源（例如 Azure VM）的备份数据。 运行备份作业时，该作业会在恢复服务保管库中创建一个恢复点。 然后，可以使用其中一个恢复点将数据还原到给定的时间点。 或者，可以使用 [Azure PowerShell](./quick-backup-vm-powershell.md)、[Azure CLI](quick-backup-vm-cli.md) 或在 [Azure 门户](quick-backup-vm-portal.md)中备份 VM。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.recoveryservices%2Frecovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/recovery-services-create-vm-and-configure-backup/)。 此模板允许你部署使用 DefaultPolicy for Protection 配置的简单 Windows VM 和恢复服务保管库。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.recoveryservices/recovery-services-create-vm-and-configure-backup/azuredeploy.json":::

该模板中定义了以下资源：

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/vaults**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>部署模板

若要部署模板，请选择“试一试”以打开 Azure Cloud Shell，然后将以下 PowerShell 脚本粘贴到 shell 窗口中。 若要粘贴代码，请右键单击 shell 窗口并选择“粘贴”。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.recoveryservices/recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

在本快速入门中，使用 Azure PowerShell 来部署 ARM 模板。 [Azure 门户](../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md) 和 [Rest API](../azure-resource-manager/templates/deploy-rest.md) 也可用于部署模板。

## <a name="validate-the-deployment"></a>验证部署

### <a name="start-a-backup-job"></a>启动备份作业

模板创建一个 VM，并在该 VM 上启用备份。 部署模板后，需要启动备份作业。 有关详细信息，请参阅[启动备份作业](./quick-backup-vm-powershell.md#start-a-backup-job)。

### <a name="monitor-the-backup-job"></a>监视备份作业

若要监视备份作业，请参阅[监视备份作业](./quick-backup-vm-powershell.md#monitor-the-backup-job)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要备份 VM，则可以清理它。

- 如果要尝试还原 VM，请跳过清理。
- 如果使用了现有的 VM，可以跳过最后一个 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet，以保留资源组和 VM。

禁用保护，删除还原点和保管库。 然后删除资源组和关联 VM 资源，如下所示：

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建恢复服务保管库，如何在 VM 上启用保护，以及如何创建初始恢复点。

- [了解如何](tutorial-backup-vm-at-scale.md)在 Azure 门户中备份 VM。
- [了解如何](tutorial-restore-disk.md)快速还原 VM
- [了解如何](../azure-resource-manager/templates/template-tutorial-create-first-template.md)创建 ARM 模板。
