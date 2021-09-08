---
title: 针对来宾配置的 PowerShell Desired State Configuration 行为更改
description: 本文概述了用于通过 Azure Policy 向计算机交付配置更改的平台。
ms.date: 05/31/2021
ms.topic: how-to
ms.openlocfilehash: ee5165ea9e8a80fc31863389df018548859e9b20
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257160"
---
# <a name="changes-to-behavior-in-powershell-desired-state-configuration-for-guest-configuration"></a>针对来宾配置的 PowerShell Desired State Configuration 行为更改

在开始之前，最好先阅读[来宾配置](./guest-configuration.md)概述。

[提供本文档的视频演练](https://youtu.be/nYd55FiKpgs)。

来宾配置使用 [Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) 版本 3 来审核和配置计算机。 DSC 配置定义了计算机应处于的状态。 在来宾配置中实施 DSC 的方式存在许多明显差异。

## <a name="guest-configuration-uses-powershell-7-cross-platform"></a>来宾配置使用 PowerShell 7 跨平台

来宾配置旨在使管理 Windows 和 Linux 的体验保持一致。 在这两种操作系统环境中，具备 PowerShell DSC 知识的人员可以使用脚本技能创建和发布配置。

来宾配置仅使用 PowerShell DSC 版本 3，不依赖于先前实现的[适用于 Linux 的 DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 或该存储库中包含的“nx”提供程序。

来宾配置在适用于 Windows 的 PowerShell 7.1.3 和适用于 Linux 的 PowerShell 7.2 预览版 6 中运行。 从 7.2 版开始，`PSDesiredStateConfiguration` 模块不再是 PowerShell 安装的一部分，而是作为 [PowerShell 库中的模块](https://www.powershellgallery.com/packages/PSDesiredStateConfiguration)进行安装。

## <a name="multiple-configurations"></a>多重配置

来宾配置支持将多个配置分配给同一台计算机。 在来宾配置扩展的操作系统中，无需执行任何特殊步骤。 无需配置[部分配置](/powershell/scripting/dsc/pull-server/partialConfigs)。

## <a name="configuration-mode-is-set-in-the-package-artifact"></a>配置模式在包项目中设置

创建配置包时，将使用以下选项设置模式：

- Audit：验证计算机的合规性。 不做任何更改。
- AuditandSet：验证和修正计算机的合规性状态。
  如果计算机不合规，则进行更改。

此模式是在包中设置的，而不是在[本地 Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings) 服务中设置的，因为在分配了多个配置时，此模式可能因配置而异。

## <a name="parameter-support-through-azure-resource-manager"></a>通过 Azure 资源管理器支持参数

文件存储在计算机上时，由[来宾配置分配](guest-configuration-assignments.md)中的 `configurationParameter` 属性数组设置的参数会覆盖配置 MOF 文件中的静态文本。 利用参数，操作员可以从服务 API 控制自定义和更改，而无需在计算机内运行命令。

Azure Policy 中将值传递给来宾配置分配的参数必须为字符串类型。 即使 DSC 资源支持数组，也无法通过参数传递数组。

## <a name="sequence-of-events"></a>事件序列

当来宾配置审核或配置计算机时，Windows 和 Linux 都使用相同的事件序列。 显著的行为更改是服务调用 `Get` 方法，以返回有关计算机状态的详细信息。

1. 代理首先运行 `Test` 以确定配置是否处于正确状态。
1. 如果将包设置为 `Audit`，则该函数返回的布尔值确定来宾分配的 Azure 资源管理器状态是合规还是不合规。
1. 如果将包设置为 `AuditandSet`，则布尔值确定是否通过使用 `Set` 方法应用配置来修正计算机。
   如果 `Test` 方法返回 False，则 `Set` 运行。 如果 `Test` 返回 True，则 `Set` 不运行。
1. 最后，提供程序运行 `Get` 以返回每个设置的当前状态，因此，会获得有关计算机为何不合规的详细信息，以及用于确认当前状态是否合规的详细信息。

## <a name="trigger-set-from-outside-machine"></a>从外部计算机触发设置

以前版本的 DSC 中的一个挑战是在没有大量自定义代码和不依赖 WinRM 远程连接的情况下大规模纠正偏移。 来宾配置解决了这一问题。 来宾配置的用户可以通过[按需修正](./guest-configuration-policy-effects.md#remediation-on-demand-applyandmonitor)控制偏移校正。

## <a name="maximum-size-of-custom-configuration-package"></a>自定义配置包的最大大小

在 Azure 自动化状态配置中，DSC 配置的[大小受到限制](../../../automation/automation-dsc-compile.md#compile-your-dsc-configuration-in-windows-powershell)。
来宾配置支持的总包大小为 100 MB（压缩前）。 包内 MOF 文件的大小没有具体限制。

## <a name="special-requirements-for-get"></a>Get 的特殊要求

函数 `Get` 方法对 Azure Policy 来宾配置具有特殊要求，而 Windows PowerShell Desired State Configuration 不需要满足这些要求。

- 返回的哈希表应包含名为 Reasons 的属性。
- Reasons 属性必须是数组。
- 数组中的每个项都应是一个哈希表，其中包含名为 Code 和 Phrase 的键。

服务使用 Reasons 属性来标准化合规性信息的呈现方式。 可将 Reasons 中的每个项视为资源合规或不合规的一个“原因”。 该属性之所以是数组，是因为资源可能出于多种原因而不合规。

服务需要 Code 和 Phrase 属性。 创作自定义资源时，需将要显示为资源不合规原因的文本（通常为 stdout）设置为 Phrase 的值。
Code 具有特定格式设置要求，因此报告可以清楚地显示有关用于进行审核的资源的信息。 此解决方案使来宾配置可扩展。 只要输出可以作为 Phrase 属性的字符串值返回，便可以运行任何命令。

- Code（字符串）：资源的名称，重复该名称，后接一个不包含空格的短名称（作为原因标识符）。 这三个值应以冒号分隔，且不包含空格。
  - 例如 `registry:registry:keynotpresent`
- Phrase（字符串）：用户可读的文本，用于解释设置不合规的原因。
  - 例如 `The registry key $key isn't present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

### <a name="the-reasons-property-embedded-class"></a>Reasons 属性嵌入式类

在基于脚本的资源（仅限 Windows）中，Reason 类包含在架构 MOF 文件中，如下所示。

```mof
[ClassVersion("1.0.0.0")]
class Reason
{
  [Read] String Phrase;
  [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
  [Key, Description("Example description")] String Example;
  [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

在基于类的资源（Windows 和 Linux）中，`Reason` 类包含在 PowerShell 模块中，如下所示。 Linux 区分大小写，因此 Code 中的“C”和 Phrase 中的“P”必须大写。

```powershell
enum ensure {
  Absent
  Present
}

class Reason {
  [DscProperty()]
  [string] $Code

  [DscProperty()]
  [string] $Phrase
}

[DscResource()]
class Example {

  [DscProperty(Key)]
  [ensure] $ensure

  [DscProperty()]
  [Reason[]] $Reasons

  [Example] Get() {
    # return current current state
  }

  [void] Set() {
    # set the state
  }

  [bool] Test() {
    # check whether state is correct
  }
}

```

如果资源具有所需的属性，则 `Get` 还应与 `Reason` 类并行返回这些属性。 如果未包括 `Reason`，则该服务将包括“全部捕获”行为，该行为将输入到 `Get` 的值与 `Get` 返回的值进行比较，并将详细的比较结果作为 `Reason` 提供。

## <a name="configuration-names"></a>配置名称

自定义配置的名称必须在所有位置都保持一致。 内容包的 `.zip` 文件名、MOF 文件中的配置名称和 Azure 资源管理器模板中的来宾分配名称必须相同。

## <a name="common-dsc-features-not-available-during-guest-configuration-public-preview"></a>来宾配置公共预览版期间不可用的常见 DSC 功能

在公共预览版期间，来宾配置不支持使用“WaitFor*”资源[指定跨计算机依赖项](/powershell/scripting/dsc/configurations/crossnodedependencies)。 一台计算机不可能监视并等待另一台计算机达到某种状态后再继续运行。

来宾配置的公共预览版本中不提供[重新启动处理](/powershell/scripting/dsc/configurations/reboot-a-node)，同时 `$global:DSCMachineStatus` 也不可用。 配置无法在配置期间或结束时重新启动节点。

## <a name="known-compatibility-issues-with-supported-modules"></a>受支持模块的已知兼容性问题

PowerShell 库中的 `PsDscResources` 模块和 Windows 附带的 `PSDesiredStateConfiguration` 模块受 Microsoft 支持，并且已成为 DSC 的常用资源集。 在为 DSCv3 更新 `PSDscResources` 模块之前，请注意以下已知的兼容性问题。

- 不要使用 Windows 附带的 `PSDesiredStateConfiguration` 模块中的资源。 而是切换到 `PSDscResources`。
- 不要使用 `PsDscResources` 中的 `WindowsFeature` 和 `WindowsFeatureSet` 资源。 而是切换到 `WindowsOptionalFeature` 和 `WindowsOptionalFeatureSet` 资源。

## <a name="coexistance-with-dsc-version-3-and-previous-versions"></a>与 DSC 版本 3 和更早版本共存

来宾配置中的 DSC 版本 3 可以与 [Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) 和 [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted) 中安装的旧版本共存。
实现是独立的。 但是，DSC 版本之间没有冲突检测，因此请不要尝试管理相同的设置。

## <a name="next-steps"></a>后续步骤

- 阅读[来宾配置概述](./guest-configuration.md)。
- 设置自定义来宾配置包[开发环境](../how-to/guest-configuration-create-setup.md)。
- 为来宾配置[创建包项目](../how-to/guest-configuration-create.md)。
- 从开发环境[测试包项目](../how-to/guest-configuration-create-test.md)。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](../how-to/guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)。
