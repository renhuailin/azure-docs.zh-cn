---
title: 使用自动化 runbook 启动虚拟机
description: 了解如何使用 Azure Automation runbooks 在 Azure DevTest Labs 的实验室中启动虚拟机。
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee3054c5b5434cba526c2025d5649fed0b44d391
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604758"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>使用 Azure Automation runbooks 按顺序启动实验室中的虚拟机
使用 DevTest Labs 的“[自动启动](devtest-lab-set-lab-policy.md#set-autostart)”功能，可以将虚拟机配置为在指定时间自动启动。 但是，此功能不支持按特定顺序启动虚拟机。 在某些情况下，这种类型的自动化会很有用。  一种方案是，实验室中的 Jumpbox VM 首先需要在其他虚拟机之前启动，因为 Jumpbox 用作其他虚拟机的访问点。  本文介绍了如何设置 Azure Automation 帐户，其中还介绍了用于执行脚本的 PowerShell runbook。 此脚本使用实验室中的虚拟机上的标记来控制启动顺序，而无需更改脚本。

## <a name="setup"></a>设置
在该示例中，实验室中的虚拟机需要使用相应的值（0、1、2等）来添加标记 **StartupOrder**。 指定任何不需要作为 -1 启动的虚拟机。

## <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户
按照[本文](../automation/automation-create-standalone-account.md)中的说明创建 Azure Automation 帐户。 创建帐户时，请选择“**Run As Accounts**”选项。 创建自动化帐户后，打开“**模块**”页面，并在菜单栏上选择“**更新 Azure 模块**”。 默认模块是多个旧版本，无需更新脚本即可工作。

## <a name="add-a-runbook"></a>运行 runbook
现在，若要将 runbook 添加到自动化帐户，请在左侧菜单中选择“**runbook**”。 在菜单上选择“**添加 runbook**”，并按照说明 [创建 PowerShell runbook](../automation/learn/powershell-runbook-managed-identity.md)。

## <a name="powershell-script"></a>PowerShell 脚本
以下脚本采用订阅名称，将实验室名称作为参数。 脚本流用于获取实验室中的所有虚拟机，然后分析出标记信息，以创建虚拟机名称及其启动顺序列表。 该脚本会按顺序浏览虚拟机，并启动虚拟机。 如果有多个虚拟机的特定序号，它们将使用 PowerShell 作业异步启动。 对于没有标记的虚拟机，请将“启动值”设置为最后一个 (10)，默认情况下，它们将启动。  如果实验室不希望自动安装虚拟机，请将标记值设置为 11，虚拟机将被忽略。

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>创建计划
若要每天执行此脚本，请在自动化帐户中[创建一个计划](../automation/shared-resources/schedules.md#create-a-schedule) 。 创建计划后，请将[其链接到 runbook](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook)。 

如果存在多个实验室和多个订阅，请将参数信息存储在不同实验室的文件中，并将文件传递给脚本，而不是单个参数。 脚本需要修改，但核心执行将是相同的。 虽然该示例使用 Azure Automation 来执行 PowerShell 脚本，但还有其他一些选项，如在生成/发布渠道中使用任务。

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解有关 Azure Automation 的详细信息：[Azure Automation 简介](../automation/automation-intro.md)。