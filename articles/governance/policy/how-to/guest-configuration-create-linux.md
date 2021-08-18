---
title: 如何创建适用于 Linux 的来宾配置策略
description: 了解如何创建适用于 Linux 的 Azure Policy 来宾配置策略。
ms.date: 03/31/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 89f4e64f6448f93a4b746ae4301450707f832cde
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112287004"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>如何创建适用于 Linux 的来宾配置策略

创建自定义策略前，请先阅读 [Azure Policy 来宾配置](../concepts/guest-configuration.md)中的概述信息。

若要了解如何创建适用于 Windows 的来宾配置策略，请参阅[如何创建适用于 Windows 的来宾配置策略](./guest-configuration-create.md)页

当审核 Linux 时，来宾配置使用 [Chef InSpec](https://community.chef.io/tools/chef-inspec)。 InSpec 配置文件定义了计算机应处于的条件。 如果配置评估失败，则会触发策略效果 auditIfNotExists，并将计算机视为不符合。

[Azure Policy 来宾配置](../concepts/guest-configuration.md)只能用于审核计算机内部的设置。 还不能修正计算机内部的设置。

请执行以下操作来创建你自己的配置，用于验证 Azure 或非 Azure 计算机的状态。

> [!IMPORTANT]
> Azure 政府和 Azure 中国世纪互联环境中具有来宾配置的自定义策略定义是一项预览功能。
>
> 必须有来宾配置扩展，才能在 Azure 虚拟机中执行审核。 若要在所有 Linux 计算机上大规模部署扩展，请分配以下策略定义：`Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`
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
> Linux 上不支持编译配置。

### <a name="base-requirements"></a>基本要求

可以安装模块的操作系统：

- Linux
- macOS
- Windows

> [!NOTE]
> 由于 cmdlet `Test-GuestConfigurationPackage` 依赖于 OMI，因此它需要 OpenSSL 版本 1.0。 这会导致使用 OpenSSL 1.1 或更高版本的任何环境出现错误。
>
> 仅 Windows 上的来宾配置模块版本 2.1.0 支持运行 cmdlet `Test-GuestConfigurationPackage`。

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>适用于 Linux 的来宾配置项目和策略

即使在 Linux 环境中，来宾配置也使用 Desired State Configuration 作为语言抽象。 实现基于本机代码 (C++)，因此不需要加载 PowerShell。 不过，它需要描述环境详细信息的配置 MOF。
DSC 充当 InSpec 的包装器，用于标准化它的执行方式、参数提供方式，以及如何将输出返回到服务。 处理自定义 InSpec 内容时，需要掌握的 DSC 知识很少。

#### <a name="configuration-requirements"></a>配置要求

自定义配置的名称必须在所有位置都保持一致。 内容包的 .zip 文件名称、MOF 文件中的配置名称，以及 Azure 资源管理器模板 (ARM template) 中的来宾分配名称必须相同。

PowerShell cmdlet 可帮助创建包。 不需要根级别文件夹或版本文件夹。 包格式必须为 .zip 文件。 且未压缩时总大小不能超过 100 MB。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux 上的自定义来宾配置

Linux 上的来宾配置使用 `ChefInSpecResource` 资源为引擎提供 [InSpec 配置文件](https://docs.chef.io/inspec/profiles/)的名称。 “名称”是唯一必需的资源属性。 创建 YAML 文件和 Ruby 脚本文件，如下所详述。

首先，创建 InSpec 使用的 YAML 文件。 此文件提供了环境的基本信息。 下面给出了一个示例：

```yaml
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

将这个名为 `inspec.yml` 的文件保存到项目目录中名为 `linux-path` 的文件夹。

接下来，使用用于审核计算机的 InSpec 语言抽象来创建 Ruby 文件。

```ruby
describe file('/tmp') do
    it { should exist }
end
```

将这个名为 `linux-path.rb` 的文件保存到 `linux-path` 目录内名为 `controls` 的新文件夹。

最后，创建一个配置，导入 PSDesiredStateConfiguration 资源模块，然后编译配置。

```powershell
# import PSDesiredStateConfiguration module
import-module PSDesiredStateConfiguration

# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
AuditFilePathExists -out ./Config
```

将这个名为 `config.ps1` 的文件保存到项目文件夹中。 通过在终端中执行 `./config.ps1`，在 PowerShell 中运行它。 随即将创建新 MOF 文件。

从技术上讲，`Node AuditFilePathExists` 命令不是必需的，但它会生成一个名为 `AuditFilePathExists.mof`（而不是默认的 `localhost.mof`）的文件。 让 .MOF 文件名遵循配置，可以在大规模操作时轻松地组织许多文件。

你现在应该有如下所示的项目结构：

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        inspec.yml
        / controls
            linux-path.rb
```

支持文件必须打包在一起。 来宾配置使用已完成的包来创建 Azure Policy 定义。

`New-GuestConfigurationPackage` cmdlet 创建包。 创建 Linux 内容时 `New-GuestConfigurationPackage` cmdlet 的参数：

- **Name**：来宾配置包名称。
- **配置**：已编译的配置文档完整路径。
- **路径**：输出文件夹路径。 此参数是可选的。 如果未指定，则在当前目录中创建包。
- **ChefInspecProfilePath**：InSpec 配置文件的完整路径。 仅当创建内容来审核 Linux 时，才支持此参数。

运行下面的命令，以使用上一步中给出的配置来创建包：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

创建配置包之后、将其发布到 Azure 之前，可以从工作站或持续集成和持续部署 (CI/CD) 环境测试该包。 GuestConfiguration cmdlet `Test-GuestConfigurationPackage` 在开发环境中包含与 Azure 计算机内使用的相同的代理。 使用此解决方案，可以在发布到计费的云环境之前，在本地执行集成测试。

由于代理实际上是在评估本地环境，因此在大多数情况下，你需要在计划审核的同一 OS 平台上运行 Test- cmdlet。

`Test-GuestConfigurationPackage` cmdlet 的参数：

- **Name**：来宾配置策略名称。
- Parameter：以哈希表格式提供的策略参数。
- **路径**：来宾配置包的完整路径。

运行下面的命令，以测试由上一步创建的包：

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

此 cmdlet 还支持来自 PowerShell 管道的输入。 将 `New-GuestConfigurationPackage` cmdlet 的输出通过管道传输到 `Test-GuestConfigurationPackage` cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefInspecProfilePath './' | Test-GuestConfigurationPackage
```

下一步是将文件发布到 Azure Blob 存储。 命令 `Publish-GuestConfigurationPackage` 需要 `Az.Storage` 模块。

`Publish-GuestConfigurationPackage` cmdlet 的参数：

- **Path**：要发布的包的位置
- **ResourceGroupName**：存储帐户所在的资源组的名称
- **StorageAccountName**：应在其中发布包的存储帐户的名称
- **StorageContainerName**：（默认：guestconfiguration）存储帐户中的存储容器的名称
- **Force**：覆盖同名存储帐户中的现有包

以下示例将包发布到名为“guestconfiguration”的存储容器。

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditFilePathExists/AuditFilePathExists.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

`New-GuestConfigurationPolicy` 创建以下文件：

- auditIfNotExists.json

cmdlet 输出中会返回一个对象，其中包含策略文件的计划显示名称和路径。

最后，使用 `Publish-GuestConfigurationPolicy` cmdlet 发布策略定义。 cmdlet 只有 Path 参数，此参数指向 `New-GuestConfigurationPolicy` 创建的 JSON 文件的位置。

必须有权在 Azure 中创建策略，才能运行发布命令。 [Azure Policy 概述](../overview.md)页中收录了具体的授权要求。 最合适的内置角色是“资源策略参与者”。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path './policies'
```

`Publish-GuestConfigurationPolicy` cmdlet 接受来自 PowerShell 管道的路径。 此功能意味着可以创建策略文件，并在一组管道命令中发布它们。

```azurepowershell-interactive
New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
| Publish-GuestConfigurationPolicy
```

在 Azure 中创建策略后，最后一步是分配定义。 了解如何使用[门户](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md) 和 [Azure PowerShell](../assign-policy-powershell.md) 分配定义。

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>使用自定义来宾配置策略中的参数

来宾配置支持在运行时替代配置属性。 此功能意味着包中 MOF 文件内的值不必被认为是静态的。 替代值是通过 Azure Policy 提供的，并不会改变配置的创作或编译方式。

借助 InSpec，参数通常在运行时作为输入处理，或使用特性作为代码处理。 来宾配置令此过程变得混淆，因此可以在分配策略时提供输入。 特性文件在计算机中自动创建。 不需要在项目中创建和添加文件。 向 Linux 审核项目添加参数需要执行两个步骤。

在编写要在计算机上审核什么的脚本的 Ruby 文件中定义输入。 下面给出了一个示例。

```ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

在配置中添加“AttributesYmlContent”属性，将任意字符串作为该属性的值。 来宾配置代理自动创建 InSpec 用于存储特性的 YAML 文件。
请参阅以下示例。

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "fromParameter"
        }
    }
}
```

使用本文档中提供的示例重新编译 MOF 文件。

cmdlet `New-GuestConfigurationPolicy` 和 `Test-GuestConfigurationPolicyPackage` 包含名为“Parameter”的参数。 此参数需要使用包含每个参数的所有详细信息的哈希表，并自动创建用于创建每个 Azure Policy 定义的文件的所有必需部分。

以下示例创建一个用于审核文件路径的策略定义，其中，用户将在分配策略时提供路径。

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = 'File path to be audited.'      # Policy parameter description (optional)
        ResourceType = 'ChefInSpecResource'           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = 'AttributesYmlContent' # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

$uri = 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D'

New-GuestConfigurationPolicy -ContentUri $uri `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Platform 'Linux' `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>策略生命周期

如果要发布策略更新，请同时更改来宾配置包和 Azure Policy 定义详细信息。

> [!NOTE]
> 来宾配置分配的 `version` 属性仅影响 Microsoft 托管的包。 对自定义内容进行版本控制的最佳做法是在文件名中包含版本。

首先，在运行 `New-GuestConfigurationPackage` 时为包指定一个名称，使其与以前的版本不同。 可以在名称中包含版本号，例如 `PackageName_1.0.0`。 使用本示例中的数字只是为了让包独一无二，而不是指定该包应被视为比其他包更新或更旧。

接下来，按下面的每项说明更新与 `New-GuestConfigurationPolicy` cmdlet 一起使用的参数。

- **版本**：运行 `New-GuestConfigurationPolicy` cmdlet 时，必须指定高于当前发布版本的版本号。
- **contentUri**：运行 `New-GuestConfigurationPolicy` cmdlet 时，必须为包的位置指定一个 URI。 在文件名中包含包版本将确保此属性的值在每个版本中都会更改。
- contentHash：此属性由 `New-GuestConfigurationPolicy` cmdlet 自动更新。 它是 `New-GuestConfigurationPackage` 创建的包的哈希值。 对于你发布的 `.zip` 文件，此属性必须是正确的。 如果只更新了 contentUri 属性，扩展就不会接受内容包。

发布更新后的包的最简单方法是，重复本文中描述的过程，并提供更新后的版本号。 此过程保证所有属性都已正确更新。

### <a name="filtering-guest-configuration-policies-using-tags"></a>使用标记筛选来宾配置策略

来宾配置模块中由 cmdlet 创建的策略可以视需要选择包括标记筛选器。 `New-GuestConfigurationPolicy` 的 -Tag 参数支持包含各个标记条目的哈希表数组。 标记会被添加到策略定义的 `If` 部分，并且不能通过策略分配进行修改。

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
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>可选：对来宾配置包进行签名

来宾配置自定义策略使用 SHA256 哈希来验证策略包是否没有更改。
客户还可以选择使用证书对包进行签名，并强制来宾配置扩展只允许已签名的内容。

若要启用此方案，需要完成两个步骤。 运行 cmdlet 对内容包进行签名，并将标记追加到应需要对代码进行签名的计算机。

若要使用签名验证功能，请运行 `Protect-GuestConfigurationPackage` cmdlet，以在发布前对包进行签名。 此 cmdlet 需要“代码签名”证书。

`Protect-GuestConfigurationPackage` cmdlet 的参数：

- **路径**：来宾配置包的完整路径。
- PublicGpgKeyPath：公共 GPG 密钥路径。 只有在对 Linux 内容进行签名时，才支持此参数。

GitHub 上的文章[生成新 GPG 密钥](https://help.github.com/en/articles/generating-a-new-gpg-key)为创建用于 Linux 计算机的 GPG 密钥提供了很好的参考。

GuestConfiguration 代理需要在 Linux 计算机上的路径 `/usr/local/share/ca-certificates/extra` 中显示证书公钥。 为了让节点能够验证已签名的内容，请先在计算机上安装证书公钥，再应用自定义策略。 可以使用 VM 内的任何技术或使用 Azure Policy 来完成此过程。 [此处提供了](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-push-certificate-windows)一个示例模板。
Key Vault 访问策略必须允许计算资源提供程序在部署过程中访问证书。 有关详细步骤，请参阅[在 Azure 资源管理器中为虚拟机设置 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

在内容发布后，将名为 `GuestConfigPolicyCertificateValidation` 且值为 `enabled` 的标记追加到所有应需要进行代码签名的虚拟机。 请参阅[标记示例](../samples/built-in-policies.md#tags)，了解如何使用 Azure Policy 大规模传递标记。 在此标记就位后，使用 `New-GuestConfigurationPolicy` cmdlet 生成的策略定义通过来宾配置扩展启用要求。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[来宾配置](../concepts/guest-configuration.md)审核 VM。
- 了解如何[以编程方式创建策略](./programmatically-create.md)。
- 了解如何[获取符合性数据](./get-compliance-data.md)。
