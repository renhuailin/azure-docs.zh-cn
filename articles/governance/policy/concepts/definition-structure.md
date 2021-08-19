---
title: 策略定义结构的详细信息
description: 介绍如何使用策略定义为组织中的 Azure 资源建立约定。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: b09d11e6f1c5ea8f4882021530dc4d06d2d2f350
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324883"
---
# <a name="azure-policy-definition-structure"></a>Azure Policy 定义结构

Azure Policy 可为资源建立多种约定。 策略定义描述资源符合性[条件](#conditions)，以及在符合某个条件时要采取的效果。 条件将资源属性[字段](#fields)或[值](#value)与所需值进行比较。 资源属性字段是通过[别名](#aliases)进行访问的。 如果资源属性字段为数组，则可使用特殊的[数组别名](#understanding-the--alias)从所有数组成员中选择值，并将条件应用于每个值。 如需了解更多，请参见[条件](#conditions)。

通过定义约定，可以控制成本并更轻松地管理资源。 例如，可指定仅允许特定类型的虚拟机。 也可要求资源使用特定的标记。 策略分配由子资源继承。 如果将策略分配应用到资源组，则会将其应用到该资源组中的所有资源。

策略定义“policyRule”架构可在此处找到：[https://schema.management.azure.com/schemas/2020-10-01/policyDefinition.json](https://schema.management.azure.com/schemas/2020-10-01/policyDefinition.json)

使用 JSON 创建策略定义。 策略定义包含以下项的元素：

- 显示名称
- description
- mode
- metadata
- parameters
- 策略规则
  - 逻辑评估
  - 效果

例如，以下 JSON 说明限制资源部署位置的策略：

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
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

Azure Policy 内置和模式位于 [Azure Policy 示例](../samples/index.md)。

## <a name="display-name-and-description"></a>显示名称和说明

请使用“displayName”和“description”来标识策略定义，并提供其使用上下文 。 **displayName** 的最大长度为 128 个字符，**description** 的最大长度为 512 个字符。

> [!NOTE]
> 在创建或更新策略定义期间，“ID”、“类型”和“名称”是由 JSON 外部属性定义，不需要在 JSON 文件中进行定义。 通过 SDK 获取策略定义将返回“ID”、“类型”和“名称”属性作为 JSON 的一部分，但每个属性都是与策略定义相关的只读信息。

## <a name="type"></a>类型

尽管无法设置 type 属性，但 SDK 返回了三个值且在门户中可见：

- `Builtin`：这些策略定义由 Microsoft 提供并维护。
- `Custom`：客户创建的所有策略定义都具有此值。
- `Static`：表示具有 Microsoft“所有权”的[法规合规性](./regulatory-compliance.md)策略定义。 这些策略定义的合规性结果是 Microsoft 基础结构上的第三方审核结果。 在 Azure 门户中，此值有时显示为“Microsoft 托管”。 有关详细信息，请参阅[云中责任分担](../../../security/fundamentals/shared-responsibility.md)。

## <a name="mode"></a>模式

“模式”的配置要取决于策略是针对 Azure 资源管理器属性还是资源提供程序属性。

### <a name="resource-manager-modes"></a>资源管理器模式

“模式”决定要为策略定义评估的资源类型。 支持的模式包括：

- `all`：评估资源组、订阅和所有资源类型
- `indexed`：仅评估支持标记和位置的资源类型

例如，资源 `Microsoft.Network/routeTables` 支持标记和位置，并在这两种模式中进行评估。 但是，无法标记资源 `Microsoft.Network/routeTables/routes`，也不会在 `Indexed` 模式下对其进行评估。

大多数情况下，建议将“mode”设置为 `all`。 通过门户创建的所有策略定义使用 `all` 模式。 如果使用 PowerShell 或 Azure CLI，则可以手动指定 **mode** 参数。 如果策略定义不包含 **mode** 值，为提供后向兼容性，在 Azure PowerShell 中默认为 `all`，在 Azure CLI 中默认为 `null`。 `null` 模式等同于使用 `indexed` 来支持后向兼容性。

在创建强制执行标记或位置的策略时，应该使用 `indexed`。 虽然并不是必需的，但是它会阻止不支持标记和位置的资源，使其不会在符合性结果中显示为不兼容。 有一个例外情况，就是资源组和订阅。 策略定义若在资源组或订阅上强制执行位置或标记，则应将“模式”设为 `all`，并明确以 `Microsoft.Resources/subscriptions/resourceGroups` 或 `Microsoft.Resources/subscriptions` 类型为目标。 有关示例，请参阅[模式：标记 - 示例 #1](../samples/pattern-tags.md)。 有关支持标记的资源列表，请参阅[有关 Azure 资源的标记支持](../../../azure-resource-manager/management/tag-support.md)。

### <a name="resource-provider-modes"></a>资源提供程序模式

充分支持以下资源提供程序模式：

- `Microsoft.Kubernetes.Data`，用于在 Azure 上或外部管理 Kubernetes 群集。 使用该资源提供程序模式的定义使用效果“审核”、“拒绝”和“已禁用”  。 不推荐使用 [EnforceOPAConstraint](./effects.md#enforceopaconstraint) 效果。

目前支持以下资源提供程序模式（预览版）：

- `Microsoft.ContainerService.Data`，用于管理 [Azure Kubernetes 服务](../../../aks/intro-kubernetes.md)上的许可控制器规则。 使用此资源提供程序模式的定义必须使用 [EnforceRegoPolicy](./effects.md#enforceregopolicy) 效果。 此模式已弃用。
- `Microsoft.KeyVault.Data`，用于管理 [Azure Key Vault](../../../key-vault/general/overview.md) 中的保管库和证书。 有关这些策略定义的详细信息，请参阅[将 Azure Key Vault 与 Azure Policy 集成](../../../key-vault/general/azure-policy.md)。

> [!NOTE]
> 资源提供程序模式仅支持内置策略定义，不支持[豁免](./exemption-structure.md)。

## <a name="metadata"></a>Metadata

`metadata` 可选属性用于存储关于策略定义的信息。 客户可在 `metadata` 中定义对其组织有用的任何属性和值。 但是，Azure Policy 和内置项使用一些常见属性。每个 `metadata` 属性的限制为 1024 个字符。

### <a name="common-metadata-properties"></a>常见元数据属性

- `version`（字符串）：跟踪有关策略定义的内容版本的详细信息。
- `category`（字符串）：确定在 Azure 门户中的哪个类别下显示策略定义。
- `preview`（布尔值）：如果策略定义为“预览”，则为 True 或 false 标志。
- `deprecated`（布尔值）：如果策略定义被标记为“已弃用”，则为 True 或 false 标志。

> [!NOTE]
> Azure Policy 服务会使用 `version`、`preview` 和 `deprecated` 属性，将变更级别传达给内置策略定义或计划和状态。 `version` 的格式为：`{Major}.{Minor}.{Patch}`。 特定状态（例如“弃用”或“预览版”）会附加至 `version` 属性，或另一个属性中附加为“布尔值”。 有关 Azure Policy 内置项版本控制方式的详细信息，请参阅[内置项版本控制](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)。

## <a name="parameters"></a>parameters

参数可减少策略定义的数量，有助于简化策略管理。 可以将参数想象成窗体上的字段 - `name`、`address`、`city`、`state`。 这些参数始终不变，但其值会基于窗体中的各填写内容变化。
构建策略时，参数同样适用。 如果在策略定义中包括参数，就可以通过使用不同的值重新使用策略以执行不同方案。

> [!NOTE]
> 参数可以添加到现有和已分配的定义。 新参数必须包含 defaultValue 属性。 这可以防止策略或计划的现有分配间接被设为无效。

### <a name="parameter-properties"></a>参数属性

参数有下述可以在策略定义中使用的属性：

- `name`：参数的名称。 由策略规则中的 `parameters` 部署函数使用。 有关详细信息，请参阅[使用参数值](#using-a-parameter-value)。
- `type`：确定参数是“字符串”、“数组”、“对象”、“布尔值”、“整数”、“浮点数”，还是“日期/时间”。
- `metadata`：定义主要由 Azure 门户用来显示用户友好信息的子属性：
  - `description`：说明参数的用途。 可以用来提供可接受值的示例。
  - `displayName`：在门户中显示的用于参数的友好名称。
  - `strongType`：（可选）通过门户分配策略定义时使用。 提供上下文感知列表。 有关详细信息，请参阅 [strongType](#strongtype)。
  - `assignPermissions`：（可选）设置为“true”，使 Azure 门户在策略分配过程中创建角色分配。 如果希望在分配范围之外分配权限，此属性会很有用。 策略中的每个角色定义（或计划中所有策略中的每个角色定义）都有一个角色分配。 参数值必须是有效的资源或范围。
- `defaultValue`：（可选）设置分配的参数的值（如果值未给定）。
  在更新已分配的现有策略定义时必须使用此项。
- `allowedValues`：（可选）提供参数在分配过程中所接受值的数组。 允许值在比较时区分大小写。

例如，可以定义策略定义来限制资源的部署位置。 **allowedLocations** 可以是该策略定义的一个参数。 每次分配策略定义来限制接受的值时，会使用此参数。 使用 **strongType** 可以在通过门户完成分配时提供增强的体验：

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>使用参数值

在策略规则中，你可以使用下列 `parameters` 函数语法引用参数：

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

此示例引用 **allowedLocations** 参数，该参数已在 [参数属性](#parameter-properties)中演示过。

### <a name="strongtype"></a>strongType

在 `metadata` 属性中，可以使用 strongType 提供 Azure 门户中的选项多选列表。 strongType 可以是受支持的资源类型，也可以是允许值。 若要确定某个资源类型是否对 strongType 有效，请使用 [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)。 资源类型“StrongType”的格式为 `<Resource Provider>/<Resource Type>`。 例如 `Microsoft.Network/virtualNetworks/subnets`。

支持部分不是由 **Get-AzResourceProvider** 返回的资源类型。 这些类型包括：

- `Microsoft.RecoveryServices/vaults/backupPolicies`

strongType 的非资源类型允许值包括：

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>定义位置

创建计划或策略时，需要指定定义位置。 定义位置必须是管理组或订阅。 此位置决定了计划或策略的分配范围。 资源必须是用于分配的目标定义位置的层次结构中的直系成员或子代。

如果定义位置是：

- **订阅** - 策略定义只能分配到该订阅内的资源。
- **管理组** - 策略定义只能分配到子管理组和子订阅内的资源。 如果你计划将此策略定义应用于多个订阅，该位置必须是包含每个订阅的管理组。

有关详细信息，请参阅[了解 Azure Policy 中的范围](./scope.md#definition-location)。

## <a name="policy-rule"></a>策略规则

策略规则包括 **If** 和 **Then** 块。 在 **If** 块中，定义强制执行策略时指定的一个或多个条件。 可以对这些条件应用逻辑运算符，以精确定义策略的方案。

在 **Then** 块中，定义满足 **If** 条件时产生的效果。

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>逻辑运算符

支持的逻辑运算符为：

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**not** 语法反转条件的结果。 **allOf** 语法（与逻辑 **And** 操作相似）要求所有条件为 true。 **anyOf** 语法（与逻辑 **Or** 操作相似）要求一个或多个条件为 true。

可以嵌套逻辑运算符。 以下示例显示了嵌套在 allOf 操作中的 not 操作。

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>条件

条件用于评估某个值是否符合特定的标准。 支持的条件有：

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

对于“less”、“lessOrEquals”、“greater”和“greaterOrEquals”，如果属性类型与条件类型不匹配，则会引发错误。 字符串比较是使用 `InvariantCultureIgnoreCase` 进行。

使用 like 和 notLike 条件时，请在值中指定通配符 `*`。 值不应包含多个通配符 `*`。

当使用 match 和 notMatch 条件时，请提供 `#` 来匹配数字、`?` 来匹配字母、`.` 来匹配所有字符，以及提供任何其他字符来匹配该实际字符。 尽管 match 和 notMatch 区分大小写，但用于评估 stringValue 的所有其他条件都不区分大小写 。 “matchInsensitively”和“notMatchInsensitively”中提供了不区分大小写的替代方案 。

### <a name="fields"></a>字段

可以使用“field”表达式来构建条件，用于评估资源请求有效负载中的属性值是否符合特定的标准。 支持以下字段：

- `name`
- `fullName`
  - 返回资源全名。 资源全名是最前面为任意父资源名称的资源名称（例如“myServer/myDatabase”）。
- `kind`
- `type`
- `location`
  - 位置字段已规范化，支持各种格式。 例如，`East US 2` 被视为等于 `eastus2`。
  - 对于不限位置的资源，请使用 **global**。
- `id`
  - 返回所评估的资源的资源 ID。
  - 示例：`/subscriptions/06be863d-0996-4d56-be22-384767287aa2/resourceGroups/myRG/providers/Microsoft.KeyVault/vaults/myVault`
- `identity.type`
  - 返回在资源上启用的[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)类型。
- `tags`
- `tags['<tagName>']`
  - 此括号语法支持具有标点符号的标记名称，例如连字符、句点或空格。
  - 其中的 \<tagName\> 是要验证其条件的标记的名称。
  - 示例：`tags['Acct.CostCenter']`，其中 Acct.CostCenter 是标记的名称。
- `tags['''<tagName>''']`
  - 此括号语法通过双撇号进行转义，可支持在其中包含撇号的标记名称。
  - 其中的“\<tagName\>”是要验证其条件的标记的名称。
  - 示例：`tags['''My.Apostrophe.Tag''']`，其中 'My.Apostrophe.Tag' 是标记的名称。
- 属性别名 - 有关列表，请参阅[别名](#aliases)。

> [!NOTE]
> `tags.<tagName>``tags[tagName]` 和 `tags[tag.with.dots]` 仍然是可接受的用于声明标记字段的方式。 但是，首选表达式是上面列出的那些。

> [!NOTE]
> 在引用 \[\*\] 别名的“field”表达式中，将在元素之间使用逻辑“与”对数组中的每个元素分别进行评估。 有关详细信息，请参阅[引用数组资源属性](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)。

#### <a name="use-tags-with-parameters"></a>使用带参数的标记

参数值可以传递给标记字段。 将参数传递给标记字段可在策略分配期间提高策略定义的灵活性。

在以下示例中，`concat` 用于为名为 tagName 参数值的标记创建标记字段查找。 如果该标记不存在，系统会使用“修改”效果，通过 `resourcegroup()` 查找函数使用在已审计资源父资源组上具有相同名称的标签值来添加该标记。

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f"
            ]
        }
    }
}
```

### <a name="value"></a>值

用于评估某个值是否符合特定标准的条件可以使用“value”表达式来构建。 值可以是文本、[参数](#parameters)的值，也可以是任何[受支持的模板函数](#policy-functions)的返回值。

> [!WARNING]
> 如果模板函数的结果是一个错误，则策略评估会失败。 评估失败是一种隐式 **拒绝**。 有关详细信息，请参阅[避免模板失败](#avoiding-template-failures)。 使用 DoNotEnforce 的 [enforcementMode](./assignment-structure.md#enforcement-mode)，以防止在测试和验证新策略定义期间，由于新的或更新的资源评估失败而受到影响。

#### <a name="value-examples"></a>Value 示例

此策略规则示例使用 **value** 将 `resourceGroup()` 函数和返回的 **name** 属性的结果与 **like** 条件 `*netrg` 进行对比。 此规则会拒绝名称以 `*netrg` 结尾的任何资源组中类型不为 `Microsoft.Network/*` 的任何资源。

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

此策略规则示例使用 value 来检查多个嵌套函数的结果是否等于 `true`。 此规则拒绝并没有至少三个标记的资源。

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>避免模板失败

在 value 中使用模板函数允许使用许多复杂嵌套函数。 如果模板函数的结果是一个错误，则策略评估会失败。 评估失败是一种隐式 **拒绝**。 在某些情况下失败的 value 示例：

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

上面的示例策略规则使用 [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) 将名称的前三个字符与 abc 进行比较。 如果名称少于三个字符，`substring()` 函数会导致错误。 此错误会导致策略变为“拒绝”效果。

相反地，请改用 [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) 函数来检查名称的前三个字符是否等于 abc，不允许名称少于三个字符，这会导致错误：

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

使用修订后的策略规则，`if()` 会先检查名称的长度，然后尝试在少于三个字符的值上获取 `substring()`。 如果名称太短，则会改为返回“不是以 abc 开头”的值，并将其与 abc 进行比较。 短名称不是以 abc 开头的资源仍会导致策略规则失败，但在评估过程中不会再造成错误。

### <a name="count"></a>计数

用于统计数组中有多少成员符合特定标准的条件可以使用“count”表达式来构建。 常见方案是检查是“至少有一个”数组成员、“正好有一个”数组成员、“所有”数组成员还是“没有”数组成员满足条件。 “count”会根据条件表达式评估每个数组成员，并将 true 结果求和，然后将其与表达式运算符进行比较。

#### <a name="field-count"></a>字段计数

统计请求有效负载中的数组有多少成员符合条件表达式。 “字段计数”表达式的结构为：

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

以下属性与“字段计数”结合使用：

- count.field（必需）：包含数组路径，且必须为数组别名。
- **count.where**（可选）：用于分别评估 `count.field` 的每个 [\[\*\] 别名](#understanding-the--alias)数组成员的条件表达式。 如果未提供此属性，具有“字段”路径的所有数组成员将评估为 true。 任何[条件](../concepts/definition-structure.md#conditions)都可在此属性内使用。
  可在此属性中使用[逻辑运算符](#logical-operators)来创建复杂的评估要求。
- **\<condition\>** （必需）：该值将与满足 **count.where** 条件表达式的项数进行比较。 应使用数字[条件](../concepts/definition-structure.md#conditions)。

在单个 policyRule 定义中，“字段计数”表达式最多可以将同一字段数组枚举三次。

有关如何在 Azure Policy 中使用数组属性的更多详细信息，包括有关“字段计数”表达式计算方式的详细说明，请参阅[引用数组资源属性](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)。

#### <a name="value-count"></a>值计数

统计数组中有多少成员符合条件。 数组可以是文本数组，也可以是[对数组参数的引用](#using-a-parameter-value)。 “值计数”表达式的结构为：

```json
{
    "count": {
        "value": "<literal array | array parameter reference>",
        "name": "<index name>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

以下属性与“值计数”结合使用：

- count.value（必需）：要评估的数组。
- count.name（必需）：索引名称，由英文字母和数字构成。 定义在当前迭代中评估的数组成员的值的名称。 该名称用于引用 `count.where` 条件内的当前值。 当 count 表达式不在另一个 count 表达式的子级中时，此项为可选 。 如果未提供此项，则索引名称将隐式设置为 `"default"`。
- **count.where**（可选）：用于针对 `count.value` 的每个数组成员分别进行评估的条件表达式。 如果未提供此属性，则所有数组成员都将评估为 true。 任何[条件](../concepts/definition-structure.md#conditions)都可在此属性内使用。 可在此属性中使用[逻辑运算符](#logical-operators)来创建复杂的评估要求。 可以通过调用 [current](#the-current-function) 函数来访问当前枚举的数组成员的值。
- **\<condition\>** （必需）：该值将与满足 `count.where` 条件表达式的项数进行比较。 应使用数字[条件](../concepts/definition-structure.md#conditions)。

将强制实施以下限制：
- 在单个 policyRule 定义中最多可以使用 10 个“值计数”表达式。
- 每个“值计数”表达式最多可以执行 100 次迭代。 此数目包括由任何父级“值计数”表达式执行的迭代次数。

#### <a name="the-current-function"></a>current 函数

`current()` 函数仅在 `count.where` 条件内可用。 它返回当前通过 count 表达式评估枚举的数组成员的值。

值计数用法

- `current(<index name defined in count.name>)`. 例如：`current('arrayMember')`。
- `current()`. 只有当 value count 表达式不是另一个 count 表达式的子级时，才允许使用此函数 。 返回与上面的值相同的值。

如果调用返回的值是一个对象，则支持属性访问器。 例如：`current('objectArrayMember').property`。

字段计数用法

- `current(<the array alias defined in count.field>)`. 例如，`current('Microsoft.Test/resource/enumeratedArray[*]')`。
- `current()`. 只有当 field count 表达式不是另一个 count 表达式的子级时，才允许使用此函数 。 返回与上面的值相同的值。
- `current(<alias of a property of the array member>)`. 例如，`current('Microsoft.Test/resource/enumeratedArray[*].property')`。

#### <a name="field-count-examples"></a>字段计数示例

示例 1：检查数组是否为空

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

示例 2：检查是否只有一个数组成员符合条件表达式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

示例 3：检查是否至少有一个数组成员符合条件表达式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

示例 4：检查是否所有对象数组成员都符合条件表达式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

示例 5：检查是否至少有一个数组成员与条件表达式中的多个属性匹配

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

示例 6：在 `where` 条件中使用 `current()` 函数来访问模板函数中当前枚举的数组成员的值。 此条件检查虚拟网络是否包含不在 10.0.0.0/24 CIDR 范围内的某个地址前缀。

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
          "equals": false
        }
    },
    "greater": 0
}
```

示例 7：在 `where` 条件中使用 `field()` 函数来访问当前枚举的数组成员的值。 此条件检查虚拟网络是否包含不在 10.0.0.0/24 CIDR 范围内的某个地址前缀。

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
          "value": "[ipRangeContains('10.0.0.0/24', first(field(('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]')))]",
          "equals": false
        }
    },
    "greater": 0
}
```

#### <a name="value-count-examples"></a>值计数示例

示例 1：检查资源名称是否与任何给定的名称模式匹配。

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

示例 2：检查资源名称是否与任何给定的名称模式匹配。 `current()` 函数未指定索引名称。 结果与前面的示例相同。

```json
{
    "count": {
        "value": [ "prefix1_*", "prefix2_*" ],
        "where": {
            "field": "name",
            "like": "[current()]"
        }
    },
    "greater": 0
}
```

示例 3：检查资源名称是否与数组参数提供的任何给定的名称模式匹配。

```json
{
    "count": {
        "value": "[parameters('namePatterns')]",
        "name": "pattern",
        "where": {
            "field": "name",
            "like": "[current('pattern')]"
        }
    },
    "greater": 0
}
```

示例 4：检查是否有任何虚拟网络地址前缀不在批准的前缀的列表下。

```json
{
    "count": {
        "field": "Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]",
        "where": {
            "count": {
                "value": "[parameters('approvedPrefixes')]",
                "name": "approvedPrefix",
                "where": {
                    "value": "[ipRangeContains(current('approvedPrefix'), current('Microsoft.Network/virtualNetworks/addressSpace.addressPrefixes[*]'))]",
                    "equals": true
                },
            },
            "equals": 0
        }
    },
    "greater": 0
}
```

示例 5：检查是否在 NSG 中定义了所有保留的 NSG 规则。 保留的 NSG 规则的属性是在一个包含对象的数组参数中定义的。

参数值：

```json
[
    {
        "priority": 101,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 22
    },
    {
        "priority": 102,
        "access": "deny",
        "direction": "inbound",
        "destinationPortRange": 3389
    }
]
```

策略:

```json
{
    "count": {
        "value": "[parameters('reservedNsgRules')]",
        "name": "reservedNsgRule",
        "where": {
            "count": {
                "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
                "where": {
                    "allOf": [
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].priority",
                            "equals": "[current('reservedNsgRule').priority]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                            "equals": "[current('reservedNsgRule').access]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                            "equals": "[current('reservedNsgRule').direction]"
                        },
                        {
                            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                            "equals": "[current('reservedNsgRule').destinationPortRange]"
                        }
                    ]
                }
            },
            "equals": 1
        }
    },
    "equals": "[length(parameters('reservedNsgRules'))]"
}
```

### <a name="effect"></a>效果

Azure Policy 支持以下类型的效果：

- Append：会将定义的字段集添加到请求
- Audit：会在活动日志中生成一个警告事件，但不会使请求失败
- AuditIfNotExists：如果相关资源不存在，则会在活动日志中生成一个警告事件
- Deny：会在活动日志中生成一个事件，并使请求失败
- DeployIfNotExists：如果相关资源不存在，则部署该资源
- **Disabled**：不评估资源是否符合策略规则
- **Modify**：添加、更新或删除资源或订阅中已定义的标记
- **EnforceOPAConstraint**（已弃用）：针对 Azure 上的自托管 Kubernetes 群集，配置 Open Policy Agent 许可控制器与 Gatekeeper v3
- **EnforceRegoPolicy**（已弃用）：在 Azure Kubernetes 服务中，配置 Open Policy Agent 许可控制器与 Gatekeeper v2

有关每种效果、评估顺序、属性和示例的完整详细信息，请参阅[了解 Azure Policy 效果](effects.md)。

### <a name="policy-functions"></a>策略函数

除了以下函数和用户定义的函数外，所有[资源管理器模板函数](../../../azure-resource-manager/templates/template-functions.md)均可在策略规则中使用：

- copyIndex()
- deployment()
- list*
- newGuid()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> 在 deployIfNotExists 策略定义中，仍可在其模板部署的 `details.deployment.properties.template` 部分中使用这些函数。

以下函数可在策略规则中使用，但与在 Azure 资源管理器模板（ARM 模板）中使用不同：

- `utcNow()` - 与 ARM 模板不同，该属性可以在 defaultValue 之外使用。
  - 以通用 ISO 8601 日期/时间格式“`yyyy-MM-ddTHH:mm:ss.fffffffZ`”返回设置为当前日期和时间的字符串。

以下函数仅适用于策略规则：

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**：[必需] 字符串 - 采用通用 ISO 8601 日期/时间格式“yyyy-MM-ddTHH:mm:ss.FFFFFFFZ”的字符串
  - numberOfDaysToAdd：[必需] 整数 - 要增加的天数

- `field(fieldName)`
  - fieldName：[必需] 字符串 - 要检索的[字段](#fields)名称
  - 从 If 条件正在评估的资源返回该字段的值。
  - `field` 主要用于 **AuditIfNotExists** 和 **DeployIfNotExists**，以引用所评估资源上的字段。 可以在 [DeployIfNotExists 示例](effects.md#deployifnotexists-example)中看到这种用法的示例。

- `requestContext().apiVersion`
  - 返回已触发策略评估的请求的 API 版本（示例：`2021-09-01`）。
    该值是 PUT/PATCH 请求中用于对资源创建/更新进行评估的 API 版本。 在对现有资源进行符合性评估时，将会一律使用最新的 API 版本。

- `policy()`
  - 返回有关正在评估的策略的下列信息。 可以从返回的对象访问属性，例如：`[policy().assignmentId]`。

    ```json
    {
      "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
      "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
      "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
      "definitionReferenceId": "StorageAccountNetworkACLs"
    }
    ```

- `ipRangeContains(range, targetRange)`
  - range：[必需]字符串 - 指定 IP 地址范围的字符串，用于检查 targetRange 是否在其中。
  - targetRange：[必需]字符串 - 此字符串指定一个 IP 地址范围，需要验证它是否包含在 range 内。
  - 返回一个布尔值，指示 range IP 地址范围是否包含 targetRange IP 地址范围。 空范围或 IP 系列之间的混合是不允许的，这会导致评估失败。

  支持的格式：
  - 单个 IP 地址（示例：`10.0.0.0`、`2001:0DB8::3:FFFE`）
  - CIDR 范围（示例：`10.0.0.0/24`、`2001:0DB8::/110`）
  - 由起始 IP 地址和结束 IP 地址定义的范围（示例：`192.168.0.1-192.168.0.9`、`2001:0DB8::-2001:0DB8::3:FFFF`）

- `current(indexName)`
  - 只能在 [count 表达式](#count)内使用的特殊函数。

#### <a name="policy-function-example"></a>策略函数示例

此策略规则示例使用 `resourceGroup` 资源函数获取 **name** 属性，并将该属性与 `concat` 数组和对象函数结合使用以构建 `like` 条件，该条件强制资源名称以资源组名称开头。

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>别名

可以使用属性别名来访问资源类型的特定属性。 通过别名，可限制允许用于资源属性的值和条件。 每个别名会映射到给定资源类型不同 API 版本的路径。 在策略评估期间，策略引擎会获取该 API 版本的属性路径。

别名列表始终不断增长。 若要找出 Azure Policy 当前支持哪些别名，请使用以下方法之一：

- 适用于 Visual Studio Code 的 Azure Policy 扩展（推荐）

  使用[适用于 Visual Studio Code 的 Azure Policy 扩展](../how-to/extension-for-vscode.md)来查看和发现资源属性的别名。

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Visual Studio Code 的 Azure Policy 扩展的屏幕截图，鼠标悬停在属性上以显示别名。" border="false":::

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

  > [!NOTE]
  > 若要查找可用于[修改](./effects.md#modify)效果的别名，请在 Azure PowerShell 4.6.0 或更高版本中使用以下命令：
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>了解 [*] 别名

很多可用的别名都会有一个显示为“正常”名称的版本，以及一个附加有 \[\*\] 的版本。 例如：

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

“正常”别名会将该字段表示为单个值。 当整个值集必须与定义完全一致，不能多也不能少时，此字段适用于精确匹配的比较场景。

\[\*\] 别名表示从数组资源属性的元素中所选的值的集合。 例如：

| Alias | 选定值 |
|:---|:---|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `ipRules` 数组的元素。 |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `ipRules` 数组的每个元素中的 `action` 属性的值。 |

在[字段](#fields)条件中使用数组别名时，可以将每个单独的数组元素与目标值进行比较。 与 [count](#count) 表达式结合使用时，可以执行以下操作：

- 检查数组的大小
- 检查是否所有或任意数组元素均满足复杂条件，或者是否没有数组元素满足复杂条件
- 检查是否恰好有 n 个数组元素满足复杂条件

有关详细信息和示例，请参阅[引用数组资源属性](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties)。

## <a name="next-steps"></a>后续步骤

- 请参阅[计划定义结构](./initiative-definition-structure.md)
- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看[了解策略效果](effects.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
