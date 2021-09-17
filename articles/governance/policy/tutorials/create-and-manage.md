---
title: 教程：构建策略以强制实施符合性
description: 本教程中将使用策略来强制执行标准、控制成本、维护安全性并施加企业范围的设计原则。
ms.date: 08/17/2021
ms.topic: tutorial
ms.openlocfilehash: f7c8b2c84f2aa0c4186fbdf69d5a579033904bca
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324730"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>教程：创建和管理策略以强制实施符合性

了解如何在 Azure 中创建和管理策略对于保持与公司标准和服务级别协议的符合性来说非常重要。 本教程介绍如何使用 Azure Policy 来执行某些与在组织中创建、分配和管理策略相关的常见任务，例如：

> [!div class="checklist"]
> - 分配策略，对将来创建的资源强制执行条件
> - 创建并分配计划定义，跟踪多个资源的符合性
> - 解决不符合或遭拒绝的资源
> - 在组织中实施新策略

若要分配一个策略用于识别现有资源的当前符合性状态，请参阅快速入门文章。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="assign-a-policy"></a>分配策略

使用 Azure Policy 强制实施符合性的第一步是分配策略定义。 策略定义用于定义实施策略的条件，以及要达到的效果。 在本例中，分配名为“如果缺少，则从资源组继承标记”的内置策略定义，以将指定的标记及其值从父资源组添加到缺少该标记的新资源或更新的资源。

1. 转到 Azure 门户以分配策略。 搜索并选择“策略”。

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="在搜索栏中搜索“策略”的屏幕截图。" border="false":::

1. 选择“Azure Policy”页左侧的“分配”。 分配即为在特定范围内分配策略以供执行。

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="从“策略概述”页中选择“分配”节点的屏幕截图。" border="false":::

1. 在“策略 - 分配”页的顶部选择“分配策略” 。

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="在“分配”页上选择“分配策略”按钮的屏幕截图。" border="false":::

1. 在“分配策略”页和“基本信息”选项卡上，通过选择省略号并选择管理组或订阅，选择“范围”  。 或者，请选择一个资源组。 范围用于确定对其强制执行策略分配的资源或资源组。
   然后在“范围”页的底部选择“选择”。 

   此示例使用 Contoso 订阅。 你的订阅将有所不同。

1. 可基于“范围”排除资源。 “排除”从低于“范围”级别的一个级别开始。 “排除”是可选的，因此暂时将其留空。

1. 选择“策略定义”旁边的省略号打开可用定义的列表。 可以使用“内置”来筛选策略定义的 **类型**，以查看所有相关策略定义及其说明。

1. 选择“如果缺少，则从资源组继承标记”。 如果不能立即找到它，请在搜索框中键入“继承标记”，然后按 ENTER 或者选择搜索框的外部。
   找到并选择策略定义后，选择“可用定义”页底部的“选择”。 

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="选择策略定义时搜索筛选器的屏幕截图。":::

1. “分配名称”中自动填充了所选的策略名称，但可以更改它。 对于本例，请保留“如果缺少，则从资源组继承标记”。 还可根据需要添加“说明”。 该说明提供有关此策略分配的详细信息。

1. 让“策略强制”保持“启用”状态。 禁用时，此设置允许测试策略的结果，而不触发效果。 有关详细信息，请参阅[强制模式](../concepts/assignment-structure.md#enforcement-mode)。

1. 系统会根据登录的用户自动填充“分配者”。 此字段是可选字段，因此可输入自定义值。

1. 选择向导顶部的“参数”选项卡。

1. 对于“标记名称”，请输入“Environment”。

1. 选择向导顶部的“修正”选项卡。

1. 让“创建修正任务”处于取消选中状态。 使用此框可以创建一个任务来更改现有资源以及新资源或更新的资源。 有关详细信息，请参阅[修正资源](../how-to/remediate-resources.md)。

1. 系统会自动勾选“创建托管标识”，因为此策略定义使用 [modify](../concepts/effects.md#modify) 效果。 系统会根据策略定义自动将“权限”设置为“参与者”。 有关详细信息，请参阅[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)和[修正安全性工作原理](../how-to/remediate-resources.md#how-remediation-security-works)。

1. 选择向导顶部的“不合规消息”选项卡。

1. 将“不合规消息”设置为“此资源没有所需标记”。 当拒绝某个资源时或在常规评估期间针对不合规资源会显示此自定义消息。

1. 选择向导顶部的“查看 + 创建”选项卡。

1. 查看选项，然后在页面底部选择“创建”。

## <a name="implement-a-new-custom-policy"></a>实施新的自定义策略

分配内置的策略定义后，可以使用 Azure Policy 执行其他操作。 接下来创建一个新的自定义策略，确保在环境中创建的虚拟机不能处于 G 系列，以便节省成本。 这样，当组织中的用户每次尝试创建 G 系列的虚拟机时，请求将被拒绝。

1. 选择“Azure Policy”页左侧“创作”下的“定义” 。

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="“创作”组下“定义”页的屏幕截图。" border="false":::

1. 选择页面顶部的“+ 策略定义”。 此按钮会打开“策略定义”页。

1. 输入以下信息：

   - 策略定义保存到的管理组或订阅。 使用“定义位置”旁边的省略号进行选择。

     > [!NOTE]
     > 若要将此策略定义应用到多个订阅，则位置必须是策略要分配到的订阅所在的管理组。 对于计划定义，也需要确保这一点。

   - 策略定义的名称 - 需要非 G 系列中的 VM SKU
   - 想通过策略定义实现的操作的说明 - 此策略定义强制此范围内创建的所有虚拟机具有 G 系列以外的 SKU，以减少成本。
   - 从现有的选项（例如“计算”）中选择，或者为此策略定义创建新的类别。
   - 复制以下 JSON 代码并根据需要进行更新：
      - 策略参数。
      - 策略规则/条件，此示例中为 - VM SKU 大小等于 G 系列
      - 策略效果，此示例中为“拒绝”。

   JSON 应如下所示。 将修改后的代码粘贴到 Azure 门户。

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   策略规则中的 _field_ 属性必须是支持的值。 可以在[策略定义结构字段](../concepts/definition-structure.md#fields)上找到值的完整列表。 例如，别名为 `"Microsoft.Compute/VirtualMachines/Size"`。

   若要查看更多 Azure Policy 示例，请参阅 [Azure Policy 示例](../samples/index.md)。

1. 选择“保存”。

## <a name="create-a-policy-definition-with-rest-api"></a>使用 REST API 创建策略定义

可通过适用于 Azure Policy 定义的 REST API 来创建策略。 借助 REST API，可创建和删除策略定义，以及获取现有定义的相关信息。 若要创建策略定义，请使用以下示例：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

包括类似于下方示例的请求正文：

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>使用 PowerShell 创建策略定义

在继续完成 PowerShell 示例之前，请确保已安装最新版本的 Azure PowerShell Az 模块。

可使用 `New-AzPolicyDefinition` cmdlet 创建策略定义。

要在文件中创建策略定义，请将路径传递给该文件。 对于外部文件，请使用以下示例：

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

对于本地文件，请使用以下示例：

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

要使用内联规则创建策略定义，请使用以下示例：

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

输出存储在 `$definition` 对象中，这会在策略分配过程中使用。 以下示例创建包含参数的策略定义：

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>使用 PowerShell 查看策略定义

若要查看订阅中的所有策略定义，请运行以下命令：

```azurepowershell-interactive
Get-AzPolicyDefinition
```

此命令可返回所有可用的策略定义，包括内置策略。 返回的每个策略的格式如下：

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>使用 Azure CLI 创建策略定义

可以将 Azure CLI 与 `az policy definition` 命令结合使用来创建策略定义。 要使用内联规则创建策略定义，请使用以下示例：

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>使用 Azure CLI 查看策略定义

若要查看订阅中的所有策略定义，请运行以下命令：

```azurecli-interactive
az policy definition list
```

此命令可返回所有可用的策略定义，包括内置策略。 返回的每个策略的格式如下：

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>创建并分配计划定义

通过计划定义，可以组合某些策略定义以实现首要目标。 计划对分配范围内的资源进行评估，以确定其是否符合所包括的策略。 有关计划定义的详细信息，请参阅 [Azure Policy 概述](../overview.md)。

### <a name="create-an-initiative-definition"></a>创建计划定义

1. 选择“Azure Policy”页左侧“创作”下的“定义” 。

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="“创作”组下的“定义”页的屏幕截图。":::

1. 选择页面顶部的“+ 计划定义”，打开“计划定义”向导 。

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="要设置的计划定义页和属性的屏幕截图。":::

1. 使用“计划位置”旁边的省略号，选择用于存储定义的管理组或订阅。 如果上一页范围仅限于单个管理组或订阅，将自动填充“计划位置”。

1. 输入计划的“名称”和“说明”。 

   此示例验证资源是否符合有关保证安全的策略定义。 将计划命名为“保证安全”，并将说明设置为： **创建此计划的目的是处理所有与保护资源相关的策略定义**。

1. 对于“类别”，请从现有的选项中选择，或者创建新类别。

1. 为计划设置“版本”，如 1.0。

   > [!NOTE]
   > 版本值是元数据，Azure Policy 服务不会将其用于更新或任何进程。

1. 选择页面底部的“下一步”或向导顶部的“策略”选项卡 。

1. 选择“添加策略定义”按钮，然后浏览列表。 选择要添加到此计划中的策略定义。 对于“保证安全”计划，请选择策略定义旁边的复选框来添加以下内置策略定义：

   - 允许的位置
   - 监视 Azure 安全中心 Endpoint Protection 的缺失情况
   - 应使用网络安全组来保护非面向 Internet 的虚拟机
   - 应为虚拟机启用 Azure 备份
   - 应在虚拟机上启用磁盘加密
   - 在资源中添加或替换标记（添加此策略定义两次）

   从列表中选择每个策略定义后，选择列表底部的“添加”。
   由于添加了两次，因此每个“在资源中添加或替换标记”策略定义获得了不同的“引用 ID” 。

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="屏幕截图显示“计划定义”页上的所选策略定义及其引用 ID 和组。":::

   > [!NOTE]
   > 通过选择一个或多个已添加的定义，并选择“将所选策略添加到组”，可以将所选策略定义添加到组中。 该组必须先存在并且能够在向导的“组”选项卡上创建。

1. 选择页面底部的“下一步”或向导顶部的“组”选项卡 。 可以从此选项卡添加新组。对于本教程，我们不添加任何组。

1. 选择页面底部的“下一步”或向导顶部的“计划参数”选项卡 。 如果希望某个参数存在于计划中以便能够传递给一个或多个包含的策略定义，则在此处定义该参数，然后在“策略参数”选项卡上使用该参数。在本教程中，我们不添加任何计划参数。

   > [!NOTE]
   > 将计划参数保存到计划定义后，就不能从计划中删除了。 如果不再需要某个计划参数，请将其从任何策略定义参数中删除。

1. 选择页面底部的“下一步”或向导顶部的“策略参数”选项卡 。

1. 计划中添加的具有参数的策略定义会显示在网格中。 值类型可以是“默认值”、“设置值”或“使用计划参数”。 如果选择了“设置值”，则在“值”下输入相关值。 如果策略定义上的参数包含允许的值的列表，则输入框会是一个下拉列表选择器。 如果选择了“使用计划参数”，则会提供下拉的选择列表，其中包含在“计划参数”选项卡上创建的计划参数的名称。

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="屏幕截图显示“计划定义”页的“策略参数”选项卡上，“允许的位置”定义参数的允许的值的选项。":::

   > [!NOTE]
   > 在使用某些 `strongType` 参数时，不能自动确定值的列表。 在这种情况下，会在参数行的右侧显示省略号。 选择它会打开“参数范围(&lt;参数名称&gt;)”页。 在此页中，选择用于提供值选项的订阅。 此参数范围仅在创建计划定义过程中使用，对策略评估或分配后的计划范围没有影响。

   将“允许的位置”值类型设置为“设置值”，然后从下拉列表中选择“美国东部 2”。 对于“在资源中添加或替换标记”策略定义的两个实例，将“标记名称”参数设置为“Env”和“CostCenter”，将“标记值”参数设置为“Test”和“Lab”，如下所示 。 将其他值保留为“默认值”。 在计划中使用相同的定义两次，但使用不同的参数，此配置在分配范围内的资源中使用值“Test”添加或替换“Env”标记值，使用值“Lab”添加或替换“CostCenter”标记值。

   :::image type="content" source="../media/create-and-manage/initiative-definition-4.png" alt-text="屏幕截图显示“计划定义”页“策略参数”选项卡上“允许的位置”定义参数的输入的允许值选项及两个标记参数集。":::

1. 选择页面底部或向导顶部的“查看 + 创建”。

1. 检查设置，然后选择“创建”。

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>使用 Azure CLI 创建策略计划定义

可以将 Azure CLI 与 `az policy set-definition` 命令结合使用来创建策略计划定义。 若要使用现有的策略定义创建策略计划定义，请使用以下示例：

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>使用 Azure PowerShell 创建策略计划定义

可以将 Azure PowerShell 与 `New-AzPolicySetDefinition` cmdlet 结合使用来创建策略计划定义。 若要使用现有的策略定义创建策略计划定义，请使用 `VMPolicySet.json` 形式的以下策略计划定义文件：

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>分配计划定义

1. 选择“Azure Policy”页左侧“创作”下的“定义” 。

1. 找到前面创建的“保证安全”计划定义并选择它。 选择页面顶部的“分配”，打开“保证安全:  分配计划”页。

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="计划定义页面上的“分配”按钮的屏幕截图。" border="false":::

   也可选择和按住（或右键单击）选定的行，或者选择上下文菜单行末尾处的省略号。 然后选择“分配”。

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="用于选择“分配”功能的计划的上下文菜单的屏幕截图。" border="false":::

1. 输入以下示例信息，填充“保证安全: 分配计划”页。 可以使用自己的信息。

   - 范围：在其中保存计划的管理组或订阅变为默认。
     可以更改范围，以将计划分配到保存位置中的某个订阅或资源组。
   - 排除项：配置上述范围内的任何资源，以防止向其应用计划分配。
   - 计划定义和分配名称：“保证安全”（预先填充了所分配计划的名称）。
   - 说明:此计划分配旨在实施这组策略定义。
   - 策略强制：保留默认值“启用”。
   - 分配者：根据登录的用户自动填充。 此字段是可选字段，因此可输入自定义值。

1. 选择向导顶部的“参数”选项卡。 如果在前面的步骤中配置了计划参数，请在此处设置一个值。

1. 选择向导顶部的“修正”选项卡。 不选中“创建托管标识”。 当分配的策略或计划包含具有 [deployIfNotExists](../concepts/effects.md#deployifnotexists) 或 [modify](../concepts/effects.md#modify) 效果的策略时，必须勾选此框。 本教程所使用的策略不属于这种情况，因此请将其留空。 有关详细信息，请参阅[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)和[修正安全性工作原理](../how-to/remediate-resources.md#how-remediation-security-works)。

1. 选择向导顶部的“查看 + 创建”选项卡。

1. 查看选项，然后在页面底部选择“创建”。

## <a name="check-initial-compliance"></a>检查初始符合性

1. 选择“Azure Policy”页左侧的“符合性”。

1. 找到“保证安全”计划。 可能仍处于“未启动”符合性状态。
   选择计划，获取分配的完整详细信息。

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="“计划合规性”页显示处于“未启动”状态的分配评估的屏幕截图。" border="false":::

1. 完成计划分配后，符合性页会更新为“符合”符合性状态。

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="“计划合规性”页显示任务评估已完成且处于“合规”状态的屏幕截图。" border="false":::

1. 选择计划符合性页上的任何策略均可打开该策略的符合性详细信息页。 此页提供符合性的资源级别详细信息。

## <a name="remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion"></a>使用排除项从范围中删除不符合要求或拒绝的资源

分配一项要求使用特定位置的策略计划之后，系统会拒绝在其他位置创建的任何资源。 本部分介绍如何通过创建单个资源组中的排除项，来解决拒绝创建资源的请求的问题。 该排除项可防止对该资源组实施策略（或计划）。 在以下示例中，允许在排除的资源组中使用任何位置。 可对订阅、资源组或单个资源应用排除。

> [!NOTE]
> 还可以使用[策略豁免](../concepts/exemption-structure.md)来跳过对资源的评估。 有关详细信息，请参阅 [Azure Policy 中的作用域](../concepts/scope.md)。

可在部署所针对的资源组中查看被分配的策略或计划阻止的部署：选择页面左侧的“部署”，然后选择失败部署的“部署名称”。  随后将会列出带有“禁止”状态的被拒绝资源。 若要确定拒绝该资源的策略或计划和分配，请在“部署概述”页上选择“失败。单击此处了解详细信息 ->”。 页面右侧会打开一个窗口，其中显示了错误信息。 “错误详细信息”下显示了相关策略对象的 GUID。

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="策略分配拒绝的失败部署的屏幕截图。" border="false":::

在“Azure Policy”页上：选择页面左侧的“符合性”，然后选择“保证安全”策略计划。  在此页面上，被阻止的资源的“拒绝”计数会增加。 在“事件”选项卡下，详述了谁尝试创建或部署已按策略定义拒绝的资源。

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="“计划合规性”页上的“事件”选项卡和策略事件详细信息的屏幕截图。" border="false":::

在此示例中，Contoso 的资深虚拟化专家之一 Trent Baker 执行了所需的工作。 我们需要为 Trent 提供例外空间。 创建新资源组 **LocationsExcluded** 后，接下来将其指定为此策略分配的例外项。

### <a name="update-assignment-with-exclusion"></a>使用排除项更新分配

1. 在“Azure Policy”页左侧的“创作”下选择“分配” 。

1. 浏览所有策略分配并打开“保证安全”策略分配。

1. 设置“排除项”：选择省略号并选择要排除的资源组（在本示例中为 _LocationsExcluded_）。 选择“添加到所选范围”，然后选择“保存” 。

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="“计划分配”页上用于将排除的资源组添加到策略分配的“排除”选项的屏幕截图。" border="false":::

   > [!NOTE]
   > 根据策略定义及其效果，也可以将排除项指定为分配范围内某个资源组中的特定资源。 由于本教程使用了“拒绝”效果，对已存在的特定资源设置排除项没有意义。

1. 选择“查看 + 保存”，然后选择“保存” 。

本部分介绍如何通过创建单个资源组中的排除项，来解决请求被拒绝的问题。

## <a name="clean-up-resources"></a>清理资源

如果今后不再使用本教程中的资源，请使用以下步骤删除前面创建的所有策略分配或定义：

1. 在“Azure Policy”页左侧的“创作”下选择“定义”（如果尝试删除分配，则选择“分配”）  。

1. 搜索要删除的新计划或策略定义（或分配）。

1. 右键单击定义（或分配）对应的行或选择其末尾的省略号，然后选择“删除定义”（或“删除分配”）。 

## <a name="review"></a>审阅

在本教程中，你已成功完成以下任务：

> [!div class="checklist"]
> - 分配策略，对将来创建的资源强制执行条件
> - 创建并分配计划定义，跟踪多个资源的符合性
> - 解决不符合或遭拒绝的资源
> - 在组织中实施新策略

## <a name="next-steps"></a>后续步骤

若要了解有关策略定义结构的详细信息，请查看以下文章：

> [!div class="nextstepaction"]
> [Azure Policy 定义结构](../concepts/definition-structure.md)
