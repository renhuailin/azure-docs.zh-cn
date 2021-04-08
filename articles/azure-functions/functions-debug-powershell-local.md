---
title: 在本地调试 PowerShell Azure Functions
description: 了解在本地运行时如何调试 PowerShell 函数。
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a668024db126c82f96756555aba513b77f7d7366
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422954"
---
# <a name="debug-powershell-azure-functions-locally"></a>在本地调试 PowerShell Azure Functions

Azure Functions 允许你将函数作为 PowerShell 脚本进行开发。

你可以使用以下标准开发工具，就像调试任何 PowerShell 脚本一样在本地调试 PowerShell 函数：

* [Visual Studio Code](https://code.visualstudio.com/)：Microsoft 提供的轻型且开源的免费文本编辑器，其中包含 PowerShell 扩展，该扩展提供完整的 PowerShell 开发体验。
* PowerShell 控制台：使用你在调试任何其他 PowerShell 进程时会所用的命令进行调试。

[Azure Functions Core Tools](functions-run-local.md) 支持对 Azure Functions（包括 PowerShell 函数）进行本地调试。

## <a name="example-function-app"></a>示例函数应用

本文中使用的函数应用具有单个 HTTP 触发的函数，并且包含以下文件：

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

此函数应用与你完成 [PowerShell 快速入门](./create-first-function-vs-code-powershell.md)后得到的应用类似。

`run.ps1` 中的函数代码类似于以下脚本：

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>设置附加点

若要调试任何 PowerShell 函数，需要停止该函数才能附加调试程序。 `Wait-Debugger` cmdlet 会停止执行操作并等待调试程序。

>[!NOTE]
>使用 PowerShell 7 时，不需要在代码中添加 `Wait-Debugger` 调用。

你需要做的所有工作是在 `if` 语句的上方添加对 `Wait-Debugger` cmdlet 的调用，如下所示：

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

调试从 `if` 语句开始。 

有了 `Wait-Debugger`，你现在就可以使用 Visual Studio Code 或 PowerShell 控制台来调试函数。

## <a name="debug-in-visual-studio-code"></a>在 Visual Studio Code 中进行调试

若要在 Visual Studio Code 中调试 PowerShell 函数，必须安装以下各项：

* [适用于 Visual Studio Code 的 PowerShell 扩展](/powershell/scripting/components/vscode/using-vscode)
* [适用于 Visual Studio Code 的 Azure Functions 扩展](./create-first-function-cli-powershell.md)
* [PowerShell Core 6.2 或更高版本](/powershell/scripting/install/installing-powershell-core-on-windows)

安装这些依赖项后，请加载现有的 PowerShell Functions 项目，或者[创建你的第一个 PowerShell Functions 项目](./create-first-function-vs-code-powershell.md)。

>[!NOTE]
> 如果你的项目没有所需的配置文件，则系统会提示你添加它们。

### <a name="set-the-powershell-version"></a>设置 PowerShell 版本

PowerShell Core 与 Windows PowerShell 一起安装。 将 PowerShell Core 设置为要与适用于 Visual Studio Code 的 PowerShell 扩展一起使用的 PowerShell 版本。

1. 按 F1 显示命令面板，然后搜索 `Session`。

1. 选择“PowerShell:显示会话菜单”。

1. 如果你的 **当前会话** 不是 **PowerShell Core 6**，请选择“切换到:PowerShell Core 6”。

打开 PowerShell 文件后，你会看到版本以绿色显示在窗口右下角。 选择此文本还会显示会话菜单。 若要了解详细信息，请参阅[选择要与扩展一起使用的 PowerShell 版本](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)。

### <a name="start-the-function-app"></a>启动函数应用

验证在要附加调试程序的函数中是否设置了 `Wait-Debugger`。  添加 `Wait-Debugger` 后，可以使用 Visual Studio Code 调试你的函数应用。

选择“调试”窗格，然后选择“附加到 PowerShell 函数”。

![调试器](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

还可以按 F5 键开始调试。

启动调试操作将执行以下任务：

* 在终端中运行 `func extensions install` 以安装你的函数应用所需的任何 Azure Functions 扩展。
* 在终端中运行 `func host start` 以在 Functions 主机中启动函数应用。
* 将 PowerShell 调试程序附加到 Functions 运行时中的 PowerShell 运行空间。

>[!NOTE]
> 你需要确保 PSWorkerInProcConcurrencyUpperBound 设置为 1，以确保 Visual Studio Code 中的调试体验正确。 这是默认设置。

在函数应用运行时，你需要一个单独的 PowerShell 控制台来调用 HTTP 触发的函数。

在这种情况下，PowerShell 控制台就是客户端。 `Invoke-RestMethod` 用来触发函数。

在 PowerShell 控制台中运行以下命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

你会注意到没有立即返回响应。 这是因为 `Wait-Debugger` 已附加了调试程序，并且 PowerShell 执行已尽快进入中断模式。 这是由 [BreakAll 概念](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)导致的，下文中将解释此概念。 按下 `continue` 按钮后，调试程序现在会在行上的 `Wait-Debugger` 后立即中断。

此时，调试程序已附加，你可以执行所有正常的调试程序操作。 有关在 Visual Studio Code 中使用调试程序的详细信息，请参阅[官方文档](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)。

继续操作并完全调用脚本后，你会注意到：

* 执行 `Invoke-RestMethod` 的 PowerShell 控制台返回了结果
* Visual Studio Code 中的 PowerShell 集成控制台正在等待脚本执行

以后调用同一函数时，PowerShell 扩展中的调试程序会在 `Wait-Debugger` 后立即中断。

## <a name="debugging-in-a-powershell-console"></a>在 PowerShell 控制台中进行调试

>[!NOTE]
> 本部分假设你已阅读 [Azure Functions Core Tools 文档](functions-run-local.md)，并且知道如何使用 `func host start` 命令来启动函数应用。

打开一个控制台，`cd` 到你的函数应用的目录中，并运行以下命令：

```sh
func host start
```

当函数应用运行起来且 `Wait-Debugger` 就位后，你可以附加到进程。 你需要另外两个 PowerShell 控制台。

其中一个控制台充当客户端。 在此控制台中，你调用 `Invoke-RestMethod` 来触发函数。 例如，你可以运行以下命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

你会注意到，它没有返回响应，这是由 `Wait-Debugger` 导致的。 PowerShell 运行空间现在正在等待调试程序附加到其中。 接下来，我们来附加该调试程序。

在另一个 PowerShell 控制台中运行以下命令：

```powershell
Get-PSHostProcessInfo
```

此 cmdlet 会返回一个表，该表类似于以下输出：

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

记下表中 `ProcessName` 为 `dotnet` 的项的 `ProcessId`。 此进程是你的函数应用。

接下来，运行以下代码片段：

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

启动后，调试程序会中断，并显示类似于以下输出的内容：

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

此时会在 [PowerShell 调试程序](/powershell/module/microsoft.powershell.core/about/about_debuggers)中的断点处停止。 在这里，你可以执行所有常用的调试操作：单步跳过、单步执行、继续、退出以及其他操作。 若要查看控制台中可用的完整调试命令集，请运行 `h` 或 `?` 命令。

还可以通过 `Set-PSBreakpoint` cmdlet 在此级别设置断点。

继续操作并完全调用脚本后，你会注意到：

* 你在其中执行了 `Invoke-RestMethod` 的 PowerShell 控制台现在返回了结果。
* 你在其中执行了 `Debug-Runspace` 的 PowerShell 控制台正在等待脚本执行。

你可以再次调用同一函数（例如，使用 `Invoke-RestMethod` 来调用），调试程序会在 `Wait-Debugger` 命令后立即进入。

## <a name="considerations-for-debugging"></a>调试注意事项

在调试 Functions 代码时，请注意以下问题。

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` 可能会导致调试程序在意外的位置进入

PowerShell 扩展使用 `Debug-Runspace`，后者又依赖于 PowerShell 的 `BreakAll` 功能。 此功能告知 PowerShell 在执行的第一个命令处停止。 此行为使你有机会在所调试的运行空间内设置断点。

在实际调用你的 `run.ps1` 脚本之前，Azure Functions 运行时会运行几个命令，因此，调试程序最终可能会在 `Microsoft.Azure.Functions.PowerShellWorker.psm1` 或 `Microsoft.Azure.Functions.PowerShellWorker.psd1` 内中断。

如果发生此中断，请运行 `continue` 或 `c` 命令来跳过此断点。 然后，你在预期的断点处停止。

## <a name="troubleshooting"></a>疑难解答

如果在调试过程中遇到困难，应检查以下各项：

| 检查 | 操作 |
|------|------|
| 在终端中运行 `func --version`。 如果出现“找不到 `func`”错误，则可能是因为本地 `path` 变量缺少 Core Tools (func.exe)。| [重新安装 Core Tools](functions-run-local.md#v2)。|  
| 在 Visual Studio Code 中，默认终端需要有权访问 func.exe。 请确保使用的不是未安装 Core Tools 的默认终端，例如适用于 Linux 的 Windows 子系统 (WSL)。  | 将 Visual Studio Code 中的默认 shell 设置为 PowerShell 7（推荐）或 Windows PowerShell 5.1。|
  

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 PowerShell 开发 Functions，请参阅 [Azure Functions PowerShell 开发人员指南](functions-reference-powershell.md)。
