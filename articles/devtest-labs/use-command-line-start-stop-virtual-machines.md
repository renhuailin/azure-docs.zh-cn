---
title: 使用命令行工具启动和停止 VM
description: 了解如何使用命令行工具启动和停止 Azure 开发测试实验室中的虚拟机。
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e0c75cda526f5a365badf3f97aeb92a94b93588f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655555"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>使用命令行工具启动和停止 Azure 开发测试实验室虚拟机
本文介绍如何使用 Azure PowerShell 或 Azure CLI 在 Azure 开发测试实验室的实验室中启动或停止虚拟机。 你可以创建 PowerShell/CLI 脚本来自动执行这些操作。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概述
Azure 开发测试实验室是一种创建快速、简单和精益的开发/测试环境的方法。 使用它可以管理成本、快速设置 VM，并最大程度地减少浪费。  Azure 门户中提供了一些内置功能，可让你将实验室中的 VM 配置为在特定时间自动启动和停止。 

但是，在某些情况下，你可能需要通过 PowerShell/CLI 脚本自动启动和停止 VM。 它为你提供了一定的灵活性，使你可以随时（而不是在特定时间）启动和停止单个计算机。 在以下情况下，使用脚本运行这些任务会很有帮助。

- 在测试环境中使用三层应用程序时，需要按顺序启动各层。 
- 当满足自定义条件时，关闭 VM 以节省费用。 
- 将它作为 CI/CD 工作流中的一项任务，在流开始时启动，将 VM 用作生成计算机、测试计算机或基础结构，然后在进程完成时停止 VM。 例如，使用 Azure 开发测试实验室自定义映像工厂。  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> 以下脚本使用 Azure PowerShell Az 模块。 

下面的 PowerShell 脚本在实验室中启动一个 VM。 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) 是此脚本的重点。 ResourceId 参数是实验室中 VM 的完全限定的资源 ID。 在 Action 参数中，会根据需要设置“启动”或“停止”选项。

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
[Azure CLI](/cli/azure/get-started-with-azure-cli) 是自动启动和停止开发测试实验室 VM 的另一种方法。 Azure CLI 可以[安装](/cli/azure/install-azure-cli)在不同操作系统上。 以下脚本提供了在实验室中启动和停止 VM 的命令。 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何使用 Azure 门户执行这些操作：[重启 VM](devtest-lab-restart-vm.md)。
