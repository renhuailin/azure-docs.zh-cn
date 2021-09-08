---
title: 如何创建自定义来宾配置策略定义
description: 了解如何创建来宾配置策略。
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 28ad60284912261510a55438919924138d4e2b5e
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868352"
---
# <a name="how-to-create-custom-guest-configuration-policy-definitions"></a>如何创建自定义来宾配置策略定义

开始之前，最好阅读[来宾配置](../concepts/guest-configuration.md)的概述页以及[如何为来宾配置配置修正选项](../concepts/guest-configuration-policy-effects.md)中有关来宾配置策略影响的详细信息。

> [!IMPORTANT]
> Azure 虚拟机需要安装来宾配置扩展。 若要在所有计算机上大规模部署此扩展，请分配以下策略计划：`Deploy prerequisites to enable guest configuration policies on
> virtual machines`
> 
> 若要使用应用配置的来宾配置包，需要安装 Azure VM 来宾配置扩展版本 1.29.24 或更高版本，或 Arc 代理 1.10.0 或更高版本 。
>
> 使用 AuditIfNotExists 的自定义来宾配置策略定义已正式发布，但使用 DeployIfNotExists 的来宾配置定义目前以预览版提供  。

使用以下步骤创建你自己的策略，这些策略可审核合规性或管理 Azure 计算机或启用了 Arc 的计算机的状态。

## <a name="install-powershell-7-and-required-powershell-modules"></a>安装 PowerShell 7 和所需的 PowerShell 模块

首先，请确保已按照[如何设置来宾配置创作环境](./guest-configuration-create-setup.md)页上的所有步骤来安装 OS 和 `GuestConfiguration` 模块所需的 PowerShell 版本。

## <a name="create-and-publish-a-guest-configuration-package-artifact"></a>创建和发布来宾配置包项目

如果尚未创建和发布，请按照[如何创建自定义来宾配置包项目](./guest-configuration-create.md)页上的所有步骤创建和发布自定义来宾配置包，并按照[如何测试来宾配置包项目](./guest-configuration-create-test.md)在开发环境中本地验证来宾配置包。

## <a name="policy-requirements-for-guest-configuration"></a>来宾配置的策略要求

策略定义 `metadata` 部分必须包括来宾配置服务的两个属性，以自动预配和报告来宾配置分配。 `category` 属性必须设置为“来宾配置”，并且名为 `guestConfiguration` 的部分必须包含有关来宾配置分配的信息。 `New-GuestConfigurationPolicy` cmdlet 会自动创建此文本。

下面的示例演示由 `New-GuestConfigurationPolicy` 自动创建的 `metadata` 部分。

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

`category` 属性必须设置为“来宾配置”。 如果定义效果设置为“DeployIfNotExists”，则 `then` 部分必须包含有关来宾配置分配的部署详细信息。 `New-GuestConfigurationPolicy` cmdlet 会自动创建此文本。

### <a name="create-an-azure-policy-definition"></a>创建 Azure Policy 定义

在创建并上传来宾配置自定义策略包后，创建来宾配置策略定义。 `New-GuestConfigurationPolicy` cmdlet 需要使用自定义策略包，并创建策略定义。

PolicyId 参数 `New-GuestConfigurationPolicy` 需要唯一字符串。 建议使用全局唯一标识符 (GUID)。 对于新定义，使用 cmdlet `New-GUID` 生成新 GUID。 对定义进行更新时，对 PolicyId 使用相同的唯一字符串，以确保更新正确的定义。

`New-GuestConfigurationPolicy` cmdlet 的参数：

- PolicyId：GUID 或标识定义的其他唯一字符串。
- ContentUri：来宾配置内容包的公共 HTTP(S) URI。
- DisplayName：策略显示名称。
- **说明**：策略说明。
- Parameter：以哈希表格式提供的策略参数。
- **版本**：策略版本。
- **路径**：在其中创建策略定义的目标路径。
- Platform：来宾配置策略和内容包的目标平台 (Windows/Linux)。
- Mode：（ApplyAndMonitor、ApplyAndAutoCorrect、Audit）选择策略是应审核配置还是部署配置。 默认设置为“审核”。
- Tag 向策略定义添加一个或多个标记筛选器
- Category 在策略定义中设置类别元数据字段

有关“Mode”参数的详细信息，请参阅[如何为来宾配置配置修正选项](../concepts/guest-configuration-policy-effects.md)页面。

创建一个策略定义，该定义使用指定路径中的自定义配置包进行审核：

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID'
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Verbose
```

创建一个策略定义，该定义使用指定路径中的自定义配置包部署配置：

```powershell
New-GuestConfigurationPolicy `
  -PolicyId 'My GUID'
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'My audit policy.' `
  -Description 'Details about my policy.' `
  -Path './policies' `
  -Platform 'Windows' `
  -Version 1.0.0 `
  -Mode 'ApplyAndAutoCorrect' `
  -Verbose
```

cmdlet 输出返回一个对象，其中包含策略文件的定义显示名称和路径。 创建审核策略定义的定义 JSON 文件称为 auditIfNotExists.json，创建策略定义以应用配置的文件称为 deployIfNotExists.json 。

#### <a name="filtering-guest-configuration-policies-using-tags"></a>使用标记筛选来宾配置策略

来宾配置中由 cmdlet 创建的策略定义可以视需要选择包括标记筛选器。 `New-GuestConfigurationPolicy` 的 Tag 参数支持包含各个标记条目的哈希表数组。 标记会添加到策略定义的 `If` 部分，并且不能通过策略分配进行修改。

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
      // Original guest configuration content
    }
  ]
}
```

#### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>使用自定义来宾配置策略定义中的参数

来宾配置支持在运行时替代配置属性。 此功能意味着包中 MOF 文件内的值不必被认为是静态的。 替代值是通过 Azure Policy 提供的，并不会改变配置的创作或编译方式。

cmdlet `New-GuestConfigurationPolicy` 和 `Get-GuestConfigurationPacakgeComplianceStatus ` 包含名为 Parameter 的参数。 此参数需要使用包含每个参数的所有详细信息的哈希表定义，并创建用于 Azure Policy 定义的每个文件的必需部分。

下面的示例创建策略定义来审核服务，其中用户在策略分配时从列表中进行选择。

```powershell
# This DSC Resource text:
Service 'UserSelectedNameExample'
  {
    Name = 'ParameterValue'
    Ensure = 'Present'
    State = 'Running'
  }`

# Would require the following hashtable:
$PolicyParameterInfo = @(
  @{
    Name = 'ServiceName'                                           # Policy parameter name (mandatory)
    DisplayName = 'windows service name.'                          # Policy parameter display name (mandatory)
    Description = 'Name of the windows service to be audited.'     # Policy parameter description (optional)
    ResourceType = 'Service'                                       # DSC configuration resource type (mandatory)
    ResourceId = 'UserSelectedNameExample'                         # DSC configuration resource id (mandatory)
    ResourcePropertyName = 'Name'                                  # DSC configuration resource property name (mandatory)
    DefaultValue = 'winrm'                                         # Policy parameter default value (optional)
    AllowedValues = @('BDESVC','TermService','wuauserv','winrm')   # Policy parameter allowed values (optional)
  }
)

New-GuestConfigurationPolicy
  -PolicyId 'My GUID'
  -ContentUri '<paste the ContentUri output from the Publish command>' `
  -DisplayName 'Audit Windows Service.' `
  -Description 'Audit if a Windows Service isn't enabled on Windows machine.' `
  -Path '.\policies' `
  -Parameter $PolicyParameterInfo `
  -Version 1.0.0
```

### <a name="publish-the-azure-policy-definition"></a>发布 Azure Policy 定义

最后，使用 `Publish-GuestConfigurationPolicy` cmdlet 发布策略定义。 cmdlet 只有 Path 参数，此参数指向 `New-GuestConfigurationPolicy` 创建的 JSON 文件的位置。

若要运行 Publish 命令，需要具有在 Azure 中创建策略定义的访问权限。 [Azure Policy 概述](../overview.md)页中收录了具体的授权要求。 建议的内置角色是“资源策略参与者”。

```powershell
Publish-GuestConfigurationPolicy -Path '.\policies'
```

在 Azure 中创建策略定义后，最后一步是分配定义。 了解如何使用[门户](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md) 和 [Azure PowerShell](../assign-policy-powershell.md) 分配定义。

### <a name="optional-piping-output-from-each-command-to-the-next"></a>可选：通过管道将每个命令的输出传送到下一个命令

来宾配置中的命令按名称支持管道参数。
可以使用 `|` 运算符通过管道将每个命令的输出传送到下一个命令。
快速循环访问时，管道传送在开发人员环境中很有用，因为你不需要复制/粘贴每个命令的输出。

使用 `|` 运算符运行序列：

```powershell
# End to end flow piping output of each command to the next
$ConfigName         = myConfigName
$ResourceGroupName  = myResourceGroupName
$StorageAccountName = myStorageAccountName
$DisplayName        = 'Configure Linux machine per my scenario.'
$Description        = 'Details about my policy.'
New-GuestConfigurationPackage -Name $ConfigName -Configuration ./$ConfigName.mof -Path ./package/ -Type AuditAndSet -Force |
Publish-GuestConfigurationPackage -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName -Force |
New-GuestConfigurationPolicy -PolicyId 'My GUID' -DisplayName $DisplayName -Description $Description -Path './policies' -Platform 'Linux' -Version 1.0.0 -Mode 'ApplyAndAutoCorrect' |
Publish-GuestConfigurationPolicy
```

## <a name="policy-lifecycle"></a>策略生命周期

如果要发布策略定义更新，请同时更改来宾配置包和 Azure Policy 定义详细信息。

> [!NOTE]
> 来宾配置分配的 `version` 属性仅影响 Microsoft 托管的包。 对自定义内容进行版本控制的最佳做法是在文件名中包含版本。

首先，在运行 `New-GuestConfigurationPackage` 时为包指定一个名称，使其与以前的版本不同。 可以在名称中包含版本号，例如 `PackageName_1.0.0`。 使用本示例中的数字只是为了让包独一无二，而不是指定该包应被视为比其他包更新或更旧。

接下来，按下面的每项说明更新与 `New-GuestConfigurationPolicy` cmdlet 一起使用的参数。

- **版本**：运行 `New-GuestConfigurationPolicy` cmdlet 时，必须指定高于当前发布版本的版本号。
- **contentUri**：运行 `New-GuestConfigurationPolicy` cmdlet 时，必须为包的位置指定一个 URI。 在文件名中包含包版本将确保此属性的值在每个版本中都会更改。
- contentHash：此属性由 `New-GuestConfigurationPolicy` cmdlet 自动更新。 它是 `New-GuestConfigurationPackage` 创建的包的哈希值。 对于你发布的 `.zip` 文件，此属性必须是正确的。 如果只更新了 contentUri 属性，扩展就不会接受内容包。

发布更新后的包的最简单方法是，重复本文中描述的过程，并提供更新后的版本号。 该过程可保证正确更新所有属性。

## <a name="next-steps"></a>后续步骤

- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](./determine-non-compliance.md#compliance-details-for-guest-configuration)。
