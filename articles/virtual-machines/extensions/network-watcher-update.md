---
title: 将网络观察程序扩展更新到最新版本
description: 了解如何将 Azure 网络观察程序扩展更新到最新版本。
services: virtual-machines
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8efe40fd4a60da7fa77e642d8ad95b3495d9771e
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094588"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>将网络观察程序扩展更新到最新版本

## <a name="overview"></a>概述

[Azure 网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)是一项网络性能监视、诊断和分析服务，可对 Azure 网络进行监视。 若要按需捕获网络流量和使用 Azure VM 上的其他高级功能，必须具备网络观察程序代理虚拟机 (VM) 扩展。 网络观察程序扩展用于连接监视器、连接监视器（预览版）、连接疑难解答和数据包捕获等功能。

## <a name="prerequisites"></a>先决条件

本文假设你已在 VM 中安装了网络观察程序扩展。

## <a name="latest-version"></a>最新版本

当前，网络观察程序扩展的最新版本为 `1.4.1974.1`。

## <a name="update-your-extension-using-a-powershell-script"></a>使用 PowerShell 脚本更新扩展
要进行大型部署的客户需要一次更新多个 VM。 要手动更新选定的 VM，请参阅下一节 

```powershell
<#
    .SYNOPSIS
    This script will scan all VMs in the provided subscription and upgrade any out of date AzureNetworkWatcherExtensions

    .DESCRIPTION
    This script should be no-op if AzureNetworkWatcherExtensions are up to date
    Requires Azure PowerShell 4.2 or higher to be installed (e.g. Install-Module AzureRM).

    .EXAMPLE
    .\UpdateVMAgentsInSub.ps1 -SubID F4BC4873-5DAB-491E-B713-1358EF4992F2 -NoUpdate

#>
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string] $SubID,
    [Parameter(Mandatory=$false)]
    [Switch] $NoUpdate = $false,
    [Parameter(Mandatory=$false)]
    [string] $MinVersion = "1.4.1974.1"
)


function NeedsUpdate($version)
{
    if ($version -eq $MinVersion)
    {
        return $false
    }

    $lessThan = $true;
    $versionParts = $version -split '\.';
    $minVersionParts = $MinVersion -split '\.';
    for ($i = 0; $i -lt $versionParts.Length; $i++)
    {
        if ([int]$versionParts[$i] -gt [int]$minVersionParts[$i])
        {
            $lessThan = $false;
            break;
        }
    }

    return $lessThan
}

Write-Host "Scanning all VMs in the subscription: $($SubID)"
Select-AzSubscription -SubscriptionId $SubID;
$vms = Get-AzVM;
$foundVMs = $false;
Write-Host "Starting VM search, this may take a while"

foreach ($vmName in $vms)
{
    # Get Detailed VM info
    $vm = Get-AzVM -ResourceGroupName $vmName.ResourceGroupName -Name $vmName.name -Status;
    $isWindows = $vm.OsVersion -match "Windows";
    foreach ($extension in $vm.Extensions)
    {
        if ($extension.Name -eq "AzureNetworkWatcherExtension")
        {
            if (NeedsUpdate($extension.TypeHandlerVersion))
            {
                $foundVMs = $true;
                if (-not ($NoUpdate))
                {
                    Write-Host "Found VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name) -> Updating " -NoNewline
                    Remove-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Force
                    Write-Host "... " -NoNewline
                    $type = if ($isWindows) { "NetworkWatcherAgentWindows" } else { "NetworkWatcherAgentLinux" };
                    Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -Location $vmName.Location -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type $type -typeHandlerVersion "1.4"
                    Write-Host "Done"
                }
                else
                {
                    Write-Host "Found $(if ($isWindows) {"Windows"} else {"Linux"}) VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name)"
                }
            }
        }
    }
}

if ($foundVMs)
{
    Write-Host "Finished $(if ($NoUpdate) {"searching"} else {"updating"}) out of date AzureNetworkWatcherExtension on VMs"
}
else
{
    Write-Host "All AzureNetworkWatcherExtensions up to date"
}

```

## <a name="update-your-extension-manually"></a>手动更新扩展

若要更新扩展，需要知道扩展版本。

### <a name="check-your-extension-version"></a>检查扩展版本

可使用 Azure 门户、Azure CLI 或 PowerShell 检查扩展版本。

#### <a name="usetheazureportal"></a>使用 Azure 门户

1. 在 Azure 门户中转到 VM 的“扩展”窗格。
1. 选择“AzureNetworkWatcher”扩展，查看“详细信息”窗格。
1. 在“版本”字段中找到版本号。  

#### <a name="use-the-azure-cli"></a>使用 Azure CLI

在 Azure CLI 提示符下运行以下命令：

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
在输出中找到“AzureNetworkWatcherExtension”，并从输出中的“TypeHandlerVersion”字段确定版本号。  请注意：有关扩展的信息会在 JSON 输出中多次出现。 请在“extensions”块下查看，你会看到扩展的完整版本号。 

你会该看到如下内容：![Azure CLI 屏幕截图](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>使用 PowerShell

在 PowerShell 提示符下运行以下命令：

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
在输出中找到“Azure Network Watcher extension”，并从输出中的“TypeHandlerVersion”字段确定版本号。   

你会该看到如下内容：![PowerShell 屏幕截图](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>更新扩展

如果你的版本低于上面提到的最新版本，请使用以下任一选项更新扩展。

#### <a name="option-1-use-powershell"></a>选项 1：使用 PowerShell

运行以下命令：

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name " AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

如果这不起作用。 使用以下步骤删除扩展并重新安装。 此操作会自动添加最新版本。

删除扩展 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

重新安装扩展

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>选项 2：使用 Azure CLI

强制执行升级。

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

如果不起作用，请删除再重新安装该扩展，然后按照以下步骤自动添加最新版本。

删除扩展.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

再次安装该扩展。

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>选项 3：重启 VM

如果已将网络观察程序扩展的自动升级设置为 True，请重启 VM 安装以获取最新扩展。

## <a name="support"></a>支持

如果在本文的任何位置需要更多帮助，请参阅 [Linux](./network-watcher-linux.md) 或 [Windows](./network-watcher-windows.md) 的网络观察程序扩展文档。 还可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
