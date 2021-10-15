---
title: 使用警报触发 Azure 自动化 Runbook
description: 本文介绍如何在引发 Azure 警报时触发 runbook 运行。
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee8d8929dc444d72539893a7978b828fc5c3742d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352618"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>使用警报触发 Azure 自动化 Runbook

可以使用 [Azure Monitor](../azure-monitor/overview.md) 来监视 Azure 中大多数服务的基本级别指标和日志。 可以使用[操作组](../azure-monitor/alerts/action-groups.md)调用 Azure 自动化 Runbook，以便基于警报自动执行任务。 本文介绍如何使用警报来配置和运行 Runbook。


## <a name="prerequisites"></a>先决条件

* 至少具有一个用户分配的托管标识的 Azure 自动化帐户。 有关详细信息，请参阅[对 Azure 自动化帐户使用用户分配的托管标识](./add-user-assigned-identity.md)。
* 导入到自动化帐户中的 Az 模块：`Az.Accounts` 和 `Az.Compute`。 有关详细信息，请参阅[导入 Az 模块](./shared-resources/modules.md#import-az-modules)。
* [Azure 虚拟机](../virtual-machines/windows/quick-create-powershell.md)。
* 在计算机上安装 [Azure Az PowerShell 模块](/powershell/azure/new-azureps-module-az)。 若要安装或升级，请参阅[如何安装 Azure Az PowerShell 模块](/powershell/azure/install-az-ps)。
* 大致熟悉 [Automation runbook](./manage-runbooks.md)。

## <a name="alert-types"></a>警报类型

可对三种警报类型使用自动化 Runbook：

* 常见警报
* 活动日志警报
* 近实时指标警报

> [!NOTE]
> 常见的警报架构会在 Azure 中标准化警报通知的使用体验。 一直以来，Azure 中的三种警报类型（指标、日志和活动日志）都已有自己的电子邮件模板、Webhook 架构等。若要了解详细信息，请参阅[常见警报架构](../azure-monitor/alerts/alerts-common-schema.md)。

当警报调用 Runbook 时，实际调用是对 Webhook 的 HTTP POST 请求。 该 POST 请求的正文包含一个 JSON 格式的对象，该对象包含与警报相关的有用属性。 下表列出了每种警报类型的有效负载架构的相应链接：

|警报  |说明|负载架构  |
|---------|---------|---------|
|[常见警报](../azure-monitor/alerts/alerts-common-schema.md)|常见警报架构，用于在目前的 Azure 中标准化警报通知的使用体验。|常见警报有效负载架构。|
|[活动日志警报](../azure-monitor/alerts/activity-log-alerts.md) |当 Azure 活动日志中的任何新事件符合特定条件时，就会发送通知。 例如，当 myProductionResourceGroup 中出现 `Delete VM` 操作或出现状态为 Active 的新 Azure 服务运行状况事件时。| [活动日志警报有效负载架构](../azure-monitor/alerts/activity-log-alerts-webhook.md)     |
|[准实时指标警报](../azure-monitor/alerts/alerts-metric-near-real-time.md) | 当一个或多个平台级指标满足指定条件时，就会以快于指标警报的速度发送通知。 例如，当 VM 的“CPU 百分比”大于 90 并且过去 5 分钟“网络传入”大于 500 MB 时。 | [准实时指标警报有效负载架构](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

由于每种警报提供的数据不同，因此需要以不同的方式处理每种警报。 下一部分将介绍如何创建 Runbook 来处理不同类型的警报。

## <a name="assign-permissions-to-managed-identities"></a>向托管标识分配权限

向适当的[托管标识](./automation-security-overview.md#managed-identities-preview)分配权限，以允许它们停止虚拟机。 Runbook 可使用自动化帐户的系统分配的托管标识或用户分配的托管标识。 已提供为每个标识分配权限的步骤。 以下步骤使用 PowerShell。 如果你偏向于使用门户，请参阅[使用 Azure 门户分配 Azure 角色](./../role-based-access-control/role-assignments-portal.md)。

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 以交互方式登录到 Azure，并按照说明进行操作。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. 为以下变量提供适当的值，然后执行脚本。

    ```powershell
    $resourceGroup = "resourceGroup"
    $automationAccount = "AutomationAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    ```

1. 使用 PowerShell cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 将角色分配给系统分配的托管标识。

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. 为用户分配的托管标识分配角色。

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity)
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. 对于系统分配的托管标识，请显示 `ClientId` 并记录值供稍后使用。

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-a-runbook-to-handle-alerts"></a>创建 Runbook 以处理警报

若要对警报使用自动化，需要有一个 Runbook，用于管理传递到 Runbook 的警报 JSON 有效负载。 下面的示例 Runbook 必须从 Azure 警报调用。

如前面部分所述，每种警报类型都有不同的架构。 此脚本采用从 `WebhookData` runbook 输入参数中的警报获取的 Webhook 数据。 然后，该脚本对 JSON 有效负载进行评估，确定正在使用的警报类型。

此示例使用来自 Azure 虚拟机 (VM) 的警报。 它从有效负载中检索 VM 数据，然后使用该信息停止运行 VM。 必须在运行该 Runbook 的自动化帐户中建立连接。 使用警报触发 runbook 时，必须检查触发的 runbook 中的警报状态。 每次警报更改状态时，都会触发 runbook。 警报有多个状态，其中两个最常见的状态为“已激活”和“已解决”。 检查 runbook 逻辑的状态，以确保 runbook 不会运行多次。 本文中的示例仅演示了如何查找状态为“已激活”的警报。

该 Runbook 使用自动化帐户的[系统分配的托管标识](./automation-security-overview.md#managed-identities-preview)向 Azure 进行身份验证，以便对 VM 执行管理操作。 可轻松修改 Runbook 以使用用户分配的托管标识。

使用此示例可以创建名为 **Stop-AzureVmInResponsetoVMAlert** 的 Runbook。 可以修改此 PowerShell 脚本，并将其用于许多不同的资源。

1. 登录 [Azure 门户](https://portal.azure.com/)，导航到你的自动化帐户。

1. 在“过程自动化”下，选择“Runbook”。 

1. 选择“+ 创建 Runbook”。
    1. 将 runbook 命名为 `Stop-AzureVmInResponsetoVMAlert`。
    1. 从“Runbook 类型”下拉列表中，选择“PowerShell” 。
    1. 选择“创建”。

1. 在 runbook 编辑器中粘贴以下代码：

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }
    
        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose
    
            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose
    
                # Ensures you do not inherit an AzContext in your runbook
                Disable-AzContextAutosave -Scope Process
     
                # Connect to Azure with system-assigned managed identity
                $AzureContext = (Connect-AzAccount -Identity).context
  
                # set and store context
                $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
    
                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -DefaultProfile $AzureContext -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

1. 如果希望 Runbook 使用系统分配的托管标识执行，请按原样保留代码。 如果希望使用用户分配的托管标识，则执行以下操作：
    1. 从第 78 行中删除 `$AzureContext = (Connect-AzAccount -Identity).context`，
    1. 将其替换为 `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`，然后
    1. 输入之前获取的客户端 ID。

1. 选择“保存”、“发布”，并在出现系统提示时选择“是”  。

1. 关闭“Runbook”页以返回“自动化帐户”页 。

## <a name="create-the-alert"></a>创建警报

警报使用操作组，操作组是警报触发的操作的集合。 Runbook 只是操作组包含的诸多操作之一。

1. 在自动化帐户中，选择“监视”下的“警报” 。

1. 选择“+ 新建预警规则”，以打开“创建警报规则”页。

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-alert-rule-portal.png" alt-text="“创建警报规则”页和子部分。":::

1. 在“范围”下，选择“编辑资源” 。 

1. 在“选择资源”页上，从“按资源类型筛选”下拉列表中选择“虚拟机”  。

1. 选中要监视的虚拟机旁边的复选框。 然后选择“完成”返回到“创建警报规则”页 。

1. 在 **条件** 下，选择 **添加条件**。

1. 在“选择信号”页上的搜索文本框中输入 `Percentage CPU`，然后从结果中选择“CPU 百分比” 。

1. 在“配置信号逻辑”页上的“阈值”下，输入用于测试的初始较低值，例如 `5` 。 确认警报按预期方式工作后，可以返回并更新此值。 然后选择“完成”返回到“创建警报规则”页 。

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-signal-logic-portal.png" alt-text="输入 CPU 百分比阈值。":::
 
1. 在“操作”下，依次选择“添加操作组”和“+创建操作组”  。

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-action-group-portal.png" alt-text="“创建操作组”页，其中的“基本信息”选项卡已打开。":::

1. 在“创建操作组”页上：
    1. 在“基本信息”选项卡上，输入操作组名称和显示名称  。
    1. 在“操作”选项卡上的“名称”文本框中，输入名称 。 然后从“操作类型”下拉列表中，选择“自动化 Runbook”打开“配置 Runbook”页  。
        1. 对于“Runbook 源”项，请选择“用户” 。  
        1. 从“订阅”下拉列表中，选择你的订阅。
        1. 从“自动化帐户”下拉列表中，选择你的自动化帐户。
        1. 从“Runbook”下拉列表中，选择“Stop-AzureVmInResponsetoVMAlert” 。
        1. 对于“启用常见警报架构”项，请选择“是” 。
        1. 选择“确定”返回到“创建操作组”页 。
        
            :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-runbook-portal.png" alt-text="使用值配置 Runbook 页。":::

    1. 选择“查看 + 创建”，然后选择“创建”返回到“创建警报规则”页  。

1. 在“警报规则详细信息”下，填写“警报规则名称”文本框 。

1. 选择“创建警报规则”。  可以在创建的[活动日志警报](../azure-monitor/alerts/activity-log-alerts.md)和[准实时警报](../azure-monitor/alerts/alerts-overview.md)中使用操作组。

## <a name="verification"></a>验证

确保你的 VM 正在运行。 导航到 Runbook“Stop-AzureVmInResponsetoVMAlert”并观察“最近的作业”列表中是否填充了信息 。 显示了某个已完成的作业后，选择该作业并查看输出。 另外，请检查 VM 是否已停止。

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/job-result-portal.png" alt-text="显示作业输出。":::


## <a name="next-steps"></a>后续步骤

* 如需了解启动 runbook 的各种方式，请参阅[启动 runbook](./start-runbooks.md)。
* 若要创建活动日志警报，请参阅[创建活动日志警报](../azure-monitor/alerts/activity-log-alerts.md)。
* 若要了解如何创建准实时警报，请参阅[在 Azure 门户中创建警报规则](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json)。

