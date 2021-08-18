---
title: 为 Bicep 创建参数文件
description: 创建用于在 Bicep 文件的部署过程中传入值的参数文件
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 4628b7d6a04bdec2a7ec4273536bf895dc23a5d9
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112292224"
---
# <a name="create-bicep-parameter-file"></a>创建 Bicep 参数文件

可使用包含参数值的 JSON 文件，而不是在脚本中以内联值的形式传递参数。 本文介绍如何创建与 Bicep 文件配合使用的参数文件。

## <a name="parameter-file"></a>参数文件

参数文件使用以下格式：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

请注意，参数文件以纯文本形式存储参数值。 此方法适用于不敏感的值，例如资源 SKU。 纯文本不适用于敏感值（如密码）。 如果需要传递包含敏感值的参数，请将该值存储在密钥保管库中。 使用 [getSecret 函数](bicep-functions-resource.md#getsecret)检索敏感值，而不是将敏感值添加到参数文件。 有关详细信息，请参阅[在部署 Bicep 过程中使用 Azure Key Vault 传递安全参数值](key-vault-parameter.md)。

## <a name="define-parameter-values"></a>定义参数值

若要确定如何定义参数名称和值，请打开 Bicep 文件。 查看 Bicep 文件的 parameters 节。 以下示例显示了 Bicep 文件中的参数。

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

在参数文件中，要注意的第一个详细信息是每个参数的名称。 参数文件中的参数名称必须与 Bicep 文件中的参数名称匹配。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

注意参数类型。 参数文件中的参数类型必须使用与 Bicep 文件相同的类型。 在本例中，两个参数类型都是字符串。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

检查 Bicep 文件中的参数是否有默认值。 如果参数具有默认值，则可以在参数文件中提供值，但不一定要这样做。 参数文件值会替代 Bicep 文件的默认值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Bicep will use default value if not provided.
    }
  }
}
```

检查 Bicep 的允许值和任何限制，例如最大长度。 这些值指定可为参数提供的值的范围。 在本例中，`storagePrefix` 最多可以有 11 个字符，`storageAccountType` 必须指定允许的值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

> [!NOTE]
> 参数文件只能包含 Bicep 文件中定义的参数的值。 如果参数文件包含的额外参数与 Bicep 文件的参数不匹配，则会收到错误。

## <a name="parameter-type-formats"></a>参数类型格式

下面的示例显示了不同参数类型的格式：字符串、整数、布尔值、数组和对象。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
  }
}
```

## <a name="deploy-bicep-file-with-parameter-file"></a>使用参数文件部署 Bicep 文件

在 Azure CLI 中，使用 `@` 和参数文件名传递本地参数文件。 例如，`@storage.parameters.json`。

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.bicep \
  --parameters @storage.parameters.json
```

有关详细信息，请参阅[使用 Bicep 和 Azure CLI 部署资源](./deploy-cli.md#parameters)。 若要部署 .bicep 文件，需要 Azure CLI 2.20 或更高版本。

在 Azure PowerShell 中，使用 `TemplateParameterFile` 参数传递本地参数文件。

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.bicep `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

有关详细信息，请参阅[使用 Bicep 和 Azure PowerShell 部署资源](./deploy-powershell.md#parameters)。 若要部署 .bicep 文件，需要 Azure PowerShell 5.6.0 或更高版本。

## <a name="file-name"></a>文件名

参数文件的一般命名约定是将 parameters 包含在 Bicep 文件名中。 例如，如果 Bicep 文件名为 azuredeploy.bicep，则参数文件名为 azuredeploy.parameters.json。 此命名约定可帮助你了解 Bicep 文件与参数之间的关系。

若要部署到不同的环境，请创建多个参数文件。 命名参数文件时，请标识其用途，如开发和生产。 例如，使用 azuredeploy.parameters-dev.json 和 azuredeploy.parameters-prod.json 部署资源 。

## <a name="parameter-precedence"></a>参数优先级

可以在同一部署操作中使用内联参数和本地参数文件。 例如，可以在本地参数文件中指定某些值，并在部署期间添加其他内联值。 如果同时为本地参数文件中的参数和内联参数提供值，则内联值优先。

可以通过提供文件的 URI 来使用外部参数文件。 使用外部参数文件时，不能传递是内联值的或来自本地文件的其他值。 会忽略所有内联参数。 提供外部文件中的所有参数值。

## <a name="parameter-name-conflicts"></a>参数名冲突

如果 Bicep 文件中有一个参数与 PowerShell 命令中的某个参数同名，PowerShell 会使用后缀 `FromTemplate` 显示 Bicep 文件中的参数。 例如，Bicep 文件中名为 `ResourceGroupName` 的参数与 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet 中的 `ResourceGroupName` 参数冲突。 系统会提示你提供 `ResourceGroupNameFromTemplate` 的值。 为了避免这种混淆，请使用未用于部署命令的参数名称。

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何在 Bicep 文件中定义参数，请参阅 [Bicep 中的参数](./parameters.md)。
- 要获取敏感值，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](./key-vault-parameter.md)。
