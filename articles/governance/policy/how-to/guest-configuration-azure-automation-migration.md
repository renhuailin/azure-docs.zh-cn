---
title: Azure 自动化 State Configuration 到来宾配置迁移计划
description: 本文为有兴趣从 Azure 自动化中的 DSC 版本 2 迁移到 Azure Policy 中的版本 3 的客户提供过程和技术指导。
ms.date: 07/1/2021
ms.topic: how-to
ms.openlocfilehash: ae387e59abe5cbff335b43cdd78030f32f6e6f8c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868338"
---
# <a name="azure-automation-state-configuration-to-guest-configuration-migration-planning"></a>Azure 自动化状态配置到来宾配置迁移计划

来宾配置是 Azure 自动化 State Configuration（也称为 Azure 自动化 Desired State Configuration 或 AADSC）提供的功能的最新实现。
如果可能，应计划将内容和计算机移动到新服务。
本文提供有关制定从 Azure 自动化到来宾配置的迁移策略的指导。

来宾配置中的新功能解决客户的首要问题：

- 增加了配置的大小限制 (100MB)
- 通过 Azure Resource Graph 进行高级报告（包括资源 ID 和状态）
- 管理相同计算机的多个配置
- 当计算机偏离所需状态时，可控制进行修正的时间
- Linux 和 Windows 都使用基于 PowerShell 的 DSC 资源

开始之前，最好参阅 [Azure Policy 来宾配置](../concepts/guest-configuration.md)页中的概念性概述信息。

## <a name="understand-migration"></a>了解迁移

迁移的最佳方法是先重新部署内容，然后迁移计算机。 下面概述了迁移的预期步骤。

- 从 Azure 自动化导出配置
- 发现模块要求并在环境中加载它们
- 编译配置
- 创建和发布来宾配置包
- 测试来宾配置包
- 将混合计算机加入 Azure Arc
- 从 Azure 自动化 State Configuration 注销服务器
- 使用来宾配置将配置分配给服务器

来宾配置将 DSC 版本 3 与 PowerShell 版本 7 结合使用。 DSC 版本 3 可以在 [Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) 和 [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted) 中与较旧 DSC 版本共存。
实现是独立的。 但是没有冲突检测。

来宾配置不需要将模块或配置发布到服务或是在服务中进行编译。 相反，内容使用专用工具进行开发和测试，并在计算机可通过 HTTPS 访问的任何位置（通常是 Azure Blob 存储）进行发布。

如果你决定迁移的正确计划是在一段时间内让计算机同时处于两种服务中，那么虽然管理起来可能会很混乱，不过没有技术障碍。 两种服务是独立的。

## <a name="export-content-from-azure-automation"></a>从 Azure 自动化导出内容

首先从 Azure 自动化 State Configuration 发现内容并导出到为来宾配置创建、测试和发布内容包的开发环境。

### <a name="configurations"></a>配置

只能从 Azure 自动化导出配置脚本。 无法导出“节点配置”或已编译的 MOF 文件。
如果将 MOF 文件直接发布到自动化帐户，并且不再有权访问原始文件，则必须从专用配置脚本重新编译，或者可能会重新创作配置（如果找不到原始配置）。

若要从 Azure 自动化导出配置脚本，请首先标识包含配置的 Azure 自动化帐户，以及部署自动化帐户的资源组的名称。

安装 PowerShell 模块“Az.Automation”。

```powershell
Install-Module Az.Automation
```

接下来，使用“Get-AzAutomationAccount”命令标识自动化帐户以及部署它们的资源组。
属性“ResourceGroupName”和“AutomationAccountName”对于后续步骤非常重要。

```powershell
Get-AzAutomationAccount

SubscriptionId        : <your subscription id>
ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 :
Plan                  :
CreationTime          : 6/30/2021 11:56:17 AM -05:00
LastModifiedTime      : 6/30/2021 11:56:17 AM -05:00
LastModifiedBy        :
Tags                  : {}
```

发现自动化帐户中的配置。 输出对于每个配置包含一个条目。 如果有许多配置，则会将信息存储为变量，以便更轻松地处理。

```powershell
Get-AzAutomationDscConfiguration -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name>

ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 : Published
Name                  : <your configuration name>
Tags                  : {}
CreationTime          : 6/30/2021 12:18:26 PM -05:00
LastModifiedTime      : 6/30/2021 12:18:26 PM -05:00
Description           :
Parameters            : {}
LogVerbose            : False
```

最后，使用命令“Export-AzAutomationDscConfiguration”将每个配置导出到本地脚本文件。 生成的文件名使用模式 `\ConfigurationName.ps1`。

```powershell
Export-AzAutomationDscConfiguration -OutputFolder /<location on your machine> -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name> -name <your configuration name>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
```

#### <a name="export-configurations-using-the-powershell-pipeline"></a>使用 PowerShell 管道导出配置

发现帐户和配置数量后，可能希望将所有配置导出到计算机上的本地文件夹。
若要自动执行此过程，请将以上每个命令的输出通过管道传递到下一个命令。

示例导出 5 个配置。 输出模式是唯一的成功指示。

```powershell
Get-AzAutomationAccount | Get-AzAutomationDscConfiguration | Export-AzAutomationDSCConfiguration -OutputFolder /<location on your machine>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
```

#### <a name="consider-decomposing-complex-configuration-files"></a>考虑分解复杂的配置文件

来宾配置可以为每台计算机管理多个配置。
许多为 Azure 自动化 State Configuration 编写的配置都采用为每台计算机管理单个配置的限制。 若要利用来宾配置提供的扩展功能，可以将大型配置文件划分为许多较小的配置，其中每个配置处理特定方案。

来宾配置中没有业务流程来控制配置的排序顺序，因此，如果需要按顺序执行配置中的步骤，请将这些步骤一起放在一个包中。

### <a name="modules"></a>模块

无法从 Azure 自动化导出模块或自动关联哪些配置需要哪个模块/版本。 必须让模块处于本地环境中才能创建新的来宾配置包。 若要创建迁移所需的模块列表，请使用 PowerShell 对 Azure 自动化查询模块的名称和版本。

如果使用的模块是自定义创作的，并且仅存在于专用开发环境中，则无法从 Azure 自动化导出它们。

如果某个自定义模块是配置所必需的，并且处于帐户中，但是在环境中找不到它，则无法编译配置，这意味着无法迁移配置。

#### <a name="list-modules-imported-in-azure-automation"></a>列出在 Azure 自动化中导入的模块

若要检索自动化帐户中安装的所有模块的列表，请使用 `Get-AzAutomationModule` 命令。 属性“IsGlobal”会告知模块是否始终内置在 Azure 自动化中，或者是否已发布到帐户。

例如，用于创建发布到任何帐户的所有模块的列表。

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false
```

还可以使用 PowerShell 库来帮助查找有关公开提供的模块的详细信息。 例如，以下示例生成内置到新自动化帐户以及包含 DSC 资源的模块的列表。

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $true | Find-Module -erroraction silentlycontinue | ? {'' -ne $_.Includes.DscResource} | Select Name, Version -Unique | format-table -AutoSize

Name                       Version
----                       -------
AuditPolicyDsc             1.4.0
ComputerManagementDsc      8.4.0
PSDscResources             2.12.0
SecurityPolicyDsc          2.10.0
xDSCDomainjoin             1.2.23
xPowerShellExecutionPolicy 3.1.0.0
xRemoteDesktopAdmin        1.1.0.0
```

#### <a name="download-modules-from-powershell-gallery-or-powershellget-repository"></a>从 PowerShell 库或 PowerShellGet 存储库下载模块

如果模块是从 PowerShell 库导入，则可以直接在 `Install-Module` 中从 `Find-Module` 通过管道发送输出。 跨命令通过管道发送输出提供了一种解决方案来加载开发人员环境，其中在 PowerShell 库中公开提供的所有模块当前都处于自动化帐户中。

如果在本地环境中将自定义 NuGet 源注册为 [PowerShellGet 存储库](/powershell/scripting/gallery/how-to/working-with-local-psrepositories)，则可以使用相同的方法从该源中拉取模块。

示例中的 `Find-Module` 命令不禁止显示错误，这意味着在库中找不到的任何模块都会返回错误消息。

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false | Find-Module | ? {'' -ne $_.Includes.DscResource} | Install-Module

  Installing package xWebAdministration'

    [                                                                                        ]
```

#### <a name="inspecting-configuration-scripts-for-module-requirements"></a>检查配置脚本的模块要求

如果从 Azure 自动化导出了配置脚本，则还可以查看内容，了解有关将每个配置编译为 MOF 文件所需的模块的详细信息。 仅当在删除了模块的自动化帐户中找到配置时，才需要此方法。
配置对计算机不再有用，但它们可能仍在帐户中。

在每个文件的顶部，查找包含“Import-DscResource”的行。
此命令仅适用于配置内部，用于在编译时加载模块。

例如，PowerShell 库中的“WindowsIISServerConfig”配置包含此示例中的行。

```powershell
configuration WindowsIISServerConfig
{

Import-DscResource -ModuleName @{ModuleName = 'xWebAdministration';ModuleVersion = '1.19.0.0'}
Import-DscResource -ModuleName 'PSDesiredStateConfiguration'
```

该配置要求将“xWebAdministration”模块版本设置为“1.19.0.0”和模块“PSDesiredStateConfiguration”。

### <a name="test-content-in-azure-guest-configuration"></a>在 Azure 来宾配置中测试内容

评估 Azure 自动化 State Configuration 中的内容是否可与来宾配置一起使用的最佳方式是遵循[如何创建自定义来宾配置包项目](./guest-configuration-create.md)中的分步教程。

执行到步骤[创作配置](./guest-configuration-create.md#author-a-configuration)时，生成 MOF 文件的配置脚本应该是从 Azure 自动化 State Configuration 中导出的脚本之一。 必须先在环境中安装所需 PowerShell 模块，然后才能将配置编译为 MOF 文件并创建来宾配置包。

#### <a name="what-if-a-module-does-not-work-with-guest-configuration"></a>如果模块不适用于来宾配置，该怎么办？

某些模块可能会遇到与来宾配置的兼容性问题。 最常见的问题是 .NET framework 与 .NET core 的相关问题。 [Windows PowerShell 5.1 与 PowerShell (core) 7.x 之间的差异](/powershell/scripting/whats-new/differences-from-windows-powershell)页面上提供了详细技术信息

解决兼容性问题的一种选项是通过运行 `powershell.exe`，从在 PowerShell 7 中导入的模块中运行 Windows PowerShell 中的命令。
可以在 Azure-Policy 存储库中查看一个使用此技术的示例模块，该模块用于审核 [Windows DSC 配置](https://github.com/Azure/azure-policy/blob/bbfc60104c2c5b7fa6dd5b784b5d4713ddd55218/samples/GuestConfiguration/package-samples/resource-modules/WindowsDscConfiguration/DscResources/WindowsDscConfiguration/WindowsDscConfiguration.psm1#L97)的状态。

该示例还演示了一个小的概念证明。

```powershell
# example function that could be loaded from module
function New-TaskResolvedInPWSH7 {
  # runs the fictitious command 'Get-myNotCompatibleCommand' in Windows PowerShell
  $compatObject = & powershell.exe -noprofile -NonInteractive -command { Get-myNotCompatibleCommand }
  # resulting object can be used in PowerShell 7
  return $compatObject
}
```

#### <a name="will-i-have-to-add-reasons-property-to-get-targetresource-in-all-modules-i-migrate"></a>是否必须在迁移的所有模块中都将“Reasons”属性添加到 Get-TargetResource？

从 Azure 门户查看配置分配的结果时，实现[“Reasons”属性](../concepts/guest-configuration-custom.md#special-requirements-for-get)可提供更好的体验。 如果模块中的 `Get` 方法不包含“Reasons”，则会返回泛型输出，其中包含 `Get` 方法返回的属性中的详细信息。 因此，这是迁移时的可选操作。

## <a name="machines"></a>机

在测试完来宾配置中的 Azure 自动化 State Configuration 内容后，需制定计划来迁移计算机。

Azure 自动化 State Configuration 适用于 Azure 中的虚拟机和位于 Azure 外部的混合计算机。 必须使用不同的步骤来规划每个方案。

### <a name="azure-vms"></a>Azure VM

Azure 虚拟机已在 Azure 中拥有[资源](../../../azure-resource-manager/management/overview.md#terminology)，这意味着它们已准备好进行将它们与配置关联的来宾配置分配。 迁移 Azure 虚拟机的高级别任务是将它们从 Azure 自动化 State Configuration 中删除，然后使用来宾配置分配配置。

若要从 Azure 自动化 State Configuration 中删除计算机，请按照[如何从自动化 State Configuration 中删除配置和节点](../../../automation/state-configuration/remove-node-and-configuration-package.md)页面中的步骤进行操作。

若要使用来宾配置分配配置，请按照 Azure Policy 快速入门（例如[快速入门：创建策略分配以识别不合规资源](../assign-policy-portal.md)）中的步骤进行操作。
在步骤 6 中选择策略定义时，请选取应用从 Azure 自动化 State Configuration 迁移的配置的定义。

### <a name="hybrid-machines"></a>混合计算机

Azure 外部的计算机[可以注册到 Azure 自动化 State Configuration](../../../automation/automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)，但它们在 Azure 中没有计算机资源。 与 Azure 自动化的连接由计算机内的本地 Configuration Manager 服务处理，节点的记录作为 Azure 自动化提供程序类型中的资源进行管理。

从 Azure 自动化 State Configuration 中删除计算机之前，请将每个节点作为[已启用 Azure Arc 的服务器](../../../azure-arc/servers/overview.md)进行加入。
加入 Azure Arc 会在 Azure 中创建计算机资源，以便可以通过 Azure Policy 管理计算机。 计算机可以随时加入 Azure Arc，不过可以使用 Azure 自动化 State Configuration 自动执行该过程。

可以使用 PowerShell DSC 将计算机注册到已启用 Azure Arc 的服务器。
有关详细信息，请查看页面[如何使用 Windows PowerShell DSC 安装 Connected Machine Agent](../../../azure-arc/servers/onboard-dsc.md)。
但请记住，对于每个自动化帐户，Azure 自动化 State Configuration 只能为每个计算机管理一个配置。 这意味着，可以选择导出、测试和准备来宾配置的内容，然后切换 Azure 自动化中的节点配置以加入 Azure Arc。作为最后一步，会从 Azure 自动化 State Configuration 中删除节点注册，转为仅通过来宾配置管理计算机状态。

## <a name="troubleshooting-issues-when-exporting-content"></a>导出内容时排查问题

提供了有关已知问题的详细信息

### <a name="exporting-configurations-results-in--character-in-file-name"></a>导出配置导致文件名中出现“\\”字符

如果在 MacOS/Linux 上使用 PowerShell，则在处理 `Export-AzAutomationDSCConfiguration` 输出的文件名时会遇到问题。

作为一种解决方法，一个模块已发布到 PowerShell 库，名为 [AADSCConfigContent](https://www.powershellgallery.com/packages/AADSCConfigContent/)。
该模块只有一个命令，该命令通过对服务发出 REST 请求来导出存储在 Azure 自动化中的配置内容。

## <a name="next-steps"></a>后续步骤

- 为来宾配置[创建包项目](./guest-configuration-create.md)。
- 从开发环境[测试包项目](./guest-configuration-create-test.md)。
- [发布包项目](./guest-configuration-create-publish.md)，以便计算机可以访问它。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](./guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](./determine-non-compliance.md#compliance-details-for-guest-configuration)。
