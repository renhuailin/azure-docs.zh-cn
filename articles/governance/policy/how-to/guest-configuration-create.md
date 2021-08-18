---
title: 如何创建适用于 Windows 的来宾配置策略
description: 了解如何创建适用于 Windows 的 Azure Policy 来宾配置策略。
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 78607b3e1694ecbfb8be58c4415d3dd79c4ac53b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112286968"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>如何创建适用于 Windows 的来宾配置策略

创建自定义策略定义之前，最好参阅 [Azure Policy 来宾配置](../concepts/guest-configuration.md)页中的概念性概述信息。

若要了解如何创建适用于 Linux 的来宾配置策略，请参阅[如何创建适用于 Linux 的来宾配置策略](./guest-configuration-create-linux.md)页

审核 Windows 时，来宾配置使用 [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) 资源模块创建配置文件。 DSC 配置定义了计算机应处于的条件。 如果配置评估失败，则会触发策略效果 auditIfNotExists，并将计算机视为不符合。

[Azure Policy 来宾配置](../concepts/guest-configuration.md)只能用于审核计算机内部的设置。 还不能修正计算机内部的设置。

请执行以下操作来创建你自己的配置，用于验证 Azure 或非 Azure 计算机的状态。

> [!IMPORTANT]
> Azure 政府和 Azure 中国世纪互联环境中具有来宾配置的自定义策略定义是一项预览功能。
>
> 必须有来宾配置扩展，才能在 Azure 虚拟机中执行审核。 若要在所有 Windows 计算机上大规模部署该扩展，请分配以下策略定义：`Deploy prerequisites to enable Guest Configuration Policy on Windows VMs`
>
> 不要在自定义内容包中使用机密或保密信息。

## <a name="install-the-powershell-module"></a>安装 PowerShell 模块

来宾配置模块自动执行创建自定义内容的过程，包括：

- 创建来宾配置内容项目 (.zip)
- 自动测试项目
- 创建策略定义
- 发布策略

此模块可以安装在以下计算机上：运行 Windows、macOS 或 Linux，包含在本地运行的 PowerShell 6.2 或更高版本，或包含 [Azure Cloud Shell](https://shell.azure.com) 或 [Azure PowerShell Core Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell-core)。

> [!NOTE]
> Linux 上尚不支持编译配置。

### <a name="base-requirements"></a>基本要求

可以安装模块的操作系统：

- Linux
- macOS
- Windows

来宾配置资源模块需要以下软件：

- PowerShell 6.2 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/scripting/install/installing-powershell)。
- Azure PowerShell 1.5.0 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/azure/install-az-ps)。
  - 只有 Az 模块“Az.Accounts”和“Az.Resources”是必需的。

### <a name="install-the-module"></a>安装模块

若要在 PowerShell 中安装 GuestConfiguration 模块，请执行以下操作：

1. 在 PowerShell 提示符下，运行以下命令：

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 验证模块是否已导入：

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>适用于 Windows 的来宾配置项目和策略

来宾配置使用 PowerShell Desired State Configuration 作为语言抽象来编写要在 Windows 中审核的内容。 代理会加载 PowerShell 6.2 的独立实例，因此，在 Windows PowerShell 5.1 中使用 PowerShell DSC 不会产生冲突，并且不要求预先安装 PowerShell 6.2 或更高版本。

有关 DSC 概念和术语的概述，请参阅 [PowerShell DSC 概述](/powershell/scripting/dsc/overview/overview)。

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>来宾配置模块与 Windows PowerShell DSC 模块的区别

当“来宾配置”审核计算机时，事件的顺序与在 Windows PowerShell DSC 中不同。

1. 代理首先运行 `Test-TargetResource` 以确定配置是否处于正确状态。
1. 该函数返回的布尔值确定来宾分配的 Azure 资源管理器状态是合规还是不合规。
1. 提供程序运行 `Get-TargetResource` 以返回每个设置的当前状态，因此，会获得有关计算机为何不合规的详细信息，以及用于确认当前状态是否合规的详细信息。

Azure 策略中将值传递给“来宾配置”分配信息的参数必须为字符串类型。 即使 DSC 资源支持数组，也无法通过参数传递数组。

### <a name="get-targetresource-requirements"></a>Get-TargetResource 要求

函数 `Get-TargetResource` 对来宾配置具有特殊要求，而 Windows Desired State Configuration 不需要满足这些要求。

- 返回的哈希表必须包含名为 Reasons 的属性。
- Reasons 属性必须是数组。
- 数组中的每个项都应是一个哈希表，其中包含名为 Code 和 Phrase 的键。

当计算机不合规时，服务使用 Reasons 属性来标准化信息的呈现方式。 可将 Reasons 中的每个项视为资源不合规的一个“原因”。 该属性之所以是数组，是因为资源可能出于多种原因而不合规。

服务需要 Code 和 Phrase 属性。 创作自定义资源时，需将要显示为资源不合规原因的文本（通常为 stdout）设置为 Phrase 的值。 Code 具有特定格式设置要求，因此报告可以清楚地显示有关用于进行审核的资源的信息。 此解决方案使来宾配置可扩展。 只要输出可以作为 Phrase 属性的字符串值返回，便可以运行任何命令。

- Code（字符串）：资源的名称，重复该名称，后接一个不包含空格的短名称（作为原因标识符）。 这三个值应以冒号分隔，且不包含空格。
  - 例如 `registry:registry:keynotpresent`
- Phrase（字符串）：用户可读的文本，用于解释设置不合规的原因。
  - 例如 `The registry key $key is not present on the machine.`

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

必须将 Reasons 属性添加到嵌入类形式的资源的架构 MOF。

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

如果资源具有所需的属性，还必须通过 `Get-TargetResource` 与 `reasons` 类来返回这些属性。 如果未包括 `reasons`，则该服务将包括“全部捕获”行为，该行为将输入到 `Get-TargetResource` 的值与 `Get-TargetResource` 返回的值进行比较，并将详细的比较结果作为 `reasons` 提供。

### <a name="configuration-requirements"></a>配置要求

自定义配置的名称必须在所有位置都保持一致。 内容包的 .zip 文件名称、MOF 文件中的配置名称，以及 Azure 资源管理器模板 (ARM template) 中的来宾分配名称必须相同。

### <a name="policy-requirements"></a>策略要求

策略定义 `metadata` 部分必须包括来宾配置服务的两个属性，以自动预配和报告来宾配置分配。 `category` 属性必须设置为“来宾配置”，并且名为 `Guest Configuration` 的部分必须包含有关来宾配置分配的信息。 `New-GuestConfigurationPolicy` cmdlet 会自动创建此文本。
请参阅此页上的分步说明。

以下示例演示了 `metadata` 部分。

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

### <a name="scaffolding-a-guest-configuration-project"></a>搭建来宾配置项目

想要加速入门过程并从示例代码开始工作的开发人员可以安装名为“来宾配置项目”的社区项目。 该项目将安装 [Plaster](https://github.com/powershell/plaster) PowerShell 模块的模板。 此工具可用于搭建项目（包括工作配置和示例资源）以及一组 [Pester](https://github.com/pester/pester) 测试来验证项目。 该模板还包含适用于 Visual Studio Code 的任务运行程序，用于自动生成和验证来宾配置包。 有关详细信息，请参阅 GitHub 项目[来宾配置项目](https://github.com/microsoft/guestconfigurationproject)。

有关一般情况下使用配置的详细信息，请参阅[编写、编译和应用配置](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)。

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>来宾配置项目的预期内容

来宾配置使用已完成的包来创建 Azure Policy 定义。 包中包含：

- 作为 MOF 的已编译 DSC 配置
- 模块文件夹
  - GuestConfiguration 模块
  - DscNativeResources 模块
  - (Windows) MOF 所需的 DSC 资源模块

PowerShell cmdlet 可帮助创建包。 不需要根级别文件夹或版本文件夹。 包格式必须是 .zip 文件，未压缩时总大小不能超过 100 MB。

### <a name="storing-guest-configuration-artifacts"></a>存储来宾配置项目

.zip 包必须存储在可由托管虚拟机访问的位置。
示例包括 GitHub 存储库、Azure 存储库或 Azure 存储。 如果你不想使包公开，则可以在 URL 中包含 [SAS 令牌](../../../storage/common/storage-sas-overview.md)。 还可以为专用网络中的计算机实现[服务终结点](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)，不过此配置仅适用于访问包，而不适用于与服务通信。

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>逐步创建适用于 Windows 的自定义来宾配置审核策略

创建 DSC 配置以审核设置。 下面的 PowerShell 脚本示例创建一个名为 AuditBitLocker 的配置，导入 PsDscResources 资源模块，并使用 `Service` 资源审核正在运行的服务。 可以从 Windows 或 macOS 计算机执行配置脚本。

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

在 PowerShell 终端中运行此脚本，或在项目文件夹中使用名称 `config.ps1` 保存此文件。 通过在终端中执行 `./config.ps1`，在 PowerShell 中运行它。 随即将创建新 mof 文件。

从技术上讲，`Node AuditBitlocker` 命令不是必需的，但它会生成一个名为 `AuditBitlocker.mof`（而不是默认的 `localhost.mof`）的文件。 让 .mof 文件名遵循配置，可以在大规模操作时轻松地组织许多文件。

编译 MOF 后，支持文件必须打包在一起。 来宾配置使用已完成的包来创建 Azure Policy 定义。

`New-GuestConfigurationPackage` cmdlet 创建包。 配置所需的模块必须在 `$Env:PSModulePath` 中提供。 创建 Windows 内容时 `New-GuestConfigurationPackage` cmdlet 的参数：

- **Name**：来宾配置包名称。
- **配置**：已编译 DSC 配置文档完整路径。
- **路径**：输出文件夹路径。 此参数是可选的。 如果未指定，则在当前目录中创建包。

运行下面的命令，以使用上一步中给出的配置来创建包：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './AuditBitlocker/AuditBitlocker.mof'
```

创建配置包之后、将其发布到 Azure 之前，可以从工作站或持续集成和持续部署 (CI/CD) 环境测试该包。 GuestConfiguration cmdlet `Test-GuestConfigurationPackage` 在开发环境中包含与 Azure 计算机内使用的相同的代理。 使用此解决方案，可以在发布到计费的云环境之前，在本地执行集成测试。

由于代理实际上是在评估本地环境，因此在大多数情况下，你需要在计划审核的同一 OS 平台上运行 Test- cmdlet。 该测试仅使用内容包中包含的模块。

`Test-GuestConfigurationPackage` cmdlet 的参数：

- **Name**：来宾配置策略名称。
- Parameter：以哈希表格式提供的策略参数。
- **路径**：来宾配置包的完整路径。

运行下面的命令，以测试由上一步创建的包：

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

此 cmdlet 还支持来自 PowerShell 管道的输入。 将 `New-GuestConfigurationPackage` cmdlet 的输出通过管道传输到 `Test-GuestConfigurationPackage` cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./AuditBitlocker/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

下一步是将文件发布到 Azure Blob 存储。 对存储帐户没有特殊要求，但最好将该文件托管在计算机附近的某个区域中。 如果没有存储帐户，请使用以下示例。 下面的命令（包括 `Publish-GuestConfigurationPackage`）需要 `Az.Storage` 模块。

```azurepowershell-interactive
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

`Publish-GuestConfigurationPackage` cmdlet 的参数：

- **Path**：要发布的包的位置
- **ResourceGroupName**：存储帐户所在的资源组的名称
- **StorageAccountName**：应在其中发布包的存储帐户的名称
- **StorageContainerName**：（默认：guestconfiguration）存储帐户中的存储容器的名称
- **Force**：覆盖同名存储帐户中的现有包

以下示例将包发布到名为“guestconfiguration”的存储容器。

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

在创建并上传来宾配置自定义策略包后，创建来宾配置策略定义。 `New-GuestConfigurationPolicy` cmdlet 需要使用自定义策略包，并创建策略定义。

`New-GuestConfigurationPolicy` cmdlet 的参数：

- **ContentUri**：来宾配置内容包的公共 HTTP(S) URI。
- DisplayName：策略显示名称。
- **说明**：策略说明。
- Parameter：以哈希表格式提供的策略参数。
- **版本**：策略版本。
- **路径**：在其中创建策略定义的目标路径。
- Platform：来宾配置策略和内容包的目标平台 (Windows/Linux)。
- Tag 向策略定义添加一个或多个标记筛选器
- Category 在策略定义中设置类别元数据字段

下面的示例在自定义策略包的指定路径中创建策略定义：

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

`New-GuestConfigurationPolicy` 创建以下文件：

- auditIfNotExists.json

cmdlet 输出中会返回一个对象，其中包含策略文件的计划显示名称和路径。

最后，使用 `Publish-GuestConfigurationPolicy` cmdlet 发布策略定义。 cmdlet 只有 Path 参数，此参数指向 `New-GuestConfigurationPolicy` 创建的 JSON 文件的位置。

必须有权在 Azure 中创建策略，才能运行发布命令。 [Azure Policy 概述](../overview.md)页中收录了具体的授权要求。 最合适的内置角色是“资源策略参与者”。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policies'
```

`Publish-GuestConfigurationPolicy` cmdlet 接受来自 PowerShell 管道的路径。 此功能意味着可以创建策略文件，并在一组管道命令中发布它们。

```azurepowershell-interactive
New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
| Publish-GuestConfigurationPolicy
```

在 Azure 中创建策略后，最后一步是分配定义。 了解如何使用[门户](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md) 和 [Azure PowerShell](../assign-policy-powershell.md) 分配定义。

### <a name="filtering-guest-configuration-policies-using-tags"></a>使用标记筛选来宾配置策略

来宾配置模块中由 cmdlet 创建的策略定义可以视需要选择包括标记筛选器。 `New-GuestConfigurationPolicy` 的 Tag 参数支持包含各个标记条目的哈希表数组。 标记会添加到策略定义的 `If` 部分，并且不能通过策略分配进行修改。

下面给出了筛选标记的策略定义的示例代码片段。

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>使用自定义来宾配置策略定义中的参数

来宾配置支持在运行时替代配置属性。 此功能意味着包中 MOF 文件内的值不必被认为是静态的。 替代值是通过 Azure Policy 提供的，并不会改变配置的创作或编译方式。

cmdlet `New-GuestConfigurationPolicy` 和 `Test-GuestConfigurationPolicyPackage` 包含名为 Parameter 的参数。 此参数需要使用包含每个参数的所有详细信息的哈希表定义，并创建用于 Azure Policy 定义的每个文件的必需部分。

下面的示例创建策略定义来审核服务，其中用户在策略分配时从列表中进行选择。

```azurepowershell-interactive
# This DSC Resource text:
Service 'UserSelectedNameExample'
      {
          Name = 'ParameterValue'
          Ensure = 'Present'
          State = 'Running'
      }

# Would require the following hashtable:
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'UserSelectedNameExample'                          # DSC configuration resource id (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policies' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>使用第三方工具扩展来宾配置

可以扩展来宾配置的项目包以包含第三方工具。
扩展来宾配置要求开发两个组件。

- 处理与管理第三方工具相关的所有活动的 Desired State Configuration 资源
  - 安装
  - Invoke
  - 转换输出
- 供工具以本机方式使用的格式正确的内容

如果社区解决方案尚不存在，则 DSC 资源需要自定义开发。
可以通过在 PowerShell 库中搜索标记 [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22) 来发现社区解决方案。

> [!NOTE]
> 来宾配置扩展性是“自带许可”方案。 使用之前，请确保已满足任何第三方工具的条款和条件。

在开发环境中安装 DSC 资源之后，使用 `New-GuestConfigurationPackage` 的 FilesToInclude 参数在内容项目中包含第三方平台的内容。

## <a name="policy-lifecycle"></a>策略生命周期

如果要发布策略更新，请同时更改来宾配置包和 Azure Policy 定义详细信息。

> [!NOTE]
> 来宾配置分配的 `version` 属性仅影响 Microsoft 托管的包。 对自定义内容进行版本控制的最佳做法是在文件名中包含版本。

首先，在运行 `New-GuestConfigurationPackage` 时为包指定一个名称，使其与以前的版本不同。 可以在名称中包含版本号，例如 `PackageName_1.0.0`。 使用本示例中的数字只是为了让包独一无二，而不是指定该包应被视为比其他包更新或更旧。

接下来，按下面的每项说明更新与 `New-GuestConfigurationPolicy` cmdlet 一起使用的参数。

- **版本**：运行 `New-GuestConfigurationPolicy` cmdlet 时，必须指定高于当前发布版本的版本号。
- **contentUri**：运行 `New-GuestConfigurationPolicy` cmdlet 时，必须为包的位置指定一个 URI。 在文件名中包含包版本将确保此属性的值在每个版本中都会更改。
- contentHash：此属性由 `New-GuestConfigurationPolicy` cmdlet 自动更新。 它是 `New-GuestConfigurationPackage` 创建的包的哈希值。 对于你发布的 `.zip` 文件，此属性必须是正确的。 如果只更新了 contentUri 属性，扩展就不会接受内容包。

发布更新后的包的最简单方法是，重复本文中描述的过程，并提供更新后的版本号。 该过程可保证正确更新所有属性。

## <a name="optional-signing-guest-configuration-packages"></a>可选：为 Guest Configuration 包签名

来宾配置自定义策略使用 SHA256 哈希来验证策略包是否没有更改。
客户还可以选择使用证书对包进行签名，并强制来宾配置扩展只允许已签名的内容。

若要启用此方案，需要完成两个步骤。 运行 cmdlet 对内容包进行签名，并将标记追加到应需要对代码进行签名的计算机。

若要使用签名验证功能，请运行 `Protect-GuestConfigurationPackage` cmdlet，以在发布前对包进行签名。 此 cmdlet 需要“代码签名”证书。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` cmdlet 的参数：

- **路径**：来宾配置包的完整路径。
- Certificate：用于对包进行签名的代码签名证书。 只有在对 Windows 内容进行签名时，才支持此参数。

GuestConfiguration 代理要求证书公钥在 Windows 计算机上的“受信任的根证书颁发机构”和 Linux 计算机上的 `/usr/local/share/ca-certificates/extra` 路径中存在。 为了让节点能够验证已签名的内容，请先在计算机上安装证书公钥，再应用自定义策略。 可以使用 VM 内的任何技术或使用 Azure Policy 来完成此过程。 [此处提供了](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-push-certificate-windows)一个示例模板。
Key Vault 访问策略必须允许计算资源提供程序在部署过程中访问证书。 有关详细步骤，请参阅[在 Azure 资源管理器中为虚拟机设置 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

下面是从签名证书导出公钥以导入计算机的示例。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

在内容发布后，将名为 `GuestConfigPolicyCertificateValidation` 且值为 `enabled` 的标记追加到所有应需要进行代码签名的虚拟机。 请参阅[标记示例](../samples/built-in-policies.md#tags)，了解如何使用 Azure Policy 大规模传递标记。 在此标记就位后，使用 `New-GuestConfigurationPolicy` cmdlet 生成的策略定义通过来宾配置扩展启用要求。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[来宾配置](../concepts/guest-configuration.md)审核 VM。
- 了解如何[以编程方式创建策略](./programmatically-create.md)。
- 了解如何[获取符合性数据](./get-compliance-data.md)。
