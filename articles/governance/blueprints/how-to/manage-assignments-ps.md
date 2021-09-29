---
title: 如何使用 PowerShell 管理分配
description: 了解如何通过正式的 Azure 蓝图 PowerShell 模块 Az.Blueprint 管理蓝图分配。
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: 0ce2132fe9d43ef0d5df1a4e0eec3a80a3a5c5ca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612807"
---
# <a name="how-to-manage-assignments-with-powershell"></a>如何使用 PowerShell 管理分配

可以使用 Azure PowerShell 模块 Az.Blueprint 管理蓝图分配。 此模块支持提取、创建、更新和删除分配。 此模块还可以提取现有蓝图定义的详细信息。 本文介绍了如何安装此模块以及如何开始使用它。

## <a name="add-the-azblueprint-module"></a>添加 Az.Blueprint 模块

必须添加 Azure PowerShell，才能启用此模块来管理蓝图分配。 此模块可以与在本地安装的 PowerShell 以及 [Azure Cloud Shell](https://shell.azure.com) 一起使用，也可以与 [Azure PowerShell Docker 映像](/powershell/azure/azureps-in-docker)一起使用。

### <a name="base-requirements"></a>基本要求

Azure 蓝图模块需要以下软件：

- Azure PowerShell 1.5.0 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/azure/install-az-ps)。
- PowerShellGet 2.0.1 或更高版本。 若尚未安装或更新，请遵循[这些说明](/powershell/scripting/gallery/installing-psget)。

### <a name="install-the-module"></a>安装模块

用于 PowerShell 的 Azure 蓝图模块是 Az.Blueprint。

1. 从管理 PowerShell 提示符运行以下命令：

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > 如果已安装 Az.Accounts，则可能需要使用 `-AllowClobber` 来强制执行安装。

1. 验证该模块是否已导入且是否为正确版本 (0.2.6)：

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>获取蓝图定义

处理分配的第一步通常是获取对蓝图定义的引用。
`Get-AzBlueprint` cmdlet 可获取一个或多个蓝图定义。 此 cmdlet 可以使用 `-ManagementGroupId {mgId}` 从管理组获取蓝图定义，也可以使用 `-SubscriptionId {subId}` 从订阅获取。 “Name”参数可获取蓝图定义，但必须与 ManagementGroupId 或 SubscriptionId 一起使用  。 “Version”可以与“Name”一起使用，以便进一步明确要返回的蓝图定义。  开关 `-LatestPublished`（而非“Version”）可以获取最新发布的版本。

下面的示例使用 `Get-AzBlueprint` 从 `{subId}` 表示的特定订阅中获取蓝图定义“101-blueprints-definition-subscription”的所有版本：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

具有多个版本的蓝图定义的输出如下所示：

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

可以展开蓝图定义的[蓝图参数](../concepts/parameters.md#blueprint-parameters)，以便提供更多信息。

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>获取蓝图分配

如果蓝图分配已存在，可以使用 `Get-AzBlueprintAssignment` cmdlet 获取对它的引用。 此 cmdlet 采用 SubscriptionId 和 Name 作为可选参数。  如果未指定 SubscriptionId，则将使用当前的订阅上下文。

下面的示例使用 `Get-AzBlueprintAssignment` 从 `{subId}` 表示的特定订阅获取单个蓝图分配“Assignment-lock-resource-groups”：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

蓝图分配的输出如下所示：

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>创建蓝图分配

如果蓝图分配尚不存在，则可以使用 `New-AzBlueprintAssignment` cmdlet 创建它。 此 cmdlet 使用以下参数：

- Name [必填]
  - 指定蓝图分配的名称
  - 必须是唯一的，并且不是 SubscriptionId 中已存在的
- Blueprint [必填]
  - 指定要分配的蓝图定义
  - 使用 `Get-AzBlueprint` 获取引用对象
- Location [必填]
  - 指定区域，以便在其中创建系统分配的托管标识和订阅部署对象
- Subscription（可选）
  - 指定要将分配部署到的订阅
  - 如果未提供，则默认为当前订阅上下文
- Lock（可选）
  - 定义要用于部署的资源的[蓝图资源锁](../concepts/resource-locking.md)
  - 支持的选项有：None、AllResourcesReadOnly、AllResourcesDoNotDelete  
  - 如果未提供，则默认为 None
- SystemAssignedIdentity（可选）
  - 选择此参数创建系统分配的托管标识，以用于分配并用于部署资源
  - “Identity”参数集的默认值
  - 不能与 UserAssignedIdentity 一起使用
- UserAssignedIdentity （可选）
  - 指定用户分配的托管标识，以用于分配并用于部署资源
  - “identity”参数集的一部分
  - 不能与 SystemAssignedIdentity 一起使用
- Parameter（可选）
  - 用于设置蓝图分配的[动态参数](../concepts/parameters.md#dynamic-parameters)的键/值对的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 动态参数的默认值是定义中的 defaultValue
  - 如果某个参数未提供且没有 defaultValue，则此参数不是可选的

    > [!NOTE]
    > Parameter 不支持 secureStrings。

- ResourceGroupParameter（可选）
  - 资源组项目的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每个资源组项目占位符都具有用于动态设置该资源组项目的 Name 和 Location 的键/值对 
  - 如果某个资源组参数未提供并且没有 defaultValue，则此资源组参数不是可选的
- AssignmentFile（可选）
  - 蓝图分配的 JSON 文件表示形式的路径
  - 此参数是 PowerShell 参数集的一部分，该参数集仅包含 Name、Blueprint 和 SubscriptionId 以及常用参数  。

### <a name="example-1-provide-parameters"></a>示例 1：提供参数

下面的示例为使用 `Get-AzBlueprint` 提取的“my-blueprint”蓝图定义版本“1.1”创建新的分配，设置托管标识并将分配对象位置后设置为“westus2”，使用 AllResourcesReadOnly 锁定资源，并在 `{subId}` 表示的特定订阅中设置 Parameter 和 ResourceGroupParameter 的哈希表 ：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

创建蓝图分配的输出如下所示：

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>示例 2：使用 JSON 分配定义文件

下面的示例创建了几乎与[示例 1](#example-1-provide-parameters)的输出相同的分配。 该示例说明了如何使用 JSON 分配定义文件和 AssignmentFile 参数，而未将参数传递给 cmdlet。 此外，它将 excludedPrincipals 属性配置为锁的一部分。  excludedPrincipals 没有相应的 PowerShell 参数，若要配置此属性，只能通过 JSON 分配定义文件设置它。

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

有关用户分配的托管标识的 JSON 分配定义文件的示例，请参阅适用于 REST API 的[示例：用户分配的托管标识的分配](/rest/api/blueprints/assignments/createorupdate#examples)中的请求正文。

## <a name="update-blueprint-assignments"></a>更新蓝图分配

有时需要更新已创建的蓝图分配。 `Set-AzBlueprintAssignment` cmdlet 可处理此操作。 该 cmdlet 将使用 `New-AzBlueprintAssignment` cmdlet 所用的大部分相同参数，以便更新在分配上设置的任何内容。 其中不包括以下参数：Name、Blueprint 和 SubscriptionId  。 它将仅更新所提供的值。

若要了解更新蓝图分配时所执行的操作，请参阅[更新分配的规则](./update-existing-assignments.md#rules-for-updating-assignments)。

- Name [必填]
  - 指定要更新的蓝图分配的名称
  - 用于查找要更新的分配，而不用于更改分配
- Blueprint [必填]
  - 指定蓝图分配的蓝图定义
  - 使用 `Get-AzBlueprint` 获取引用对象
  - 用于查找要更新的分配，而不用于更改分配
- Location（可选）
  - 指定区域，以便在其中创建系统分配的托管标识和订阅部署对象
- Subscription（可选）
  - 指定要将分配部署到的订阅
  - 如果未提供，则默认为当前订阅上下文
  - 用于查找要更新的分配，而不用于更改分配
- Lock（可选）
  - 定义要用于部署的资源的[蓝图资源锁](../concepts/resource-locking.md)
  - 支持的选项有：None、AllResourcesReadOnly、AllResourcesDoNotDelete  
- SystemAssignedIdentity（可选）
  - 选择此参数创建系统分配的托管标识，以用于分配并用于部署资源
  - “Identity”参数集的默认值
  - 不能与 UserAssignedIdentity 一起使用
- UserAssignedIdentity （可选）
  - 指定用户分配的托管标识，以用于分配并用于部署资源
  - “identity”参数集的一部分
  - 不能与 SystemAssignedIdentity 一起使用
- Parameter（可选）
  - 用于设置蓝图分配的[动态参数](../concepts/parameters.md#dynamic-parameters)的键/值对的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 动态参数的默认值是定义中的 defaultValue
  - 如果某个参数未提供且没有 defaultValue，则此参数不是可选的

    > [!NOTE]
    > Parameter 不支持 secureStrings。

- ResourceGroupParameter（可选）
  - 资源组项目的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每个资源组项目占位符都具有用于动态设置该资源组项目的 Name 和 Location 的键/值对 
  - 如果某个资源组参数未提供并且没有 defaultValue，则此资源组参数不是可选的

下面的示例通过更改锁模式更新使用 `Get-AzBlueprint` 提取的“my-blueprint”蓝图定义版本“1.1”的分配：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

创建蓝图分配的输出如下所示：

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>删除蓝图分配

要删除蓝图分配时，`Remove-AzBlueprintAssignment` cmdlet 将处理此操作。 该 cmdlet 使用 Name 或 InputObject 来指定要删除的蓝图分配 。 SubscriptionId 是必需的参数，在任何情况下都必须提供它。

下面的示例使用 `Get-AzBlueprintAssignment` 提取现有的蓝图分配，然后从 `{subId}` 表示的特定订阅中删除它：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="code-example"></a>代码示例

通过将所有步骤组合在一起，以下示例将获取蓝图定义，然后在 `{subId}` 表示的特定订阅中创建、更新和删除蓝图分配：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。
