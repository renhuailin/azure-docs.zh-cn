---
title: 教程 - 在 Azure 自动化中创建 PowerShell 工作流 Runbook
description: 本教程介绍如何创建、测试和发布 PowerShell 工作流 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e1550caff2fbd28a08e89c3fa570216ff8002430
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057693"
---
# <a name="tutorial-create-a-powershell-workflow-runbook-in-automation"></a>教程：在自动化中创建 PowerShell 工作流 Runbook

本教程指导在 Azure 自动化中创建 [PowerShell 工作流 Runbook](../automation-runbook-types.md#powershell-workflow-runbooks)。 PowerShell 工作流 Runbook 是基于 Windows PowerShell 工作流的文本 Runbook。 可以在 Azure 门户中使用文本编辑器创建和编辑 Runbook 的代码。 

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 PowerShell 工作流 Runbook
> * 测试并发布 Runbook
> * 运行 Runbook 作业并跟踪其状态
> * 添加身份验证来管理 Azure 资源
> * 更新 Runbook 参数以启动 Azure 虚拟机

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 至少具有一个用户分配的托管标识的 Azure 自动化帐户。 有关详细信息，请参阅[启用托管标识](../quickstarts/enable-managed-identity.md)。
* 导入到自动化帐户中的 Az 模块：`Az.Accounts` 和 `Az.Compute`。 有关详细信息，请参阅[导入 Az 模块](../shared-resources/modules.md#import-az-modules)。
* 两个或更多个 [Azure 虚拟机](../../virtual-machines/windows/quick-create-powershell.md)。 这些计算机会被停止和启动，所以它们不应是生产 VM。
* 在计算机上安装 [Azure Az PowerShell 模块](/powershell/azure/new-azureps-module-az)。 若要安装或升级，请参阅[如何安装 Azure Az PowerShell 模块](/powershell/azure/install-az-ps)。

## <a name="assign-permissions-to-managed-identities"></a>向托管标识分配权限

向适当的[托管标识](../automation-security-overview.md#managed-identities-preview)分配权限，以允许它们停止虚拟机。 Runbook 可使用自动化帐户的系统分配的托管标识或用户分配的托管标识。 已提供为每个标识分配权限的步骤。 以下步骤使用 Azure 门户。 如果希望使用 PowerShell，请参阅[使用 Azure PowerShell 分配 Azure 角色](../../role-based-access-control/role-assignments-powershell.md)。

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到自动化帐户。

1. 在“帐户设置”下选择“标识(预览)” 。

1. 在“系统分配”选项卡中，选择“权限”下的“Azure 角色分配”，打开“Azure 角色分配”页面   。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-role-assignments-portal.png" alt-text="在门户中选择 Azure 角色分配。":::

1. 选择“+ 添加角色分配(预览)”，打开“添加角色分配(预览)”页面 。 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-add-role-assignment-portal.png" alt-text="在门户中添加角色分配。":::

1. 选择适当的值。

   |属性 |说明 |
   |---|---|
   |作用域| 范围是角色分配应用到的资源集。 从下拉列表中选择“资源组”。|
   |订阅|此字段应自动填充为你的订阅。|
   |资源组|在下拉列表中，选择要授予标识权限的资源组。|
   |角色|从下拉列表中选择“DevTest Labs 用户”。|

1. 选择“保存”，然后关闭“Azure 角色分配”页面，回到“系统分配”选项卡  。

1. 选择“用户分配”选项卡。

1. 从列表中选择用户分配的托管标识，打开“托管标识”页面。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/select-user-assigned-identity-portal.png" alt-text="在门户中选择用户分配的托管标识。":::

1. 记下“客户端 ID”供以后使用。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/managed-identity-client-id-portal.png" alt-text="在门户中显示托管标识的客户端 ID":::

1. 在左侧菜单中选择“Azure 角色分配”，然后选择“+ 添加角色分配(预览)”，打开“添加角色分配(预览)”页面  。 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/user-assigned-add-role-assignment-portal.png" alt-text="在门户中为用户分配的标识添加角色分配。":::

1. 选择适当的值。

   |属性 |说明 |
   |---|---|
   |作用域| 从下拉列表中选择“资源组”。|
   |订阅|此字段应自动填充为你的订阅。|
   |资源组|在下拉列表中，选择要授予标识权限的资源组。|
   |角色|从下拉列表中选择“DevTest Labs 用户”。|

1. 选择“保存”，然后关闭“Azure 角色分配”页面，回到“用户分配”选项卡  。

## <a name="create-new-runbook"></a>创建新的 Runbook

首先，创建一个简单的 [PowerShell 工作流 Runbook](../automation-runbook-types.md#powershell-workflow-runbooks)。 Windows PowerShell 工作流的一个优点是能够与典型脚本一样并行而不是按顺序执行一组命令

1. 在打开的自动化帐户页面中，选择“流程自动化”下的“Runbook” 

1. 选择“+ 创建 Runbook”。
    1. 将 runbook 命名为 `MyFirstRunbook-Workflow`。
    1. 从“Runbook 类型”下拉菜单中，选择“PowerShell 工作流” 。
    1. 选择“创建”。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/create-powershell-workflow-runbook.png" alt-text="在门户中创建 PowerShell 工作流 Runbook":::

## <a name="add-code-to-the-runbook"></a>将代码添加到 Runbook

可以直接将代码键入 Runbook 中，或者从“库”控件中选择 cmdlet、Runbook 和资产，并使用任何相关的参数将它们添加到 Runbook。 在本教程中，我们将代码直接键入 Runbook。

Runbook 目前为空，只有必需的 `Workflow` 关键字、Runbook 名称以及括住整个工作流的大括号。

```powershell
Workflow MyFirstRunbook-Workflow
{
}
```

1. 你可以使用 `Parallel` 关键字创建一个包含将并发运行的多个命令的脚本块。 在大括号之间输入以下代码：

   ```powershell
   Parallel {
        Write-Output "Parallel"
        Get-Date
        Start-Sleep -s 3
        Get-Date
    }

   Write-Output " `r`n"
   Write-Output "Non-Parallel"
   Get-Date
   Start-Sleep -s 3
   Get-Date  
   ```

1. 选择“保存”来保存 Runbook。

## <a name="test-the-runbook"></a>测试 Runbook

在发布 Runbook 使其可在生产中使用之前，应对其进行测试，确保其能正常运行。 测试 Runbook 时将运行其“草稿”版本，你可以通过交互方式查看其输出。

1. 选择“测试窗格”来打开“测试”页 。

1. 选择“启动”以启动测试 。  这会创建一个 [runbook 作业](../automation-runbook-execution.md) 并且在窗格中显示其状态。

   作业状态一开始为“排队”，表明作业正在等待云中的 Runbook 辅助角色变为可用状态。 在某个辅助角色认领该作业后，其状态会变为“正在启动”。 最后，当 Runbook 开始实际运行时，其状态会变为“正在运行”。

1. Runbook 作业完成后，“测试”窗格会显示其输出。 输出内容应与下图类似：

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-parallel-output.png" alt-text="PowerShell 工作流 Runbook 并行输出":::

   查看输出。 同时执行 `Parallel` 块中的所有内容（包括 `Start-Sleep` 命令）。 `Parallel` 块外部的相同命令按顺序运行，如不同的日期时间戳所示。 

1. 关闭“测试”页返回到画布。

## <a name="publish-and-start-the-runbook"></a>发布和启动 Runbook

你创建的 Runbook 仍处于“草稿”模式。 必须先发布它，然后才能在生产中运行它。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。 在本例中，还没有已发布版本，因为刚刚创建 Runbook。

1. 选择“发布”以发布该 Runbook，并在出现提示时选择“是” 。

1. “状态”字段现在显示的是“已发布” 。 查看顶部的选项，可通过它们立即启动 Runbook、计划将来的启动时间，或者创建一个 [Webhook](../automation-webhooks.md) 来通过 HTTP 调用启动 Runbook。 选择“启动”，并在出现提示时选择“是” 启动 runbook。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-overview.png" alt-text="PowerShell 工作流 Runbook 概述页面":::

1. 此时会为已创建的 Runbook 作业打开一个“作业”页面。 在此示例中，请让页面保持打开状态，以便观察作业的进度。 “状态”字段与测试 Runbook 时看到的状态相符。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-page-overview.png" alt-text="Runbook 作业页的屏幕截图。":::

1. 在 runbook 状态显示为“已完成”后，选择“输出” 。 输出内容应与测试输出类似。

1. 关闭“作业”页，返回到 Runbook 概述页面 。

1. 选择“资源”下的“作业” 。 此页列出你的 Runbook 创建的所有作业。 由于只将该作业运行了一次，应该只会看到一个列出的作业。

1. 选择作业，以打开你在启动 runbook 时查看过的同一“作业”页。 使用此页面可查看为 Runbook 创建的任何作业的详细信息。 关闭“作业”页，返回到 Runbook 概述页面 。

## <a name="add-authentication-to-manage-azure-resources"></a>添加身份验证来管理 Azure 资源

已经测试并发布 Runbook，但到目前为止它不执行任何有用的操作。 需要让其管理 Azure 资源。 除非它使用该订阅的凭据进行身份验证，否则它不能执行该操作。 该 Runbook 使用自动化帐户的系统分配的托管标识向 Azure 进行身份验证，以便对 VM 执行管理操作。 可轻松修改 Runbook 以使用用户分配的托管标识。

1. 选择“概述”，然后选择“编辑”以打开文本编辑器 。

1. 将所有现有代码替换为以下内容：

   ```powershell
   workflow MyFirstRunbook-Workflow
   {
   $resourceGroup = "resourceGroupName"
    
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process
    
   # Connect to Azure with system-assigned managed identity
   $AzureContext = (Connect-AzAccount -Identity).context
    
   # set and store context
   $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
   }
   ```

   使用表示资源组的有效值编辑 `$resourceGroup` 变量。

1. 如果希望 Runbook 使用系统分配的托管标识执行，请按原样保留代码。 如果希望使用用户分配的托管标识，则执行以下操作：
    1. 从第 9 行中删除 `$AzureContext = (Connect-AzAccount -Identity).context`，
    1. 将其替换为 `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`，然后
    1. 输入之前获取的客户端 ID。

1. 选择“保存”，然后选择“测试窗格” 。

1. 选择“启动”以启动测试 。 完成后，你应该会看到类似以下内容的输出，它显示了帐户的基本信息。 此操作确认凭据有效。

   :::image type="content" source="../media/automation-tutorial-runbook-textual/runbook-auth-output.png" alt-text="确认凭据的基本信息。":::

1. 关闭“测试”页返回到画布。

## <a name="add-code-to-start-a-virtual-machine"></a>添加代码以启动虚拟机

现在，你的 Runbook 向 Azure 订阅进行身份验证，你可以管理资源。 添加一个命令以启动虚拟机。 你可以选取你的 Azure 订阅中的任何 VM，但现在，你将在 Runbook 中将该名称硬编码。 

1. 将下面的代码添加为恰好在右大括号前面的最后一行。 将 `VMName` 替换为 VM 的实际名称。 

   ```powershell
   Start-AzVM -Name "VMName" -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. 测试 Runbook 并确认 VM 已启动。 然后返回到画布。

## <a name="add-input-parameters-to-the-runbook"></a>向 Runbook 添加输入参数

Runbook 当前启动的是已在 Runbook 中硬编码的 VM。 如果可以在启动 Runbook 时指定 VM，它会更有用。 将输入参数添加到 Runbook，以提供该功能。

1. 请使用以下代码替换第 3 行 (`$resourceGroup = "resourceGroupName"`)：

    ```powershell
    Param(
        [string]$resourceGroup,
        [string]$VMName
    )
   ```

1. 使用以下内容替换之前的 `Start-AzVM` 命令：

   ```powershell
   Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. 测试 Runbook 并确认 VM 已启动。 然后返回到画布。

## <a name="manage-multiple-vms-simultaneously"></a>同时管理多个 VM

可以使用 `ForEach -Parallel` 构造同时处理集合中每个项的命令。 修改代码，以便 Runbook 现在：
- 接受虚拟机名称的集合，
- 接受参数以停止或启动虚拟机，并且
- 针对所有虚拟机并行执行操作

1. 将所有现有代码替换为以下内容：

    ```powershell
    workflow MyFirstRunbook-Workflow
    {
    Param(
        [string]$resourceGroup,
        [string[]]$VMs,
        [string]$action
    )
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
    
    # Start or stop VMs in parallel
    if($action -eq "Start")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Start-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
            }
        }
    elseif ($action -eq "Stop")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Stop-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
            }
        }
    else {
            Write-Output "`r`n Action not allowed. Please enter 'stop' or 'start'."
        }
    }
    ```

1. 如果希望 Runbook 使用系统分配的托管标识执行，请按原样保留代码。 如果希望使用用户分配的托管标识，则执行以下操作：
    1. 从第 13 行中删除 `$AzureContext = (Connect-AzAccount -Identity).context`，
    1. 将其替换为 `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`，然后
    1. 输入之前获取的客户端 ID。

1. 选择“保存”，然后选择“发布”，并在出现系统提示时选择“是”  。

1. 在概述页面中选择“启动” 。

1. 填充参数，然后选择“确定”。

   |参数 |说明 |
   |---|---|
   |RESOURCEGROUP|输入 VM 的资源组的名称。|
   |VM|使用下列语法输入虚拟机的名称：`["VM1","VM2","VM3"]`|
   |操作|输入 `stop` 或 `start`。|

1. 导航到虚拟机列表，且每隔几秒钟刷新一次页面。 注意观察，每个 VM 的操作是并行发生的。 如果没有 `-Parallel` 关键字，这些操作将按顺序执行。 虽然 VM 将按顺序启动，但根据每个 VM 的特征，各个 VM 进入“正在运行”阶段的时间可能会略有不同。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此 Runbook，请按照以下步骤将其删除：

1. 导航到自动化帐户。
1. 在“过程自动化”下，选择“Runbook”。 
1. 选择 Runbook。
1. 在 Runbook 概述页面中，选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个 PowerShell 工作流 runbook。 要查看 Python 3 runbook，请参阅：

> [!div class="nextstepaction"]
> [教程：创建 Python 3 runbook（预览版）](automation-tutorial-runbook-textual-python-3.md)