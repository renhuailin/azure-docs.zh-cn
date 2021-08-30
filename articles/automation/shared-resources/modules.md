---
title: 管理 Azure 自动化中的模块
description: 本文介绍如何使用 PowerShell 模块在 DSC 配置中启用 runbook 和 DSC 资源中的 cmdlet。
services: automation
ms.subservice: shared-capabilities
ms.date: 04/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9fc7a8d5b27da251f13f2c9dfeffa03f7cdbd149
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452552"
---
# <a name="manage-modules-in-azure-automation"></a>管理 Azure 自动化中的模块

Azure 自动化使用许多 PowerShell 模块在 runbook DSC 配置中启用 runbook 和 DSC 资源中的 cmdlet。 支持的模块包括：

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az)。
* [Azure PowerShell AzureRM.Automation](/powershell/module/azurerm.automation/)。
* 其他 PowerShell 模块。
* 内部 `Orchestrator.AssetManagement.Cmdlets` 模块。
* Python 2 模块。
* 你创建的自定义模块。

创建自动化帐户时，Azure 自动化默认导入一些模块。 请参阅[默认模块](#default-modules)。

## <a name="sandboxes"></a>沙盒

当自动化执行 runbook 和 DSC 编译作业时，它会将模块加载到沙盒中，其中 runbook 可以运行，并且 DSC 配置可以编译。 自动化还自动将任何 DSC 资源放置到 DSC 拉取服务器上的模块中。 计算机在应用 DSC 配置时可以提取资源。

>[!NOTE]
>请务必仅导入 runbook 和 DSC 配置所需的模块。 不建议导入根 Az 模块。 它包括可能不需要的许多其他模块，这可能导致性能问题。 改为导入单个模块，如 Az.Compute。

云沙盒最多支持 48 个系统调用，并出于安全原因限制所有其他调用。 云沙盒不支持其他功能，例如凭据管理和某些网络连接。

由于包含的模块和 cmdlet 数量众多，因此很难事先知道哪个 cmdlet 会进行不受支持的调用。 通常，对于需要提升的访问权限以及需要凭据作为参数的 cmdlet，或者对于与网络相关的 cmdlet，我们都知道其问题所在。 沙盒不支持执行完全堆栈网络操作的任何 cmdlet，包括 AIPService PowerShell 模块中的 [Connect-AipService](/powershell/module/aipservice/connect-aipservice) 和 DNSClient 模块中的 [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname)。

这些是沙盒的已知限制。 建议的解决方法是部署[混合 Runbook 辅助角色](../automation-hybrid-runbook-worker.md)或使用 [Azure Functions](../../azure-functions/functions-overview.md)。

> [!IMPORTANT] 
> 不要在任何专门使用 Az 模块执行的脚本中包含关键字“AzureRm”。 即使在注释中包含关键字，也可能导致加载 AzureRm，然后与 Az 模块冲突。

## <a name="default-modules"></a>默认模块

下表列出了在创建自动化帐户时，Azure 自动化默认导入的模块。 自动化可以导入这些模块的较新版本。 但是，即使你删除了较新新版本，也不能从自动化帐户中删除原始版本。 请注意，这些默认模块包括多个 AzureRM 模块。

默认模块也称为“全局模块”。 在 Azure 门户中，查看创建帐户时导入的模块时，“全局模块”属性将为 true 。

![Azure 门户中的全局模块属性的屏幕截图](../media/modules/automation-global-modules.png)

自动化不会自动将根 Az 模块导入任何新的或现有的自动化帐户。 要详细了解如何使用这些模块，请参阅[迁移到 Az 模块](#migrate-to-az-modules)。

> [!NOTE]
> 我们不建议更改自动化帐户中用于部署[在空闲时间启动/停止 VM](../automation-solution-vm-management.md) 的模块和 runbook。

|模块名称|版本|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.存储 | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Az 模块

对于 Az.Automation，大多数 cmdlet 的名称与 AzureRM 模块的名称相同，但 `AzureRM` 前缀已更改为 `Az`。 有关不遵循此命名约定的 Az 模块列表，请参阅[例外名称列表](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)。

## <a name="internal-cmdlets"></a>内部 cmdlet

Azure 自动化支持内部 cmdlet，这些cmdlet 只有在 Azure 沙盒环境中或对 Windows 混合 Runbook 辅助角色执行 runbook 时才能可用。 内部模块 `Orchestrator.AssetManagement.Cmdlets` 默认安装在自动化帐户中，并且计算机上需要已安装 Windows 混合 Runbook 辅助角色。 

下表定义了内部 cmdlet。 这些 cmdlet 旨在用于代替 Azure PowerShell cmdlet 与自动化帐户资源进行交互。 它们可以从加密的变量、凭据和加密的连接中检索机密。

|名称|说明|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

请注意，内部 cmdlet 的命名与 Az 和 AzureRM cmdlet 的不同。 内部 cmdlet 名称不包含像 `Azure` 或 `Az` 这样的单词，但请使用单词 `Automation`。 建议在 Azure 沙盒中或 Windows 混合 Runbook 辅助角色上执行 runbook 时，不要使用 Az 或 AzureRM cmdlet，因为它们需要的参数较少，而且在执行过程中是在作业的上下文中运行的。

使用 Az 或 AzureRM cmdlet 在 runbook 上下文之外操作自动化资源。 

## <a name="python-modules"></a>Python 模块

可以在 Azure 自动化中创建 Python 2 runbook。 有关 Python 模块信息，请参阅[在 Azure 自动化中管理 Python 2 包](../python-packages.md)。

## <a name="custom-modules"></a>自定义模块

Azure 自动化支持你创建用于 runbook 和 DSC 配置的自定义 PowerShell 模块。 自定义模块的一种类型是集成模块，该模块可以选择包含元数据文件，以定义模块 cmdlet 的自定义功能。 [添加连接类型](../automation-connections.md#add-a-connection-type)中提供了集成模块的使用的示例。

Azure 自动化可以导入自定义模块以提供其 cmdlet。 它可以在后台存储该模块并在 Azure 沙盒中使用，方式与其他模块别无二致。

## <a name="migrate-to-az-modules"></a>迁移到 Az 模块

本部分介绍如何迁移到自动化中的 Az 模块。 有关详细信息，请参阅[将 Azure PowerShell 从 AzureRM 迁移到 Az](/powershell/azure/migrate-from-azurerm-to-az)。

我们不建议在同一自动化帐户中运行 AzureRM 模块和 Az 模块。 当确定要从 AzureRM 迁移到 Az 时，最好完全采用完整迁移。 自动化通常会重复使用自动化帐户中的沙盒，以节省启动时间。 如果不进行完整模块迁移，则可以启动仅使用 AzureRM 模块的作业，然后启动另一个仅使用 Az 模块的作业。 沙盒即将崩溃，你会收到一个错误，指出模块不兼容。 这种情况会导致任何特定 runbook 或配置的随机崩溃。

>[!NOTE]
>创建新的自动化帐户时，即使在迁移到 Az 模块后，自动化也会默认安装 AzureRM 模块。 你仍可以使用 AzureRM cmdlet 更新教程 runbook。 但是，不应运行这些 runbook。

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>在模块迁移之前测试 runbook 和 DSC 配置

在迁移到 Az 模块之前，请务必在单独的自动化帐户中仔细测试所有 runbook 和 DSC 配置。 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>停止并取消计划使用 AzureRM 模块的所有 runbook

要确保不运行任何使用 AzureRM 模块的现有 runbook 或 DSC 配置，必须停止并取消计划所有受影响的 runbook 和配置。 首先，请确保分别查看每个 runbook 或 DSC 配置及其计划，以确保将来可以根据需要重新计划项目。

准备好删除计划时，可以使用 Azure 门户或 [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) cmdlet。 请参阅[删除计划](schedules.md#remove-a-schedule)。

### <a name="remove-azurerm-modules"></a>删除 AzureRM 模块

在导入 Az 模块之前，可以删除 AzureRM 模块。 但如果这样做，则可以中断源代码管理同步并导致任何仍在计划的脚本失败。 如果决定删除模块，请参阅[卸载 AzureRM](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm)。

### <a name="import-az-modules"></a>导入 Az 模块

在自动化帐户中导入 Az 模块的行为不会在 runbook 使用的 PowerShell 会话中自动导入该模块。 在以下情况中，模块会导入到 PowerShell 会话中：

* runbook 从模块中调用 cmdlet 时。
* runbook 使用 [Import-Module](/powershell/module/microsoft.powershell.core/import-module) cmdlet 显式导入模块时。
* runbook 使用 [using module](/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) 语句显式导入模块时。 从 Windows PowerShell 5.0 开始支持 using 语句，并支持类和枚举类型导入。
* runbook 导入另一个依赖模块时。

可以在 Azure 门户将 Az 模块导入自动化帐户。 切记，仅导入所需的 Az 模块，而不是每个可用的 Az 模块。 由于 [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) 是其他 Az 模块的依赖项，因此请确保在其他模块之前先将其导入。

1. 登录 Azure [门户](https://portal.azure.com)。
1. 搜索并选择“自动化帐户”。
1. 在“自动化帐户”页上，从列表中选择你的自动化帐户。
1. 在自动化帐户中的“共享资源”下，选择“模块” 。
1. 选择“浏览库”。  
1. 在搜索栏中，输入模块名称（例如 `Az.Accounts`）。
1. 在 PowerShell 模块页，选择“导入”，将模块导入自动化帐户。

    ![将模块导入自动化帐户的屏幕截图](../media/modules/import-module.png)

还可以搜索要导入的模块，通过 [PowerShell 库](https://www.powershellgallery.com)执行此导入。 找到该模块后，选择它，然后选择“Azure 自动化”选项卡。选择“部署到 Azure 自动化”。

![直接从 PowerShell 库导入模块的屏幕截图](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>测试 runbook

将 Az 模块导入自动化帐户后，可以开始编辑 runbook 和 DSC 配置以使用新模块。 测试 runbook 的修改以使用新 cmdlet 的一种方法是在 runbook 的开头使用 `Enable-AzureRmAlias -Scope Process` 命令。 通过将此命令添加到 runbook，脚本无需更改也可运行。

## <a name="author-modules"></a>创作者模块

当创作用于 Azure 自动化的自定义 PowerShell 模块时，建议遵循本部分中的注意事项。 要准备要导入的模块，必须至少创建一个名称与模块文件夹的名称相同的 .psd1、.psm1 或 PowerShell 模块 .dll 文件。 然后压缩模块文件夹，以便 Azure 自动化可以将其作为单个文件导入。 .zip 包的名称应与包含的模块文件夹的名称相同。

要详细了解如何创作 PowerShell 模块，请参阅[如何编写 PowerShell 脚本模块](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)。

### <a name="version-folder"></a>版本文件夹

通过 PowerShell 并行模块版本控制，可以在 PowerShell 中使用一个模块的多个版本。 如果你有经过测试的较旧脚本并仅适用于特定版本的 PowerShell 模块，则此方法很有用，但其他脚本需要同一 PowerShell 模块的较新版本。

若要构造 PowerShell 模块以使其包含多个版本，请创建模块文件夹，然后在该模块文件夹中为要使用的每个模块版本创建一个文件夹。 在以下示例中，名为“TestModule”的模块提供了两个版本：1.0.0 和 2.0.0。

```dos
TestModule
   1.0.0
   2.0.0
```

在每个版本文件夹中，将组成模块的 PowerShell .psm1、.psd1 或 PowerShell 模块 .dll 文件复制到相应的版本文件夹中。 压缩模块文件夹，以便 Azure 自动化可以将其作为单个 .zip 文件导入。 虽然 Azure 自动化仅显示导入的模块的最高版本，但如果模块包中包含并行版本的模块，则它们均可在你的 runbook 或 DSC 配置中使用。  

虽然自动化支持在同一包中包含并行版本的模块，但它不支持跨模块包导入使用模块的多个版本。 例如，将包含版本 1 和版本 2 的模块 A 导入到你的 Azure 自动化帐户中。 稍后，当你导入 Azure 自动化帐户时，如果将模块 A 更新为包括版本 3 和 4，则在任何 runbook 或 DSC 配置中只有版本 3 和 4 可用。 如果需要所有版本（版本 1、2、3 和 4 都可用），则导入的 .zip 文件应包含版本 1、2、3 和 4。

如果要在 runbook 之间使用同一模块的不同版本，则应始终使用 `Import-Module` cmdlet 声明要在 runbook 中使用的版本，并包括参数 `-RequiredVersion <version>`。 即使你要使用的版本是最新版本。 这是因为 Runbook 作业可能在同一沙盒中运行。 如果沙盒已经显式加载了某个版本号的模块（因为该沙盒中的先前作业显示是这样做的），则该沙盒中的未来作业将不会自动加载该模块的最新版本。 这是因为它的某些版本已在沙盒中加载。

对于 DSC 资源，请使用以下命令指定特定版本：

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>帮助信息

在模块中加入每个 cmdlet 的摘要、说明和帮助 URI。 在 PowerShell 中，可以使用 `Get-Help` cmdlet 来定义 cmdlet 的帮助信息。 以下示例显示如何在 .psm1 模块文件中定义摘要和帮助 URI。

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  提供此信息通过 PowerShell 控制台中的 `Get-Help` cmdlet 显示帮助文本。 此文本也显示在 Azure 门户中。

  ![集成模块帮助的屏幕截图](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>连接类型

如果模块连接到外部服务，请使用[自定义集成模块](#custom-modules)定义连接类型。 模块中的每个 cmdlet 都应接受该连接类型（连接对象）的实例作为参数。 用户可在每次调用 cmdlet 时将连接资产的参数映射到 cmdlet 的相应参数。

![在 Azure 门户中使用自定义连接](../media/modules/connection-create-new.png)

下面的 runbook 示例使用名为 `ContosoConnection` 的 Contoso 连接资产来访问 Contoso 资源并从外部服务返回数据。 在此示例中，字段映射到 `PSCredential` 对象的 `UserName` 和 `Password` 属性，然后传递给 cmdlet。

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

实现此行为的更轻松且更好的方法是直接将连接对象传递给 cmdlet：

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

要为 cmdlet 启用类似行为，可以允许 cmdlet 以参数的形式直接接受连接对象，而不是只接受连接字段作为参数。 通常情况下，需要为每个 cmdlet 提供一个参数集，这样不使用自动化的用户在调用 cmdlet 时就不需要构造一个哈希表来充当连接对象。 参数集 `UserAccount` 可用于传递连接字段属性。 `ConnectionObject` 允许将连接一直传下去。

### <a name="output-type"></a>输出类型

在模块中定义所有 cmdlet 的输出类型。 定义 cmdlet 的输出类型以后，就可以利用设计时 IntelliSense 在创作期间确定 cmdlet 的输出属性。 这种做法在图形 runbook 的创作过程中特别有用，这种创作过程要求掌握一定程度的设计时知识，以改进用户对模块的使用体验。

添加 `[OutputType([<MyOutputType>])]`，其中 `MyOutputType` 是有效类型。 要详细了解 `OutputType`，请参阅[关于 Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)。 下面代码是将 `OutputType` 添加到 cmdlet 的示例：

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![图形 runbook 输出类型的屏幕截图](../media/modules/runbook-graphical-module-output-type.png)

  此行为类似于 PowerShell 集成服务环境中 cmdlet 输出的“预输入”功能，但不需要运行。

  ![POSH IntelliSense 的屏幕截图](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet 状态

使模块中的所有 cmdlet 以无状态方式呈现。 多个 runbook 作业可在同一 `AppDomain` 和同一进程及沙盒中同时运行。 如果在这些级别上共享了任何状态，则作业可能会互相影响。 此行为会导致间歇性和难以诊断的问题。 下面是应避免的处理方式的示例：

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>模块依赖项

确保模块完全包含在可以使用 xcopy 进行复制的包中。 runbook 执行时，自动化模块将分发到自动化沙盒中。 模块必须独立于运行它们的主机工作。

你应能够压缩并移动模块包，并确保该模块包在导入到其他主机的 PowerShell 环境时可以正常运行。 为此，请确保模块不依赖在模块导入自动化时压缩的模块文件夹外部的任何文件。

模块不应依赖主机上的任何唯一注册表设置。 例如，安装产品时所做的设置。

### <a name="module-file-paths"></a>模块文件路径

确保模块中所有文件的路径少于 140 个字符。 长度超过 140 个字符的任何路径都会导致导入 runbook 时出现问题。 自动化无法使用 `Import-Module` 将路径大小超过 140 个字符的文件导入 PowerShell 会话中。

## <a name="import-modules"></a>导入模块

本部分定义了将模块导入自动化帐户的几种方法。

### <a name="import-modules-in-the-azure-portal"></a>在 Azure 门户中导入 Azure 模块

在 Azure 门户中导入 Azure 模块：

1. 在端口中，搜索并选择“自动化帐户”。
1. 在“自动化帐户”页上，从列表中选择你的自动化帐户。
1. 在“共享资源”下，选择“模块” 。
1. 选择“添加模块”。
1. 选择包含模块的 .zip 文件。
1. 选择“确定”以开始导入过程。

### <a name="import-modules-by-using-powershell"></a>使用 PowerShell 导入模块

可以使用 [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) cmdlet 将模块导入自动化帐户。 cmdlet 会获取模块 .zip 包的 URL。

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

还可以使用相同的 cmdlet 直接从 PowerShell 库导入模块。 请确保从 [PowerShell 库](https://www.powershellgallery.com)获取 `ModuleName` 和 `ModuleVersion`。

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>从 PowerShell 库导入模块

可以直接从库中或自动化帐户中导入 [PowerShell 库](https://www.powershellgallery.com)模块。

直接从 PowerShell 库导入模块：

1. 转到 https://www.powershellgallery.com ，然后搜索要导入的模块。
2. 在“安装选项”下的“Azure 自动化”选项卡上，选择“部署到 Azure 自动化”  。 此操作会打开 Azure 门户。 
3. 在“导入”页上，选择你的自动化帐户并选择“确定”。

![PowerShell 库导入模块的屏幕截图](../media/modules/powershell-gallery.png)

直接从自动化帐户导入 PowerShell 库模块：

1. 在端口中，搜索并选择“自动化帐户”。
1. 在“自动化帐户”页上，从列表中选择你的自动化帐户。
1. 在“共享资源”下，选择“模块” 。 
1. 选择“浏览库”，然后搜索模块的库。 
1. 选择要导入的模块，然后选择“导入”。 
1. 选择“确定”以开始导入过程。

![从 Azure 门户导入 PowerShell 库模块的屏幕截图](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>删除模块

如果模块出现问题，或者需要回滚到模块的早期版本，则可以将其从自动化帐户中删除。 无法删除[默认模块](#default-modules)的原始版本，这些模块是在创建自动化帐户时导入的。 如果要删除的模块是[默认模块](#default-modules)之一的较新版本，则它会回滚到使用自动化帐户安装的版本。 否则，从自动化帐户中删除的任何模块都将被删除。

### <a name="delete-modules-in-the-azure-portal"></a>在 Azure 门户中删除模块

在 Azure 门户中删除模块：

1. 在端口中，搜索并选择“自动化帐户”。
1. 在“自动化帐户”页上，从列表中选择你的自动化帐户。
1. 在“共享资源”下，选择“模块” 。
1. 选择要删除的模块。
1. 在“模块”页上，选择“删除”。 如果此模块是[默认模块](#default-modules)之一，则它会回滚到创建自动化帐户时存在的版本。

### <a name="delete-modules-by-using-powershell"></a>使用 PowerShell 删除模块

要通过 PowerShell 删除模块，请运行以下命令：

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>后续步骤

* 要详细了解如何使用 Azure PowerShell 模块，请参阅 [Azure PowerShell 入门](/powershell/azure/get-started-azureps)。

* 要详细了解如何创建 PowerShell 模块，请参阅[编写 Windows PowerShell 模块](/powershell/scripting/developer/module/writing-a-windows-powershell-module)。
