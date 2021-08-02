---
title: 创建参数文件
description: 创建用于在 Azure 资源管理器模板部署过程中传入值的参数文件
ms.topic: conceptual
ms.date: 05/11/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8b7d9afc91462ec3dc61b25135460e347b121e5c
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111960281"
---
# <a name="create-resource-manager-parameter-file"></a>创建资源管理器参数文件

可使用包含参数值的 JSON 文件，而不是在脚本中以内联值的形式传递参数。 本文介绍如何创建用于 JSON 模板的参数文件。

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

请注意，参数文件以纯文本形式存储参数值。 此方法适用于不敏感的值，例如资源 SKU。 纯文本不适用于敏感值（如密码）。 如果需要传递包含敏感值的参数，请将该值存储在密钥保管库中。 然后引用参数文件中的密钥保管库。 在部署过程中会安全地检索敏感值。

以下参数文件包含一个纯文本值和一个存储在密钥保管库中的敏感值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

有关使用 Key Vault 中的值的详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](key-vault-parameter.md)。

## <a name="define-parameter-values"></a>定义参数值

若要确定如何定义参数名称和值，请打开 JSON 模板并查看 `parameters` 部分。 下面的示例显示了 JSON 模板的参数。

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

在参数文件中，要注意的第一个详细信息是每个参数的名称。 参数文件中的参数名必须与模板中的参数名匹配。

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

注意参数类型。 参数文件中的参数类型必须使用与模板相同的类型。 在本例中，两个参数类型都是字符串。

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

检查模板中的参数是否有默认值。 如果参数具有默认值，则可以在参数文件中提供值，但不一定要这样做。 参数文件值会替代模板的默认值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

检查模板的允许值和任何限制，例如最大长度。 这些值指定可为参数提供的值的范围。 在本例中，`storagePrefix` 最多可以有 11 个字符，`storageAccountType` 必须指定允许的值。

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
> 参数文件只能包含在模板中定义的参数的值。 如果参数文件包含的额外参数与模板参数不匹配，则会收到错误。

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

## <a name="deploy-template-with-parameter-file"></a>使用参数文件部署模板

在 Azure CLI 中，使用 `@` 和参数文件名传递本地参数文件。 例如 `@storage.parameters.json`。

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

有关详细信息，请参阅[使用 ARM 模板和 Azure CLI 部署资源](./deploy-cli.md#parameters)。

在 Azure PowerShell 中，使用 `TemplateParameterFile` 参数传递本地参数文件。

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

有关详细信息，请参阅[使用 ARM 模板和 Azure PowerShell 部署资源](./deploy-powershell.md#pass-parameter-values)。

> [!NOTE]
> 不能在门户中将参数文件与自定义模板边栏选项卡一起使用。

> [!TIP]
> 如果要使用 [Visual Studio 中的 Azure 资源组项目](create-visual-studio-deployment-project.md)，请确保将参数文件的“生成操作”设置为“内容” 。

## <a name="file-name"></a>文件名

参数文件的一般命名约定是在模板名称中包含“parameters”。 例如，如果模板名为 _azuredeploy.json_，则参数文件名为 _azuredeploy.parameters.json_。 此命名约定可帮助你查看模板与参数之间的连接。

若要部署到不同的环境，请创建多个参数文件。 命名参数文件时，请标识其用途，如开发和生产。 例如，使用 azuredeploy.parameters-dev.json 和 azuredeploy.parameters-prod.json 部署资源 。

## <a name="parameter-precedence"></a>参数优先级

可以在同一部署操作中使用内联参数和本地参数文件。 例如，可以在本地参数文件中指定某些值，并在部署期间添加其他内联值。 如果同时为本地参数文件中的参数和内联参数提供值，则内联值优先。

可以通过提供文件的 URI 来使用外部参数文件。 使用外部参数文件时，不能传递是内联值的或来自本地文件的其他值。 会忽略所有内联参数。 提供外部文件中的所有参数值。

## <a name="parameter-name-conflicts"></a>参数名冲突

如果模板中有一个参数与 PowerShell 命令中的某个参数同名，PowerShell 会使用后缀 `FromTemplate` 显示模板的参数。 例如，模板中名为 `ResourceGroupName` 的参数与 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet 中的 `ResourceGroupName` 参数冲突。 系统会提示你提供 `ResourceGroupNameFromTemplate` 的值。 为了避免这种混淆，请使用未用于部署命令的参数名称。

## <a name="next-steps"></a>后续步骤

- 有关如何在模板中定义参数的详细信息，请参阅 [ARM 模板中的参数](./parameters.md)。
- 有关使用 Key Vault 中的值的详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](key-vault-parameter.md)。