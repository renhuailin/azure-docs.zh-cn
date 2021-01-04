---
title: Azure 自动化 Runbook 类型
description: 本文介绍可以在 Azure 自动化中使用的不同 Runbook 类型，以及在确定要使用的具体类型时的注意事项。
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 1a0c12297f19d30bf13ffbe594e0433c83914a8e
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733954"
---
# <a name="azure-automation-runbook-types"></a>Azure 自动化 Runbook 类型

下表定义了 Azure 自动化流程自动化功能支持的几种 Runbook 类型。 若要了解有关流程自动化环境的信息，请参阅[在 Azure 自动化中执行 Runbook](automation-runbook-execution.md)。

| 类型 | 说明 |
|:--- |:--- |
| [图形](#graphical-runbooks)|基于 Windows PowerShell 的图形 Runbook，只能在 Azure 门户上的图形编辑器中创建和编辑。 |
| [图形 PowerShell 工作流](#graphical-runbooks)|基于 Windows PowerShell 工作流的图形 Runbook，只能在 Azure 门户上的图形编辑器中创建和编辑。 |
| [PowerShell](#powershell-runbooks) |基于 Windows PowerShell 脚本的文本 runbook。 |
| [PowerShell 工作流](#powershell-workflow-runbooks)|基于 Windows PowerShell 工作流脚本的文本 runbook。 |
| [Python](#python-runbooks) |基于 Python 脚本的文本 runbook。 |

在确定特定 Runbook 需要使用的类型时，请注意以下事项。

* 无法将 Runbook 从图形转换为文本类型，反之亦然。
* 将不同类型的 Runbook 用作子 Runbook 存在各种限制。 有关详细信息，请参阅 [Azure 自动化中的子 Runbook](automation-child-runbooks.md)。

## <a name="graphical-runbooks"></a>图形 Runbook

可以在 Azure 门户中使用图形编辑器创建和编辑图形 Runbook 与图形 PowerShell 工作流 Runbook。 但是，不能使用其他工具来创建或编辑这种类型的 Runbook。 图形 Runbook 的主要功能：

* 导出到自动化帐户中的文件，然后导入到另一个自动化帐户。
* 生成 PowerShell 代码。
* 在导入过程中从图形 PowerShell 工作流 runbook 转换为。

### <a name="advantages"></a>优点

* 使用直观的插入-链接-配置创作模型。
* 注重数据在执行流程期间的流动方式。
* 直观展示管理流程。
* 可以以子 Runbook 形式包括其他 Runbook，以便创建高级工作流。
* 鼓励模块化编程。

### <a name="limitations"></a>限制

* 无法在 Azure 门户之外进行创建或编辑。
* 可能需要包含 PowerShell 代码的代码活动，才能执行复杂逻辑。
* 无法转换为某种[文本格式](automation-runbook-types.md)，也无法将文本 Runbook 转换为图形格式。 
* 不能查看或直接编辑图形工作流创建的 PowerShell 代码。 可以查看在任何代码活动中创建的代码。
* 无法在 Linux 混合 Runbook 辅助角色上运行 Runbook。 请参阅[使用混合 Runbook 辅助角色使数据中心或云端的资源实现自动化](automation-hybrid-runbook-worker.md)。

## <a name="powershell-runbooks"></a>PowerShell Runbook

基于 Windows PowerShell 的 PowerShell Runbook。 可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。  还可以使用任何脱机文本编辑器，以便[导入 Runbook](manage-runbooks.md) 到 Azure 自动化中。

### <a name="advantages"></a>优点

* 通过 PowerShell 代码实现所有复杂的逻辑，而无需其他复杂的 PowerShell 工作流。
* 与 PowerShell 工作流 Runbook 相比，它的启动速度更快，因为它们在运行前不需要经过编译。
* 在 Azure 和适用于 Windows 和 Linux 的混合 Runbook 辅助角色上运行。

### <a name="limitations"></a>限制

* 你必须熟悉 PowerShell 脚本。
* Runbook 无法使用[并行处理](automation-powershell-workflow.md#use-parallel-processing)并行执行多个操作。
* 出现错误时，Runbook 无法使用[检查点](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)恢复 Runbook。
* 你可以使用 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook)（用于创建新作业）仅将 PowerShell 工作流 Runbook 和图形 Runbook 以子 Runbook 的形式包括在内。

### <a name="known-issues"></a>已知问题

以下是当前 PowerShell Runbook 的已知问题：

* PowerShell Runbook 无法检索未加密且值为 Null 的[变量资产](./shared-resources/variables.md)。
* PowerShell Runbook 无法检索名称中包含 `*~*` 的变量资产。
* 在 PowerShell Runbook 中，处于循环状态的 [Get-Process](/powershell/module/microsoft.powershell.management/get-process) 操作在经历大约 80 次迭代后可能会崩溃。
* 如果 PowerShell Runbook 尝试一次性将大量数据写入输出流中，则可能会发生故障。 通常情况下，通过使 Runbook 仅输出处理大型对象所需的信息可以避免出现这种问题。 例如，可以让 cmdlet 仅输出所需的参数，如在 `Get-Process | Select ProcessName, CPU` 中那样，而不是不受限地使用 `Get-Process`。

## <a name="powershell-workflow-runbooks"></a>PowerShell 工作流 Runbook

PowerShell 工作流 Runbook 是基于 [Windows PowerShell 工作流](automation-powershell-workflow.md)的文本 Runbook。 可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。 还可以使用任何脱机文本编辑器，以便[导入 Runbook](manage-runbooks.md) 到 Azure 自动化中。

### <a name="advantages"></a>优点

* 通过 PowerShell 工作流代码实现所有复杂的逻辑。
* 出现错误时，使用[检查点](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)恢复操作。
* 使用[并行处理](automation-powershell-workflow.md#use-parallel-processing)并行执行多个操作。
* 能够以子 Runbook 的形式包括其他图形 Runbook 和 PowerShell 工作流 Runbook，以创建高级工作流。

### <a name="limitations"></a>限制

* 你必须熟悉 PowerShell 工作流。
* Runbook 还必须处理与 PowerShell 工作流相关的其他复杂问题，例如[反序列化的对象](automation-powershell-workflow.md#deserialized-objects)。
* 与 PowerShell Runbook 相比，Runbook 的启动时间更长，因为它们在运行前需要进行编译。
* 可以使用 `Start-AzAutomationRunbook` cmdlet 仅将 PowerShell Runbook 作为子 Runbook 包括在内。
* Runbook 无法在 Linux 混合 Runbook 辅助角色上运行。

## <a name="python-runbooks"></a>Python Runbook

在 Python 2 和 Python 3 下编译 python runbook。 Python 3 runbook 目前处于预览阶段。 可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。 还可以使用任何脱机文本编辑器，以及将 [Runbook 导入](manage-runbooks.md)到 Azure 自动化中。

### <a name="advantages"></a>优点

* 使用强大的 Python 库。
* 可以在 Azure 中或在混合 Runbook 辅助角色上运行。
* 对于 Python 2，安装了 [python 2.7](https://www.python.org/downloads/release/latest/python2) 支持 Windows 混合 Runbook 辅助角色。
* 对于 Python 3 云作业，支持 Python 3.8 版本。 如果代码在不同版本之间兼容，则任何1.x 版本中的脚本和包都可能起作用。  
* 对于 Windows 计算机上的 Python 3 混合作业，您可以选择安装您可能想要使用的任何1.x 版本。  
* 对于 Linux 计算机上的 Python 3 混合作业，我们依赖于计算机上安装的 Python 3 版本来运行 DSC OMSConfig 和 Linux 混合辅助角色。 建议在 Linux 计算机上安装3.6。 但是，如果在不同版本的 Python 3 之间没有重大更改，则不同版本也应该有效。

### <a name="limitations"></a>限制

* 你必须熟悉 Python 脚本。
* 若要使用第三方库，必须将[包导入](python-packages.md)自动化帐户。
* 使用   Powershell/powershell 工作流中的 AutomationRunbook cmdlet 启动 Python 3 runbook (预览) 不起作用。 你可以使用 Az. Automation 模块中的 **AzAutomationRunbook** Cmdlet 或 AzureRm 模块中的 **start-azurermautomationrunbook** cmdlet，以解决此限制。  
* Python 3 runbook (预览) 并且包不适用于 PowerShell。
* 不支持使用 Webhook 来启动 Python Runbook。
* Azure 自动化不支持 **sys.databases**。

### <a name="known-issues"></a>已知问题

Python 3 作业有时会失败，并出现异常消息 *解释器可执行文件路径无效*。 如果作业延迟、启动超过10分钟或使用 **AutomationRunbook** 启动 Python 3 runbook，可能会看到此异常。 如果作业延迟，请重新启动 runbook。

## <a name="next-steps"></a>后续步骤

* 要了解 PowerShell Runbook，请参阅[教程：创建 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)。
* 要了解 PowerShell 工作流 Runbook，请参阅[教程：创建 PowerShell 工作流 Runbook](learn/automation-tutorial-runbook-textual.md)。
* 要了解图形 Runbook，请参阅[教程：创建图形 Runbook](learn/automation-tutorial-runbook-graphical.md)。
* 要了解 Python Runbook，请参阅[教程：创建 Python Runbook](learn/automation-tutorial-runbook-textual-python2.md)。
