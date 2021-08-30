---
title: 测试工具包的模板测试用例
description: 介绍由 Azure 资源管理器模板测试工具包运行的模板测试。
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 961a62ae45b423e2e4c2abf8a4e7d771c2775591
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744900"
---
# <a name="test-cases-for-arm-templates"></a>ARM 模板的测试用例

本文介绍 Azure 资源管理器模板（ARM 模板）的[模板测试工具包](test-toolkit.md)运行的测试。 它提供通过或未通过测试的示例，并包含每个测试的名称 。 有关如何运行测试或如何运行特定测试的详细信息，请参阅[测试参数](test-toolkit.md#test-parameters)。

## <a name="use-correct-schema"></a>使用正确的架构

测试名称：DeploymentTemplate 架构正确

在模板中，必须指定有效的架构值。

下面的示例未通过，因为架构无效。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-01-01/deploymentTemplate.json#",
}
```

下面的示例显示一个警告，因为架构版本 `2015-01-01` 已弃用且不维护。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
}
```

下面的示例通过，因为其使用有效的架构。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
}
```

模板的 `schema` 属性必须设置为以下架构之一：

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
- `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="declared-parameters-must-be-used"></a>必须使用声明的参数

测试名称：必须引用参数

此测试查找模板中未使用的参数或有效表达式中未使用的参数。

为了尽量避免混淆，请删除模板中已定义但未使用的任何参数。 消除未使用的参数可简化模板部署，因为无需提供不必要的值。

下面的示例未通过，因为引用参数的表达式缺少前导方括号 (`[`) 。

```json
"resources": [
  {
    "location": " parameters('location')]"
  }
]
```

下面的示例通过，因为表达式有效。

```json
"resources": [
  {
    "location": "[parameters('location')]"
  }
]
```

## <a name="secure-parameters-cant-have-hard-coded-default"></a>安全参数不能包含硬编码的默认值

测试名称：安全字符串参数不能有默认值

不要为模板中的安全参数提供硬编码的默认值。 安全参数可以将空字符串作为默认值，也可以在表达式中使用 [newGuid](template-functions-string.md#newguid) 函数。

在包含敏感值（如密码）的参数上使用类型 `secureString` 或 `secureObject`。 当参数使用安全类型时，不会将参数的值记录或存储在部署历史记录中。 此操作可防止恶意用户发现敏感值。

当你提供默认值时，可访问该模板或部署历史记录的任何人都可以发现该值。

下面的示例未通过。

```json
"parameters": {
  "adminPassword": {
    "defaultValue": "HardcodedPassword",
    "type": "secureString"
  }
}
```

下一示例通过。

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
```

下面的示例通过，因为使用了 `newGuid` 函数。

```json
"parameters": {
  "secureParameter": {
    "type": "secureString",
    "defaultValue": "[newGuid()]"
  }
}
```

## <a name="environment-urls-cant-be-hard-coded"></a>环境 URL 不能硬编码

测试名称：**DeploymentTemplate 不得包含硬编码 Uri**

不要在模板中对环境 URL 进行硬编码。 请改用[环境](template-functions-deployment.md#environment)函数在部署期间动态获取这些 URL。 有关被阻止的 URL 主机的列表，请参阅[测试用例](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)。

下面的示例未通过，因为 URL 已进行硬编码。

```json
"variables":{
  "AzureURL":"https://management.azure.com"
}
```

与 [concat](template-functions-string.md#concat) 或 [uri](template-functions-string.md#uri) 一起使用时，此测试也会失败。

```json
"variables":{
  "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
  "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

以下示例通过了测试。

```json
"variables": {
  "AzureSchemaURL": "[environment().gallery]"
}
```

## <a name="location-uses-parameter"></a>位置使用参数

测试名称：不应硬编码位置

若要设置资源的位置，模板应具有一个名为 `location` 的参数，其类型设置为 `string`。 在主模板（azuredeploy.json 或 mainTemplate.json）中，此参数可默认为资源组位置 。 在链接模板或嵌套模板中，位置参数不应具有默认位置。

模板用户对其可以创建资源的区域的访问权限可能有限。 硬编码资源位置可能会阻止用户创建资源。 如果资源组是在用户无法访问的区域中创建的，则表达式 `"[resourceGroup().location]"` 可能会阻止用户。 被阻止的用户无法使用该模板。

提供默认为资源组位置的 `location` 参数后，用户可以在方便的情况下使用默认值，也可以指定其他位置。

下面的示例未通过，因为资源的 `location` 设置为 `resourceGroup().location`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

下一个示例使用 `location` 参数，但未通过，因为参数默认为硬编码的位置。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "westus"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

当该模板用作主模板时，下面的示例通过。 创建一个默认为资源组位置但允许用户提供不同值的参数。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

> [!NOTE]
> 如果前面的示例用作链接模板，测试将不通过。 用作链接模板时，删除默认值。

## <a name="resources-should-have-location"></a>资源应具有位置

测试名称：资源应具有位置

应将资源的位置设置为[模板表达式](template-expressions.md)或 `global`。 模板表达式通常使用[位置使用参数](#location-uses-parameter)中所述的 `location` 参数。

下面的示例未通过，因为 `location` 不是表达式或 `global`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "westus",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

下面的示例通过，因为资源 `location` 设置为 `global`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "global",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}

```

下一个示例也会通过，因为 `location` 参数使用表达式。 资源 `location` 使用表达式的值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-02-01",
      "name": "storageaccount1",
      "location": "[parameters('location')]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>VM 大小使用参数

测试名称：VM 大小应为参数

不要对 `hardwareProfile` 对象的 `vmSize` 进行硬编码。 如果 `hardwareProfile` 省略或包含硬编码的值，则测试不通过。 提供一个参数，使模板的用户可以修改部署的虚拟机的大小。 有关详细信息，请参阅 [Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines)。

下面的示例未通过，因为 `hardwareProfile` 对象的 `vmSize` 是硬编码的值。

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_D2_v3"
      }
    }
  }
]
```

当参数为 `vmSize` 指定值时，示例通过：

```json
"parameters": {
  "vmSizeParameter": {
    "type": "string",
    "defaultValue": "Standard_D2_v3",
    "metadata": {
      "description": "Size for the virtual machine."
    }
  }
}
```

然后，`hardwareProfile` 使用 `vmSize` 的表达式来引用参数的值：

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    "name": "demoVM",
    "location": "[parameters('location')]",
    "properties": {
      "hardwareProfile": {
        "vmSize": "[parameters('vmSizeParameter')]"
      }
    }
  }
]
```

## <a name="min-and-max-values-are-numbers"></a>最小值和最大值为数字

测试名称：最小值和最大值为数字

使用 `minValue` 和 `maxValue` 定义参数时，请将它们指定为数字。 必须成对使用 `minValue` 和 `maxValue`，否则测试将不通过。

下面的示例未通过，因为 `minValue` 和 `maxValue` 是字符串。

```json
"exampleParameter": {
  "type": "int",
  "minValue": "0",
  "maxValue": "10"
}
```

下面的示例未通过，因为只使用了 `minValue`。

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0
}
```

下面的示例通过，因为 `minValue` 和 `maxValue` 是数字。

```json
"exampleParameter": {
  "type": "int",
  "minValue": 0,
  "maxValue": 10
}
```

## <a name="artifacts-parameter-defined-correctly"></a>项目参数定义正确

测试名称：项目参数

当包含 `_artifactsLocation` 和 `_artifactsLocationSasToken` 的参数时，请使用正确的默认值和类型。 若要通过此测试，必须满足以下条件：

- 如果提供一个参数，则必须提供另一个参数。
- `_artifactsLocation` 必须是 `string`。
- `_artifactsLocation` 在主模板中必须具有默认值。
- `_artifactsLocation` 在嵌套模板中不能有默认值。
- `_artifactsLocation` 必须使用 `"[deployment().properties.templateLink.uri]"` 或原始存储库 URL 作为其默认值。
- `_artifactsLocationSasToken` 必须是 `secureString`。
- `_artifactsLocationSasToken` 的默认值只能有一个空字符串。
- `_artifactsLocationSasToken` 在嵌套模板中不能有默认值。

## <a name="declared-variables-must-be-used"></a>必须使用已声明的变量

测试名称：必须引用变量

此测试查找模板中未使用的变量，或者有效表达式中未使用的变量。 为了尽量避免混淆，请删除已定义但未使用的任何变量。

必须引用使用 `copy` 元素来迭代值的变量。 有关详细信息，请参阅 [ARM 模板中的变量迭代](copy-variables.md)。

以下示例失败的原因是未引用使用 `copy` 元素的变量。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {}
}
```

下面的示例未通过，因为引用变量的表达式缺少前导方括号 (`[`)。

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": " variables('varExample')]"
  }
}
```

以下示例通过的原因是在 `outputs` 中引用了变量。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('stringArray')]"
    }
  }
}
```

下面的示例通过，因为表达式有效。

```json
"outputs": {
  "outputVariable": {
    "type": "string",
    "value": "[variables('varExample')]"
  }
}
```

## <a name="dynamic-variable-should-not-use-concat"></a>动态变量不应使用 concat

测试名称：动态变量引用不应使用 Concat

有时，你需要基于另一个变量或参数的值来动态构造变量。 设置值时，不要使用 [concat](template-functions-string.md#concat) 函数。 改用包含可用选项的对象，并在部署过程中动态获取对象中的一个属性。

以下示例通过了测试。 在部署过程中会动态设置 `currentImage` 变量。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "osType": {
      "type": "string",
      "allowedValues": [
        "Windows",
        "Linux"
      ]
    }
  },
  "variables": {
    "imageOS": {
      "Windows": {
        "image": "Windows Image"
      },
      "Linux": {
        "image": "Linux Image"
      }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[variables('currentImage')]"
    }
  }
}
```

## <a name="use-recent-api-version"></a>使用最新的 API 版本

测试名称：apiVersions 应为最新版本

每个资源的 API 版本应使用硬编码为字符串的最新版本。 该测试根据工具包缓存中的资源提供程序版本评估模板中的 API 版本。 自测试运行之日起不到两年的 API 版本被视为最新版本。 当有较新版本可用时，请勿使用预览版本。

指示未找到 API 版本的警告仅说明工具包缓存中不包含该版本。 使用最新版本的 API（推荐）时，可能会出现警告。

了解有关[工具包缓存](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache)的详细信息。

下面的示例未通过，因为 API 版本超过两年。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

下面的示例未通过，因为在有较新版本可用时使用了预览版本。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2020-08-01-preview",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

下面的示例通过，因为它是最新版本，不是预览版本。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-02-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]"
  }
]
```

## <a name="use-hard-coded-api-version"></a>使用硬编码的 API 版本

测试名称：不允许使用提供程序 apiVersions

资源类型的 API 版本决定了哪些属性可用。 在模板中提供硬编码的 API 版本。 不要检索在部署过程中确定的 API 版本，因为你不知道哪些属性可用。

下面的示例未通过。

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
    ...
  }
]
```

以下示例通过了测试。

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-12-01",
    ...
  }
]
```

## <a name="properties-cant-be-empty"></a>属性不能为空

测试名称：模板不应包含空白

不要将属性硬编码为空值。 空值包括 Null 和空字符串、对象或数组。 如果属性设置为空值，请从模板中删除该属性。 可以在部署过程中将属性设置为空值，例如通过参数设置。

[嵌套模板](linked-templates.md#nested-template)中的 `template` 属性可以包含空属性。 有关嵌套模板的详细信息，请参阅 [Microsoft.Resources deployments](/azure/templates/microsoft.resources/deployments)。

下面的示例未通过，因为有空属性。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {},
    "kind": ""
  }
]
```

以下示例通过的原因是属性包含值。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-01-01",
    "name": "storageaccount1",
    "location": "[parameters('location')]",
    "sku": {
      "name": "Standard_LRS",
      "tier": "Standard"
    },
    "kind": "Storage"
  }
]
```

## <a name="use-resource-id-functions"></a>使用资源 ID 函数

测试名称：ID 应派生自 ResourceID

指定资源 ID 时，请使用资源 ID 函数之一。 允许使用的函数包括：

- [resourceId](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)

不要使用 concat 函数创建资源 ID。 下面的示例未通过。

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

下一示例通过。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>ResourceId 函数具有正确的参数

测试名称：ResourceId 不应包含

生成资源 ID 时，不要为可选参数使用不必要的函数。 默认情况下，[resourceId](template-functions-resource.md#resourceid) 函数使用当前订阅和资源组。 不需要提供这些值。

下面的示例未通过，因为不需要提供当前订阅 ID 和资源组名称。

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

下一示例通过。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

此测试适用于：

- [resourceId](template-functions-resource.md#resourceid)
- [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
- [tenantResourceId](template-functions-resource.md#tenantresourceid)
- [extensionResourceId](template-functions-resource.md#extensionresourceid)
- [reference](template-functions-resource.md#reference)
- [list*](template-functions-resource.md#list)

对于 `reference` 和 `list*`，当使用 `concat` 构造资源 ID 时，无法通过测试。

## <a name="dependson-best-practices"></a>dependsOn 最佳做法

测试名称：DependsOn 最佳做法

设置部署依赖项时，不要使用 [if](template-functions-logical.md#if) 函数来测试某个条件。 如果一个资源依赖于[基于条件部署](conditional-resource-deployment.md)的资源，设置依赖项时应与任何其他资源一样。 条件资源未部署时，Azure 资源管理器会自动将其从所需依赖项中删除。

`dependsOn` 元素不能以 [concat](template-functions-array.md#concat) 函数开头。

下面的示例未通过，因为包含 `if` 函数。

```json
"dependsOn": [
  "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

下面的示例未通过，因为它以 `concat` 开头。

```json
"dependsOn": [
  "[concat(variables('storageAccountName'))]"
]
```

以下示例通过了测试。

```json
"dependsOn": [
  "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>嵌套或链接的部署不能使用调试

测试名称：部署资源不能使用调试

使用 `Microsoft.Resources/deployments` 资源类型定义[嵌套或链接模板](linked-templates.md)时，可以启用[调试](/azure/templates/microsoft.resources/deployments#debugsetting-object)。 当需要测试模板但可能会暴露敏感信息时，可以使用调试。 在生产环境中使用该模板之前，请关闭调试。 可以删除该 `debugSetting` 对象，或者将 `detailLevel` 属性更改为 `none`。

下面的示例未通过。

```json
"debugSetting": {
  "detailLevel": "requestContent"
}
```

以下示例通过了测试。

```json
"debugSetting": {
  "detailLevel": "none"
}
```

## <a name="admin-user-names-cant-be-literal-value"></a>管理员用户名不能是文本值

测试名称：adminUsername 不应为文本

设置 `adminUserName` 时，请不要使用文本值。 为用户名创建一个参数，并使用表达式来引用该参数的值。

下面的示例未通过，因为使用了文本值。

```json
"osProfile":  {
  "adminUserName": "myAdmin"
}
```

下面的示例通过，因为使用了表达式。

```json
"osProfile": {
  "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>使用最新的 VM 映像

测试名称：VM 映像应使用最新版本

此测试已禁用，但输出显示它已通过。 最佳做法是检查模板是否符合以下条件：

如果模板包含带有映像的虚拟机，请确保它使用的是最新版本的映像。

## <a name="use-stable-vm-images"></a>使用稳定的 VM 映像

测试名称：虚拟机不应为预览版

虚拟机不应使用预览映像。 该测试检查 `storageProfile` 以验证 `imageReference` 是否未使用包含“预览版”的字符串， 以及是否未在 `imageReference` 属性 `offer`、`sku` 或 `version` 中使用该“预览版”。

有关 `imageReference` 属性的详细信息，请参阅 [Microsoft.Compute virtualMachines](/azure/templates/microsoft.compute/virtualmachines#imagereference-object) 和 [Microsoft.Compute virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets#imagereference-object)。

由于 `imageReference` 是包含“预览版”的字符串，因此下面的示例未通过。

```json
"properties": {
  "storageProfile": {
    "imageReference": "latest-preview"
  }
}
```

当在 `offer`、`sku` 或 `version` 中使用“预览版”时，以下示例将不通过。

```json
"properties": {
  "storageProfile": {
    "imageReference": {
      "publisher": "Canonical",
      "offer": "UbuntuServer_preview",
      "sku": "16.04-LTS-preview",
      "version": "preview"
    }
  }
}
```

以下示例通过了测试。

```json
"storageProfile": {
  "imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
  }
}
```

## <a name="dont-use-managedidentity-extension"></a>不要使用 ManagedIdentity 扩展

测试名称：不能使用 ManagedIdentityExtension

不要将 `ManagedIdentity` 扩展应用于虚拟机。 扩展在 2019 版中已弃用，且不得再使用。

## <a name="outputs-cant-include-secrets"></a>输出不能包含机密

测试名称：输出不能包含机密

不要在 `outputs` 部分包含任何可能公开机密的值。 例如，`secureString` 或 `secureObject` 类型的安全参数，或 [list *](template-functions-resource.md#list) 函数，如 `listKeys`。

模板的输出存储在部署历史记录中，因此恶意用户可能会发现该信息。

下面的示例未通过，因为它在输出值中包含了安全参数。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secureParam": {
      "type": "secureString"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "string",
      "value": "[concat('this is the value ', parameters('secureParam'))]"
    }
  }
}
```

下面的示例未通过此测试，因为它在输出中使用了 [list*](template-functions-resource.md#list) 函数。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    }
  },
  "functions": [],
  "variables": {},
  "resources": [],
  "outputs": {
    "badResult": {
      "type": "object",
      "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
    }
  }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>将 protectedSettings 用于 commandToExecute 机密

测试名称：CommandToExecute 必须将 ProtectedSettings 用于机密

对于类型为 `CustomScript` 的资源，当 `commandToExecute` 包含密码等机密数据时，请使用加密的 `protectedSettings`。 例如，机密数据可用于 `secureString` 或 `secureObject` 类型的安全参数、[list*](template-functions-resource.md#list) 函数（如 `listKeys`）或自定义脚本。

请勿在 `settings` 对象中使用机密数据，因为它使用明文。 有关详细信息，请参阅 [Microsoft.Compute virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions)、[Windows](
/azure/virtual-machines/extensions/custom-script-windows) 或 [Linux](../../virtual-machines/extensions/custom-script-linux.md)。

下面的示例未通过，因为 `settings` 使用具有安全参数的 `commandToExecute`。

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

下面的示例未通过，因为 `settings` 使用具有 `listKeys` 函数的 `commandToExecute`。

```json
"properties": {
  "type": "CustomScript",
  "settings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

下面的示例通过，因为 `protectedSettings` 使用具有安全参数的 `commandToExecute`。

```json
"parameters": {
  "adminPassword": {
    "type": "secureString"
  }
}
...
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[parameters('adminPassword')]"
  }
}
```

下面的示例通过，因为 `protectedSettings` 使用具有 `listKeys` 函数的 `commandToExecute`。

```json
"properties": {
  "type": "CustomScript",
  "protectedSettings": {
    "commandToExecute": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-02-01')]"
  }
}
```

## <a name="use-recent-api-versions-in-reference-functions"></a>在引用函数中使用最新的 API 版本

测试名称：apiVersions 在引用函数中应该是最新的

[引用](template-functions-resource.md#reference)函数中使用的 API 版本必须是最新的，而且不能是预览版。 该测试根据工具包缓存中的资源提供程序版本评估模板中的 API 版本。 自测试运行之日起不到两年的 API 版本被视为最新版本。

指示未找到 API 版本的警告仅说明工具包缓存中不包含该版本。 使用最新版本的 API（推荐）时，可能会出现警告。

了解有关[工具包缓存](https://github.com/Azure/arm-ttk/tree/master/arm-ttk/cache)的详细信息。

下面的示例未通过，因为 API 版本超过两年。

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01')]"
  }
}
```

下面的示例未通过，因为 API 版本是预览版。

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2020-08-01-preview')]"
  }
}
```

下面的示例通过，因为 API 版本不到两年，且不是预览版。

```json
"outputs": {
  "stgAcct": {
    "type": "string",
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2021-02-01')]"
  }
}
```

## <a name="use-type-and-name-in-resourceid-functions"></a>在 resourceId 函数中使用类型和名称

测试名称：资源不应不明确

此测试已禁用，但输出显示它已通过。 最佳做法是检查模板是否符合以下条件：

[resourceId](template-functions-resource.md#resourceid) 必须包含资源类型和资源名称。 此测试查找模板的所有 `resourceId` 函数，并验证模板中使用的资源是否语法正确。 否则，该函数被视为不明确。

例如，函数 `resourceId` 被视为不明确：

- 在模板中未找到资源且未指定资源组时。
- 如果资源包含条件且未指定资源组。
- 如果相关资源包含部分但并非所有名称段。 例如，一个子资源包含多个名称段。 有关详细信息，请参阅 [resourceId 备注](template-functions-resource.md#remarks-3)。

## <a name="use-inner-scope-for-nested-deployment-secure-parameters"></a>使用内部作用域作为嵌套部署安全参数

测试名称：嵌套部署中的安全参数

使用具有 `inner` 作用域的嵌套模板的 `expressionEvaluationOptions` 对象评估包含 `secureString` 或 `secureObject` 类型的安全参数或 [list*](template-functions-resource.md#list) 函数（如 `listKeys`）的表达式。 如果使用 `outer` 作用域，则表达式在父模板的作用域内以明文计算。 然后，有权访问部署历史记录的所有人都可以看到该安全值。 `expressionEvaluationOptions` 的默认值为 `outer`。

有关嵌套模板的详细信息，请参阅 [Microsoft.Resources deployments](/azure/templates/microsoft.resources/deployments) 和[嵌套模板中的表达式计算范围](linked-templates.md#expression-evaluation-scope-in-nested-templates)。

下面的示例未通过，因为 `expressionEvaluationOptions` 使用 `outer` 作用域来评估安全参数或 `list*` 函数。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "outer"
      }
    }
  }
]
```

下面的示例通过，因为 `expressionEvaluationOptions` 使用 `inner` 作用域来评估安全参数或 `list*` 函数。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "nestedTemplate",
    "properties": {
      "expressionEvaluationOptions": {
        "scope": "inner"
      }
    }
  }
]
```

## <a name="next-steps"></a>后续步骤

- 若要了解如何运行测试工具包，请参阅[使用 ARM 模板测试工具包](test-toolkit.md)。
- 有关介绍如何使用测试工具包的 Microsoft Learn 模块，请参阅[使用 What-if 和 ARM 模板测试工具包来预览更改和验证 Azure 资源](/learn/modules/arm-template-test/)。
- 若要测试参数文件，请参阅[参数文件的测试用例](parameters.md)。
- 有关 createUiDefinition 测试，请参阅 [createUiDefinition.json 的测试用例](createUiDefinition-test-cases.md)。
- 若要了解所有文件的测试，请参阅[所有文件的测试用例](all-files-test-cases.md)。
