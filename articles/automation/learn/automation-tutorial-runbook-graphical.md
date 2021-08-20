---
title: 在 Azure 自动化中创建图形 Runbook
description: 本教程指导如何在 Azure 自动化中创建、测试和发布图形 runbook。
services: automation
ms.subservice: process-automation
ms.date: 07/16/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e782fd046af23beb5c84c8e419cd5925db7230ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449032"
---
# <a name="tutorial-create-a-graphical-runbook"></a>教程：创建图形 Runbook

本教程指导在 Azure 自动化中创建 [图形 Runbook](../automation-runbook-types.md#graphical-runbooks)。 可以在 Azure 门户中使用图形编辑器创建和编辑图形 runbook 与图形 PowerShell 工作流 runbook。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建简单的图形 Runbook
> * 测试并发布 Runbook
> * 运行 Runbook 作业并跟踪其状态
> * 更新 Runbook 以使用 Runbook 参数和条件链接启动 Azure 虚拟机

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

* 带有 [Azure 运行方式帐户](../create-run-as-account.md)的[自动化帐户](../index.yml)，用来保存 runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
* 适用于自动化帐户的 PowerShell 模块 Az.Accounts 和 Az.Compute。  有关详细信息，请参阅[在 Azure 自动化中管理模块](../shared-resources/modules.md)。
* [Azure 虚拟机](../../virtual-machines/windows/quick-create-portal.md) (VM)。 由于需要停止并启动此虚拟机，因此它不应当是生产 VM。 VM 最初为“停止”状态。

## <a name="create-runbook"></a>创建 Runbook

首先创建一个将输出文本 `Hello World` 的简单 Runbook。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在 Azure 门户中，导航到自动化帐户。

1. 在“流程自动化”下选择“Runbook”，打开 runbook 页面。  

1. 选择“创建 runbook”打开“创建 runbook”页面。 

1. 将 runbook 命名为 `MyFirstRunbook-Graphical`。

1. 从“Runbook 类型”下拉菜单中，选择“图形”。 

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/create-graphical-runbook.png" alt-text="创建 runbook 输入页。":::

1. 选择“创建”以创建 runbook 并打开图形编辑器的“编辑图形 Runbook”页。 

## <a name="add-activities"></a>添加活动

编辑器左侧是“库控件”。 中心是“画布”。 右侧是“配置控件”。 使用“库控件”，可以选择要添加到 runbook 的活动。 你将添加 `Write-Output` cmdlet，以便从 Runbook 输出文本。

1. 在“库控件”的搜索字段中，键入 `Write-Output`。

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

1. 向下滚动到列表的底部。 右键单击“Write-Output”并选择“添加到画布”。 还可以选择 cmdlet 名称旁边的省略号图标 (...)，然后选择“添加到画布”。

1. 从“画布”中，选择“写入输出”活动。  此操作会打开可用于配置活动的“配置控件”页。

1. 从“配置控件”中，“标签”字段默认为该 cmdlet 的名称，但可将其更改为更易记的名称。  将其更改为 `Write Hello World to output`。

1. 选择“参数”为 cmdlet 参数提供值。

   某些 cmdlet 有多个参数集，你需要选择要使用的参数集。 在本例中，`Write-Output` 仅有一个参数集。

1. 从“活动参数配置”页中，选择 `INPUTOBJECT` 参数以打开“参数值”页。  使用此参数指定要发送到输出流的文本。

1. “数据源”下拉菜单中提供了可用于填充参数值的源。 在此菜单中选择“PowerShell 表达式”。

   可以使用来自另一个活动、自动化资产或 PowerShell 表达式之类的源的输出。 在本例中，输出内容只是 `Hello World`。 可以使用 PowerShell 表达式并指定一个字符串。

1. 在“表达式”文本框中，输入 `"Hello World"` 并选择“确定”两次以返回到图形编辑器。 

1. 选择“保存”以保存 runbook。

## <a name="test-the-runbook"></a>测试 Runbook

在发布 Runbook 使其可在生产中使用之前，应对其进行测试，确保其能正常运行。 测试 Runbook 时将运行其“草稿”版本，你可以通过交互方式查看其输出。

1. 从图形编辑器中，选择“测试窗格”以打开“测试”窗格。 

1. 选择“启动”以启动测试 。

   会创建一个 [runbook 作业](../automation-runbook-execution.md) 并且在窗格中显示其状态。 作业状态一开始为 `Queued`，表明作业正在等待云中的 Runbook 辅助角色变为可用状态。 在某个辅助角色认领该作业后，其状态会变为 `Starting`。 最后，当 Runbook 开始实际运行时，其状态会变为 `Running`。

   Runbook 作业完成后，“测试”窗格会显示其输出。 在本例中，你会看到 `Hello World`。

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-test-results.png" alt-text="Hello World runbook 输出。":::

1. 选择右上角的 X 关闭“测试”窗格并返回到图形编辑器。 

## <a name="publish-and-start-the-runbook"></a>发布和启动 Runbook

创建的 runbook 仍处于草稿模式，必须先发布，然后才能在生产中运行。 当发布 Runbook 时，可以用草稿版本覆盖现有的已发布版本。

1. 从图形编辑器中，选择“发布”以发布该 runbook，并在出现提示时选择“是” 。 将返回到“Runbook”概述页。

1. 从“Runbook”概述页中，“状态”值为“已发布”。  

1. 选择“启动”，并在出现提示时选择“是”启动 runbook，然后将打开“作业”页。  

   顶部的选项现在可用于：启动 runbook、计划将来的启动时间，或创建一个 [webhook](../automation-webhooks.md) 以便通过 HTTP 调用来启动 runbook。

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/published-status.png" alt-text="概述页和已发布状态。":::

1. 在“作业”页中，验证“状态”字段是否显示“已完成”。  

1. 选择“输出”即可看到显示了 `Hello World`。

1. 选择“所有日志”以查看 runbook 作业的流，并选择唯一项以打开“作业流详细信息”页。  应该只能看到 `Hello World`。

    “所有日志”可以显示 runbook 作业的其他流，例如“详细”流和“错误”（如果 runbook 向其中写入内容）。

1. 关闭“作业流详细信息”页，然后关闭“作业”页，返回到“Runbook 概述”页。  

1. 在“资源”下，选择“作业”以查看 runbook 的所有作业。  “作业”页列出了你的 Runbook 创建的所有作业。 由于只将该作业运行了一次，应该只会看到一个列出的作业。

1. 选择作业，以打开你在启动 runbook 时查看过的同一“作业”页。

1. 关闭“作业”页，然后从左侧菜单中选择“概述”。 

## <a name="create-variable-assets"></a>创建变量资产

现已测试并发布了 Runbook，但到目前为止尚未执行任何有用的操作来管理 Azure 资源。 在配置用于身份验证的 Runbook 之前，必须创建一个变量来保存订阅 ID，设置一个活动来进行身份验证，然后引用该变量。 包括对订阅上下文的引用，这样就可以轻松地处理多个订阅。

1. 从“概述”中，选择“订阅 ID”旁边的“复制到剪贴板”图标。  

1. 关闭“Runbook”页以返回“自动化帐户”页。 

1. 在“共享资源”下选择“变量” 。

1. 选择“添加变量”以打开“新建变量”页。 

1. 在“新建变量”页上，设置以下值：

    | 字段| 值|
    |---|---|
    |值|按 CTRL+V 粘贴订阅 ID。<kbd></kbd>|
    |名称 |输入 `AzureSubscriptionId`。|
    |类型|保留默认值“字符串”。|
    |已加密|保留默认值“否”。|

1. 选择“创建”以创建变量并返回到“变量”页。 

1. 在“流程自动化”下，选择“runbook”，然后选择 runbook“MyFirstrunbook-Graphical”。  

## <a name="add-authentication"></a>添加身份验证

现已创建一个变量来保存订阅 ID，接下来可以配置 Runbook，以使用订阅的运行方式凭据进行身份验证。 通过将 Azure 运行方式连接添加为资产来配置身份验证。 此外，必须将 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 和 [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) cmdlet 添加到画布。

> [!NOTE]
> 对于 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的别名。 这些别名不适用于图形 runbook。 图形 runbook 只能使用 `Connect-AzAccount`。

1. 在“Runbook”页中，选择“编辑”以返回到图形编辑器。 

1. 不再需要 `Write Hello World to output` 活动。 选择活动，活动右上角会显示一个省略号。 省略号可能很难看到。 选择省略号，然后选择“删除”。

1. 从“库控件”中，导航到“资产” > “连接” > “AzureRunAsConnection”   。 选择省略号，然后选择“添加到画布”。

1. 在“配置控件”中，将“标签”值从 `Get-AutomationConnection` 更改为 `Get Run As Connection`。 

1. 从“库控件”的搜索字段中，输入 `Connect-AzAccount`。

1. 将 `Connect-AzAccount` 添加到画布，然后将活动拖动到 `Get Run As Connection` 下方。

1. 将鼠标悬停在 `Get Run As Connection` 上方，直到在该形状的底部显示一个圆圈。 选择并按住圆圈，将显示一个箭头。 将箭头拖到 `Connect-AzAccount`，以构成一个链接。 Runbook 首先运行“`Get Run As Connection`”，然后运行 `Connect-AzAccount`。

    ![创建活动之间的链接](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

1. 从“画布”中选择 `Connect-AzAccount`。

1. 在“配置控件”中，将“标签”从 `Connect-AzAccount` 更改为 `Login to Azure`。 

1. 选择“参数”以打开“活动参数配置”页。 

1. `Connect-AzAccount` cmdlet 有多个参数集，需要选择其中一个，然后提供参数值。 选择“参数集”，然后选择“ServicePrincipalCertificateWithSubscriptionId” 。 请注意不要选择 ServicePrincipalCertificateFileWithSubscriptionId，因为名称相似

   此参数集的参数会显示在“活动参数配置”页上。

    ![添加 Azure 帐户参数](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

1. 选择“CERTIFICATETHUMBPRINT”以打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“活动输出”。 
    1. 在“选择数据”中，选择“获取运行方式连接”。 
    1. 在“字段路径”文本框中，输入 `CertificateThumbprint`。
    1. 选择“确定”以回到“活动参数配置”页。 

1. 选择“SERVICEPRINCIPAL”以打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“常量值”。 
    1. 选择“True”选项。
    1. 选择“确定”以回到“活动参数配置”页。 

1. 选择“TENANT”以打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“活动输出”。 
    1. 在“选择数据”中，选择“获取运行方式连接”。 
    1. 在“字段路径”文本框中，输入 `TenantId`。
    1. 选择“确定”以回到“活动参数配置”页。 

1. 选择“APPLICATIONID”以打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“活动输出”。 
    1. 在“选择数据”中，选择“获取运行方式连接”。 
    1. 在“字段路径”文本框中，输入 `ApplicationId`。
    1. 选择“确定”以回到“活动参数配置”页。 

1. 选择“确定”，返回到图形编辑器。

1. 从“库控件”的搜索字段中，输入 `Set-AzContext`。

1. 将 `Set-AzContext` 添加到画布，然后将活动拖动到 `Login to Azure` 下方。

1. 在“配置控件”中，将“标签”从 `Set-AzContext` 更改为 `Specify Subscription Id`。 

1. 选择“参数”以打开“活动参数配置”页。 

1. `Set-AzContext` cmdlet 有多个参数集，需要选择其中一个，然后提供参数值。 单击“参数集”，然后选择“订阅”。  此参数集的参数会显示在“活动参数配置”页上。

1. 选择“SUBSCRIPTION”以打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“变量资产”。 
    1. 从变量列表中，选择“AzureSubscriptionId”。
    1. 选择“确定”以回到“活动参数配置”页。 

1. 选择“确定”，返回到图形编辑器。

1. 在 `Login to Azure` 和 `Specify Subscription Id` 之间形成链接。 此时，Runbook 看起来应该如下所示。

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="将箭头拖到“指定订阅 ID”后 Runbook 的屏幕截图。":::

## <a name="add-activity-to-start-a-virtual-machine"></a>添加用于启动虚拟机的活动

现在，必须添加一个 `Start-AzVM` 活动来启动虚拟机。 可以选取 Azure 订阅中的任何 VM，但暂时请在 [Start-AzVM](/powershell/module/az.compute/start-azvm) cmdlet 中对 VM 名称进行硬编码。

1. 从“库控件”的搜索字段中，输入 `Start-AzVM`。

1. 将 `Start-AzVM` 添加到画布，然后将活动拖动到 `Specify Subscription Id` 下方。

1. 从“配置控件”中，选择“参数”以打开“活动参数配置”页。  

1. 选择“参数集”，然后选择“ResourceGroupNameParameterSetName”。  此参数集的参数会显示在“活动参数配置”页上。 “RESOURCEGROUPNAME”和“NAME”参数旁边出现了感叹号，表示它们是必需的参数。  这两个字段都需要字符串值。

1. 选择 RESOURCEGROUPNAME 打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“PowerShell 表达式”。 
    1. 在“表达式”文本框中，输入资源组的名称并用双引号括起。
    1. 选择“确定”以回到“活动参数配置”页。 

1. 选择“NAME”以打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“PowerShell 表达式”。 
    1. 在“表达式”文本框中，输入虚拟机的名称并用双引号括起。
    1. 选择“确定”以回到“活动参数配置”页。 

1. 选择“确定”，返回到图形编辑器。

1. 在 `Specify Subscription Id` 和 `Start-AzVM` 之间形成链接。 此时，Runbook 看起来应该如下所示。

    ![Runbook Start-AzVM 输出](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

1. 选择“测试窗格”，以便测试 runbook。

1. 选择“启动”，开始测试。

1. 完成后，请确保该 VM 已启动。 然后停止 VM 以执行后续步骤。

1. 返回到 runbook 的图形编辑器。

## <a name="add-input-parameters"></a>添加输入参数

Runbook 当前会启动资源组中为 `Start-AzVM` cmdlet 指定的 VM。 如果在 Runbook 启动时指定了 VM 名称和资源组，则该 Runbook 会更有用。 让我们向 Runbook 中添加输入参数，以提供该功能。

1. 在图形编辑器的顶部菜单栏中，选择“输入和输出”。

1. 选择“添加输入”，打开“Runbook 输入参数”页 。

1. 在“Runbook 输入参数”页上，设置以下值：

    | 字段| 值|
    |---|---|
    |名称| 输入 `VMName`。|
    |类型|保留默认值“字符串”。|
    |必需|将值更改为“是”。|

1. 选择“确定”以返回到“输入和输出”页 

1. 选择“添加输入”以重新打开“Runbook 输入参数”页 。

1. 在“Runbook 输入参数”页上，设置以下值：

    | 字段| 值|
    |---|---|
    |名称| 输入 `ResourceGroupName`。|
    |类型|保留默认值“字符串”。|
    |必需|将值更改为“是”。|

1. 选择“确定”以返回到“输入和输出”页。  此页可能如下所示：

    ![Runbook 输入参数](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

1. 选择“确定”，返回到图形编辑器。

1. 新输入可能无法立即可供使用。 选择“保存”，关闭图形编辑器，然后重新打开图形编辑器。 现在应该可以使用新输入了。

1. 选择 `Start-AzVM` 活动，然后选择“参数”以打开“活动参数配置”页。 

1. 对于之前配置的参数“RESOURCEGROUPNAME”，将“数据源”更改为“Runbook 输入”，然后选择“ResourceGroupName”。    选择“确定”。

1. 对于之前配置的参数“NAME”，将“数据源”更改为“Runbook 输入”，然后选择“VMName”。    选择“确定”。 此页可能如下所示：

    ![Start-AzVM 参数](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

1. 选择“确定”，返回到图形编辑器。

1. 选择“保存”，然后选择“测试窗格”。  请注意，现在可以为你创建的两个输入变量提供值。

1. 关闭“测试”页以返回到图形编辑器。

1. 当系统提示发布 runbook 的新版本时，选择“发布”，然后选择“是”。  将返回到“Runbook”概述页。

1. 选择“启动”以打开“启动 Runbook”页。 

1. 为 `VMNAME` 和 `RESOURCEGROUPNAME` 参数输入适当的值。 然后选择“确定”。  然后将打开“作业”页。

1. 监视作业，并验证 VM 在“状态”变为“完成”后启动。  然后停止 VM 以执行后续步骤。

1. 返回到 runbook 的图形编辑器。

## <a name="create-a-conditional-link"></a>创建条件链接

现在可以修改该 runbook，使之仅在 VM 尚未启动的情况下尝试启动该 VM。 为此，可以添加一个 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) cmdlet 来检索 VM 的实例级状态。 然后，可以添加名为 `Get Status` 的 PowerShell 工作流代码模块，并使用 PowerShell 代码片段来确定 VM 状态是“正在运行”还是“已停止”。 仅当当前运行状态为“已停止”时，`Get Status` 模块中的条件链接才运行 `Start-AzVM`。 在此过程的最后，Runbook 将使用 `Write-Output` cmdlet 输出一条消息，告知 VM 是否已成功启动。

1. 在图形编辑器中，右键单击 `Specify Subscription Id` 和 `Start-AzVM` 之间的链接，然后选择“删除”。

1. 从“库控件”的搜索字段中，输入 `Get-AzVM`。

1. 将 `Get-AzVM` 添加到画布，然后将活动拖动到 `Specify Subscription Id` 下方。

1. 从“配置控件”中，选择“参数”以打开“活动参数配置”页。  

   选择“参数集”，然后选择“GetVirtualMachineInResourceGroupParamSet”。  此参数集的参数会显示在“活动参数配置”页上。 “RESOURCEGROUPNAME”和“NAME”参数旁边出现了感叹号，表示它们是必需的参数。  这两个字段都需要字符串值。

1. 选择 RESOURCEGROUPNAME 打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“Runbook 输入”。 
    1. 选择参数“ResourceGroupName”。
    1. 选择“确定”以回到“活动参数配置”页。 

1. 选择“NAME”以打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“Runbook 输入”。 
    1. 选择“VMName”参数。
    1. 选择“确定”以回到“活动参数配置”页。 

1. 选择“STATUS”以打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“常量值”。 
    1. 选择“True”选项。
    1. 选择“确定”以回到“活动参数配置”页。 

1. 选择“确定”，返回到图形编辑器。

1. 在 `Specify Subscription Id` 和 `Get-AzVM` 之间形成链接。

1. 清除“库控件”搜索字段，然后导航到“RUNBOOK 控件” > “代码”。   选择省略号，然后选择“添加到画布”。 将活动拖动到 `Get-AzVM` 下方。

1. 在“配置控件”中，将“标签”从 `Code` 更改为 `Get Status`。 

1. 从“配置控件”中选择“代码”以打开“代码编辑器”页。  

1. 将以下代码片段粘贴到“PowerShell 代码”文本框中。

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

1. 选择“确定”，返回到图形编辑器。

1. 在 `Get-AzVM` 和 `Get Status` 之间形成链接。

1. 在 `Get Status` 和 `Start-AzVM` 之间形成链接。 此时，Runbook 看起来应该如下所示。

    ![使用代码模块的 Runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

1. 选择 `Get Status` 和 `Start-AzVM` 之间的链接。

1. 从“配置控件”中，将“应用条件”更改为“是”。   该链接将变为虚线，表示仅在条件解析为 True 时才会运行目标活动。  

1. 对于“条件表达式”，请输入 `$ActivityOutput['Get Status'] -eq "Stopped"`。 现在，仅当 VM 已停止时，`Start-AzVM` 才会运行。

1. 从“库控件”的搜索字段中，输入 `Write-Output`。

1. 将 `Write-Output` 添加到画布，然后将活动拖动到 `Start-AzVM` 下方。

1. 选择活动省略号，然后选择“复制”。 将复制的活动拖到第一个活动的右侧。

1. 选择第一个 `Write-Output` 活动。
    1. 在“配置控件”中，将“标签”从 `Write-Output` 更改为 `Notify VM Started`。 
    1. 选择“参数”以打开“活动参数配置”页。 
    1. 选择“INPUTOBJECT”以打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“PowerShell 表达式”。 
    1. 在“表达式”文本框中，输入 `"$VMName successfully started."`。
    1. 选择“确定”以回到“活动参数配置”页。 
    1. 选择“确定”，返回到图形编辑器。

1. 选择第一个 `Write-Output1` 活动。
    1. 在“配置控件”中，将“标签”从 `Write-Output1` 更改为 `Notify VM Start Failed`。 
    1. 选择“参数”以打开“活动参数配置”页。 
    1. 选择“INPUTOBJECT”以打开“参数值”页。 
    1. 从“数据源”下拉菜单中，选择“PowerShell 表达式”。 
    1. 在“表达式”文本框中，输入 `"$VMName could not start."`。
    1. 选择“确定”以回到“活动参数配置”页。 
    1. 选择“确定”，返回到图形编辑器。

1. 在 `Start-AzVM` 和 `Notify VM Started` 之间形成链接。

1. 在 `Start-AzVM` 和 `Notify VM Start Failed` 之间形成链接。

1. 选择“`Notify VM Started`”的链接并将“应用条件”更改为“是”。 

1. 对于“条件表达式”，请键入 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`。 现在，只有在 VM 已成功启动的情况下，此 `Write-Output` 控件才会运行。

1. 选择 `Notify VM Start Failed` 的链接。

1. 在“控制”页中，对于“应用条件”，选择“是”。  

1. 在“条件表达式”文本框中，输入 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`。 现在，只有在 VM 未成功启动的情况下，此 `Write-Output` 控件才会运行。 Runbook 看起来应如下图所示。

    ![使用 Write-Output 的 Runbook](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

1. 保存 Runbook 并打开“测试”窗格。

1. 在 VM 已停止的情况下启动该 Runbook，此时该计算机应会启动。

## <a name="next-steps"></a>后续步骤

* 若要详细了解图形创作，请参阅[在 Azure 自动化中创作图形](../automation-graphical-authoring-intro.md)。
* 若要开始使用 PowerShell Runbook，请参阅[创建 PowerShell Runbook](automation-tutorial-runbook-textual-powershell.md)。
* 若要开始使用 PowerShell 工作流 Runbook，请参阅[创建 PowerShell 工作流 Runbook](automation-tutorial-runbook-textual.md)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation)。
