---
title: 在 Azure 自动化中创建模块化 Runbook
description: 本文介绍如何创建由另一个 Runbook 调用的 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 11bd439b511f6ae60078f5006a1ff4571b5908e9
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617257"
---
# <a name="create-modular-runbooks-in-automation"></a>在自动化中创建模块化 Runbook

在 Azure 自动化中，一种良好的做法是编写可重用的模块化 Runbook，其中使用由其他 Runbook 调用的离散函数。 父 Runbook 通常会调用一个或多个子 Runbook 来执行所需的功能。 

可通过两种方式调用子 Runbook：内联方式或通过 cmdlet。 下表汇总了这两种方式的差异，以帮助确定哪种方式更适合你的方案。

|  | 内联 | Cmdlet |
|:--- |:--- |:--- |
| **作业** |子 Runbook 在父级所在的同一个作业中运行。 |为子 Runbook 创建单独的作业。 |
| **执行** |父 Runbook 等待子 Runbook 完成，然后再继续。 |父 Runbook 会在子 Runbook 启动后立刻继续运行，或父 Runbook 会等待子作业完成。 |
| **输出** |父 Runbook 可以直接从子 Runbook 获取输出。 |父 Runbook 必须检索子 Runbook 作业的输出，或父 Runbook 可以直接从子 Runbook 获取输出。 |
| **Parameters** |子 Runbook 参数的值需单独指定，并且可以使用任意数据类型。 |子 Runbook 参数的值必须组合成单个哈希表。 此哈希表只能包含简单数据类型、数组和利用 JSON 序列化的对象数据类型。 |
| **自动化帐户** |父 Runbook 只能使用同一自动化帐户中的子 Runbook。 |父 Runbook 可以使用同一 Azure 订阅甚至（已连接的）不同订阅中的任何自动化帐户内的子 Runbook。 |
| **发布** |在发布父 Runbook 之前必须先发布子 Runbook。 |子 Runbook 在父 Runbook 启动前的任意时间发布。 |

## <a name="call-a-child-runbook-by-using-inline-execution"></a>使用内联执行调用子 Runbook

若要从另一个 Runbook 调用某个内联 Runbook，请使用被调用 Runbook 的名称并提供其参数值，就像使用活动或 cmdlet 时一样。 同一自动化帐户中的所有 Runbook 可按此方式相互使用。 父 Runbook 等待子 Runbook 完成，然后转移到下一行，并直接向父级返回任何输出。

在调用某个内联 Runbook 时，它会在与父 Runbook 所在的同一个作业中运行。 在子 Runbook 的作业历史记录中，没有相应的指示。 子 Runbook 发生的任何异常和任何流输出都将与父级关联。 此行为导致作业更少，并使它们更易于跟踪和故障排除。

发布某个 Runbook 时，必须事先发布它所调用的任何子 Runbook。 原因是在编译 Runbook 时，Azure 自动化会生成与任何子 Runbook 的关联。 如果尚未发布子 Runbook，则父 Runbook 看似会正确发布，但会在启动时生成异常。 

如果遇到异常，可以重新发布父 Runbook，以正确引用子 Runbook。 如果由于已创建关联而更改了任何子 Runbook，无需重新发布父 Runbook。

内联调用的子 Runbook 的参数可以是任意数据类型，包括复杂对象。 不会进行 [JSON 序列化](start-runbooks.md#work-with-runbook-parameters)，因为在使用 Azure 门户或 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet 启动 Runbook 时会进行这种序列化。

### <a name="runbook-types"></a>Runbook 类型

只有特定的 Runbook 类型可以互相调用：

* [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和[图形 Runbook](automation-runbook-types.md#graphical-runbooks) 可以互相内联调用，因为两者都基于 PowerShell。
* [PowerShell 工作流 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 和图形 PowerShell 工作流 Runbook 可以互相内联调用，因为两者都基于 PowerShell 工作流。
* PowerShell 类型和 PowerShell 工作流类型不能互相内联调用。 它们必须使用 `Start-AzAutomationRunbook`。

Runbook 的发布顺序仅对于 PowerShell 工作流和图形 PowerShell 工作流 Runbook 重要。

Runbook 在通过内联执行调用图形或 PowerShell 工作流子 Runbook 时，只需使用 Runbook 的名称。 名称必须以 `.\\` 开头以指定脚本位于本地目录。

### <a name="example"></a>示例

以下示例将启动一个测试子 Runbook，该 Runbook 接受一个复杂对象、一个整数值和一个布尔值。 该子 Runbook 的输出将分配到某个变量。 在本示例中，子 Runbook 是 PowerShell 工作流 Runbook。

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

下面是同一示例，但它使用 PowerShell Runbook 作为子项。

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-by-using-a-cmdlet"></a>使用 cmdlet 启动子 Runbook

> [!IMPORTANT]
> 如果 Runbook 结合 `Wait` 参数使用 `Start-AzAutomationRunbook` cmdlet 调用子 Runbook，并且子 Runbook 生成对象结果，则操作可能会遇到错误。 若要解决此错误，请参阅[具有对象输出的子 Runbook](troubleshoot/runbooks.md#child-runbook-object)。 该文章介绍了如何使用 [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) cmdlet 来实现用于轮询结果的逻辑。

可按[使用 Windows PowerShell 启动 Runbook](start-runbooks.md#start-a-runbook-with-powershell) 中所述，使用 `Start-AzAutomationRunbook` 启动 Runbook。 此 cmdlet 有两种使用模式：

- cmdlet 在子 Runbook 的作业创建时返回作业 ID。 
- cmdlet 等待子作业完成，然后返回子 Runbook 的输出。 脚本通过指定 `Wait` 参数启用此模式。

使用 cmdlet 启动的子 Runbook 的作业独立于父 Runbook 作业运行。 此行为会导致生成比启动内联 Runbook 时更多的作业，并使这些作业更难以跟踪。不过，父级可以异步启动多个子 Runbook，而无需等待每个子 Runbook 完成。 对于调用内嵌子 Runbook 的并行执行，父 Runbook 必须使用[并行关键字](automation-powershell-workflow.md#use-parallel-processing)。

由于计时原因，子 Runbook 的输出不会可靠地返回到父 Runbook。 另外，`$VerbosePreference`、`$WarningPreference` 和其他变量可能不会传播到子 Runbook。 为避免这些问题，可以结合 `Wait` 参数使用 `Start-AzAutomationRunbook` 将子 Runbook 作为单独的自动化作业调用。 此方法会阻止父 Runbook，直到子 Runbook 完成。

如果你不希望父 Runbook 在等待时被阻止，可以使用不带 `Wait` 参数的 `Start-AzAutomationRunbook` 启动子 Runbook。 在这种情况下，Runbook 必须使用 [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) 以等待作业完成。 它还必须使用 [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) 和 [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) 来检索结果。

使用 cmdlet 启动的子 Runbook 的参数以哈希表形式提供，如 [Runbook 参数](start-runbooks.md#work-with-runbook-parameters)中所述。 只能使用简单数据类型。 如果 Runbook 的参数使用复杂数据类型，则必须内联调用该 Runbook。

将子 Runbook 作为单独作业启动时，可能会丢失订阅上下文。 要使子 Runbook 针对特定的 Azure 订阅执行 Az 模块 cmdlets，子 Runbook 必须独立于父 Runbook 对此订阅进行身份验证。

如果同一自动化帐户中的作业使用多个订阅，则选择一个作业中的订阅可能也会更改当前所选的其他作业的订阅上下文。 若要避免此情况，请在每个 Runbook 的开头使用 `Disable-AzContextAutosave -Scope Process`。 此操作仅保存该 Runbook 执行的上下文。

### <a name="example"></a>示例

以下示例将启动一个包含参数的子 Runbook，然后结合 `Wait` 参数使用 `Start-AzAutomationRunbook` cmdlet 等待其完成。 在子 Runbook 完成后，该示例将从子 Runbook 收集 cmdlet 输出。 要使用 `Start-AzAutomationRunbook`，脚本必须向 Azure 订阅进行身份验证。

```powershell
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -DefaultProfile $AzureContext `
    -Parameters $params -Wait
```

如果希望 Runbook 使用系统分配的托管标识执行，请按原样保留代码。 如果希望使用用户分配的托管标识，则执行以下操作：
1. 从第 5 行中删除 `$AzureContext = (Connect-AzAccount -Identity).context`，
1. 将其替换为 `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`，然后
1. 输入客户端 ID。

## <a name="next-steps"></a>后续步骤

* 若要运行 Runbook，请参阅[在 Azure 自动化中启动 Runbook](start-runbooks.md)。
* 若要监视 Runbook 操作，请参阅 [Azure 自动化中的 Runbook 输出和消息](automation-runbook-output-and-messages.md)。
