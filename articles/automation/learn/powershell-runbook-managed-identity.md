---
title: 在 Azure 自动化中使用托管标识创建 PowerShell runbook
description: 在本教程中，你会了解如何在 Azure 自动化中将托管标识与 PowerShell runbook 配合使用。
services: automation
ms.subservice: process-automation
ms.date: 08/16/2021
ms.topic: tutorial
ms.openlocfilehash: 9f728a8e51dbe310f744d11dd495038b2ff96126
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540264"
---
# <a name="tutorial-create-automation-powershell-runbook-using-managed-identity"></a>教程：使用托管标识创建自动化 PowerShell runbook

本教程会指导你在 Azure 自动化中创建一个使用[托管标识](../automation-security-overview.md#managed-identities-preview)（而不是运行方式帐户）与资源进行交互的 [PowerShell runbook](../automation-runbook-types.md#powershell-runbooks)。 基于 Windows PowerShell 的 PowerShell Runbook。 借助 Azure Active Directory (Azure AD) 的托管标识，runbook 可以轻松访问其他受 Azure AD 保护的资源。

在本教程中，你将了解如何：

> [!div class="checklist"]
> * 向托管标识分配权限
> * 创建 PowerShell Runbook

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 至少具有一个用户分配的托管标识的 Azure 自动化帐户。 有关详细信息，请参阅[对 Azure 自动化帐户使用用户分配的托管标识](../add-user-assigned-identity.md)。
* 导入到自动化帐户中的 Az 模块：`Az.Accounts`、`Az.Automation`、`Az.ManagedServiceIdentity` 和 `Az.Compute`。 有关详细信息，请参阅[导入 Az 模块](../shared-resources/modules.md#import-az-modules)。
* 在计算机上安装 [Azure Az PowerShell 模块](/powershell/azure/new-azureps-module-az)。 若要安装或升级，请参阅[如何安装 Azure Az PowerShell 模块](/powershell/azure/install-az-ps)。
* [Azure 虚拟机](../../virtual-machines/windows/quick-create-powershell.md)。 由于需要停止并启动此虚拟机，因此它不应当是生产 VM。
* 大致熟悉 [Automation runbook](../manage-runbooks.md)。

## <a name="assign-permissions-to-managed-identities"></a>向托管标识分配权限

向托管标识分配权限，以允许它们停止和启动虚拟机。

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 以交互方式登录到 Azure，并按照说明进行操作。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount -Subscription
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. 为以下变量提供适当的值，然后执行脚本。

    ```powershell
    $resourceGroup = "resourceGroupName"
    
    # These values are used in this tutorial
    $automationAccount = "xAutomationAccount"
    $userAssignedOne = "xUAMI"
    ```

1. 使用 PowerShell cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 将角色分配给系统分配的托管标识。

    ```powershell
    $role1 = "DevTest Labs User"
    
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. 用户分配的托管标识需要相同的角色分配

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedOne).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role1
    ```

1. 执行本教程中使用的 cmdlet `Get-AzUserAssignedIdentity` 和 `Get-AzAutomationAccount` 需要系统分配的托管标识具有其他权限。

    ```powershell
    $role2 = "Reader"
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName $role2
    ```

## <a name="create-powershell-runbook"></a>创建 PowerShell Runbook

创建一个允许由任一托管标识执行的 runbook。 该 runbook 会启动已停止的 VM，或停止正在运行的 VM。

1. 登录 [Azure 门户](https://portal.azure.com/)，导航到你的自动化帐户。

1. 在“过程自动化”下，选择“Runbook”。 

1. 选择“创建 Runbook”  。
    1. 将 runbook 命名为 `miTesting`。
    1. 从“Runbook 类型”下拉菜单中，选择“PowerShell”。 
    1. 选择“创建”。

1. 在 runbook 编辑器中粘贴以下代码：

    ```powershell
    Param(
     [string]$resourceGroup,
     [string]$VMName,
     [string]$method,
     [string]$UAMI 
    )
    
    $automationAccount = "xAutomationAccount"
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process | Out-Null
    
    # Connect using a Managed Service Identity
    try {
            Connect-AzAccount -Identity -ErrorAction stop -WarningAction SilentlyContinue | Out-Null
        }
    catch{
            Write-Output "There is no system-assigned user identity. Aborting."; 
            exit
        }
    
    if ($method -eq "SA")
        {
            Write-Output "Using system-assigned managed identity"
        }
    elseif ($method -eq "UA")
        {
            Write-Output "Using user-assigned managed identity"
    
            # Connects using the Managed Service Identity of the named user-assigned managed identity
            $identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $UAMI
    
            # validates assignment only, not perms
            if ((Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.UserAssignedIdentities.Values.PrincipalId.Contains($identity.PrincipalId))
                {
                    Connect-AzAccount -Identity -AccountId $identity.ClientId | Out-Null
                }
            else {
                    Write-Output "Invalid or unassigned user-assigned managed identity"
                    exit
                }
        }
    else {
            Write-Output "Invalid method. Choose UA or SA."
            exit
         }
    
    # Get current state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName -Status).Statuses[1].Code
    
    Write-Output "`r`n Beginning VM status: $status `r`n"
    
    # Start or stop VM based on current state
    if($status -eq "Powerstate/deallocated")
        {
            Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup
        }
    elseif ($status -eq "Powerstate/running")
        {
            Stop-AzVM -Name $VMName -ResourceGroupName $resourceGroup -Force
        }
    
    # Get new state of VM
    $status = (Get-AzVM -ResourceGroupName $resourceGroup -Name $VMName -Status).Statuses[1].Code  
    
    Write-Output "`r`n Ending VM status: $status `r`n `r`n"
    
    Write-Output "Account ID of current context: " (Get-AzContext).Account.Id
    ```

1. 在编辑器中的第 8 行上，根据需要修改 `$automationAccount` 变量的值。

1. 选择“保存”，然后选择“测试窗格” 。

1. 使用适当的值填充参数 `RESOURCEGROUP` 和 `VMNAME`。 对于 `METHOD` 参数输入 `SA`，对于 `UAMI` 参数输入 `xUAMI`。 该 runbook 会尝试使用系统分配的托管标识更改 VM 的电源状态。

1. 选择“启动”。 该 runbook 完成后，输出应类似于以下内容：

    ```output
     Beginning VM status: PowerState/deallocated
    
    OperationId : 5b707401-f415-4268-9b43-be1f73ddc54b
    Status      : Succeeded
    StartTime   : 8/3/2021 10:52:09 PM
    EndTime     : 8/3/2021 10:52:50 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/running 
     
    Account ID of current context: 
    MSI@50342
    ```

1. 将 `METHOD` 参数的值更改为 `UA`。

1. 选择“启动”。 该 runbook 会尝试使用指定的用户分配的托管标识更改 VM 的电源状态。 该 runbook 完成后，输出应类似于以下内容：

    ```output
    Using user-assigned managed identity
    
     Beginning VM status: PowerState/running 
    
    OperationId : 679fcadf-d0b9-406a-9282-66bc211a9fbf
    Status      : Succeeded
    StartTime   : 8/3/2021 11:06:03 PM
    EndTime     : 8/3/2021 11:06:49 PM
    Error       : 
    Name        : 
    
     Ending VM status: PowerState/deallocated 
     
    Account ID of current context: 
    9034f5d3-c46d-44d4-afd6-c78aeab837ea
    ```

## <a name="clean-up-resources"></a>清理资源

若要删除不再需要的任何资源，请运行以下 runbook。

```powershell
#Remove runbook
Remove-AzAutomationRunbook `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name "miTesting" `
    -Force

# Remove role assignments
Remove-AzRoleAssignment `
    -ObjectId $UAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role2

Remove-AzRoleAssignment `
    -ObjectId $SAMI `
    -ResourceGroupName $resourceGroup `
    -RoleDefinitionName $role1
```

## <a name="next-steps"></a>后续步骤

在本教程中，你在 Azure 自动化中创建了一个使用[托管标识](../automation-security-overview.md#managed-identities-preview)（而不是运行方式帐户）与资源进行交互的 [PowerShell runbook](../automation-runbook-types.md#powershell-runbooks)。 有关 PowerShell 工作流 runbook 的信息，请参阅：

> [!div class="nextstepaction"]
> [教程：创建 PowerShell 工作流 Runbook](automation-tutorial-runbook-textual.md)




