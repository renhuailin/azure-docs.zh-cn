---
title: 用于部署的链接模板
description: 介绍如何使用 Azure 资源管理器模板（ARM 模板）中的链接模板创建一个模块化的模板解决方案。 演示如何传递参数值、指定参数文件和动态创建的 URL。
ms.topic: conceptual
ms.date: 09/10/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: b8be710611d892913c43e9d500a051a3d3b55ca5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820505"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>部署 Azure 资源时使用链接模版和嵌套模版

若要部署复杂的解决方案，可以将 Azure 资源管理器模板（ARM 模板）分解为许多相关模板，然后通过主模板将它们一起部署。 相关模板可以是嵌入在主模板内的单独文件或模板语法。 本文使用术语“链接模板”来指代一个通过主模板中的链接进行引用的单独模板文件。 它使用术语 **嵌套模板** 指代主模板内嵌套的模板语法。

对于中小型解决方案，单个模板更易于理解和维护。 可以查看单个文件中的所有资源和值。 对于高级方案，使用链接模板可将解决方案分解为目标组件。 可以轻松地将这些模板重复用于其他方案。

如需教程，请参阅[教程：部署链接模板](./deployment-tutorial-linked-template.md)。

> [!NOTE]
> 对于链接模板或嵌套模板，只能将部署模式设置为[增量](deployment-modes.md)。 但是，主模板可以在完整模式下进行部署。 如果在完整模式下部署主模板，并且链接模板或嵌套模板以相同的资源组为目标，则在链接模板或嵌套模板中部署的资源会包括在针对完整模式部署进行的评估中。 将在主模板和链接模板或嵌套模板中部署的资源的合并集合与资源组中的现有资源进行比较。 此合并集合中未包含的任何资源都会被删除。
>
> 如果链接模板或嵌套模板针对不同的资源组，则该部署使用增量模式。
>

## <a name="nested-template"></a>嵌套模板

若要嵌套某个模板，请向主模板中添加一个[部署资源](/azure/templates/microsoft.resources/deployments)。 在 `template` 属性中，指定模板语法。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

以下示例通过嵌套模板部署一个存储帐户。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2021-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>嵌套模板中的表达式计算作用域

使用嵌套模板时，可以指定是在父模板的作用域内还是在嵌套模板的作用域内计算模板表达式。 作用域决定了如何对参数、变量和函数（例如 [resourceGroup](template-functions-resource.md#resourcegroup) 和 [subscription](template-functions-resource.md#subscription)）进行求解。

可以通过 `expressionEvaluationOptions` 属性设置作用域。 默认情况下，`expressionEvaluationOptions` 属性设置为 `outer`，这意味着它使用父模板作用域。 将值设置为“`inner`”可使表达式在嵌套模板的作用域内进行计算。

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2021-04-01",
  "name": "nestedTemplate1",
  "properties": {
    "expressionEvaluationOptions": {
      "scope": "inner"
    },
  ...
```

> [!NOTE]
>
> 当作用域设置为 `outer` 时，对于已在嵌套模板中部署的资源，无法在嵌套模板的 outputs 节中使用 `reference` 函数。 若要返回嵌套模板中部署的资源的值，请使用 `inner` 作用域或将嵌套模板转换为链接模板。

以下模板演示了如何根据作用域对模板表达式进行求解。 它包含一个名为 `exampleVar` 的变量，父模板和嵌套模板中都定义了此变量。 它返回此变量的值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

`exampleVar` 的值因 `expressionEvaluationOptions` 中 `scope` 属性的值而异。 下表显示了这两个作用域的结果。

| 评估范围 | 输出 |
| ----- | ------ |
| 内部 | from nested template |
| outer（或默认值） | from parent template |

下面的示例部署一个 SQL Server，并检索要用于密码的密钥保管库机密。 作用域设置为 `inner`，因为它会动态创建密钥保管库 ID（请参见外部模板 `parameters` 中的 `adminPassword.reference.keyVault`）并将其作为参数传递到嵌套模板。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2021-02-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

使用嵌套模板中的安全参数值时请小心。 如果将范围设置为“外部”，则安全值将作为纯文本存储在部署历史记录中。 在部署历史记录中查看模板的用户可以看到安全值。 改为使用内部范围或向父模板添加需要安全值的资源。

以下摘录显示了哪些值是安全的，哪些值是不安全的。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPasswordOrKey": {
      "type": "securestring",
      "metadata": {
        "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
      }
    }
  },
  ...
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2021-04-01",
      "name": "mainTemplate",
      "properties": {
        ...
        "osProfile": {
          "computerName": "mainTemplate",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in parent template
        }
      }
    },
    {
      "name": "outer",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "outer"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2021-04-01",
              "name": "outer",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "outer",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // No, not secure because resource is in nested template with outer scope
                }
              }
            }
          ]
        }
      }
    },
    {
      "name": "inner",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "parameters": {
          "adminPasswordOrKey": {
              "value": "[parameters('adminPasswordOrKey')]"
          },
          "adminUsername": {
              "value": "[parameters('adminUsername')]"
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": {
              "type": "string",
              "metadata": {
                "description": "Username for the Virtual Machine."
              }
            },
            "adminPasswordOrKey": {
              "type": "securestring",
              "metadata": {
                "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2021-04-01",
              "name": "inner",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "inner",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in nested template and scope is inner
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="linked-template"></a>链接的模板

若要链接某个模板，请向主模板中添加一个[部署资源](/azure/templates/microsoft.resources/deployments)。 在 `templateLink` 属性中，指定要包括的模板的 URI。 以下示例链接到存储帐户中的模板。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

引用链接模板时，`uri` 的值不能是本地文件或只能在本地网络上使用的文件。 Azure 资源管理器必须能够访问该模板。 提供可下载的 HTTP 或 HTTPS 形式的 URI 值。

可以使用包含 HTTP 或 HTTPS 的参数来引用模板。 例如，一种常见模式是使用 `_artifactsLocation` 参数。 可以使用如下所示的表达式来设置链接模板：

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

如果要链接到 GitHub 中的模板，请使用原始 URL。 链接的格式为：`https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json`。 若要获取原始链接，请选择“原始”。

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="选择原始 URL":::

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

### <a name="parameters-for-linked-template"></a>链接模板的参数

可以在外部文件中或以内联方式为链接模板提供参数。 提供外部参数文件时，请使用 `parametersLink` 属性：

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

若要以内联方式传递参数值，请使用 `parameters` 属性。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parameters": {
        "storageAccountName": {
          "value": "[parameters('storageAccountName')]"
        }
      }
    }
  }
]
```

不能同时使用内联参数和指向参数文件的链接。 同时指定 `parametersLink` 和 `parameters` 时，部署将失败，并出现错误。

### <a name="use-relative-path-for-linked-templates"></a>为链接模板使用相对路径

使用 `Microsoft.Resources/deployments` 的 `relativePath` 属性可以更轻松地创作链接模板。 可以使用此属性在相对于父级的某个位置部署远程链接模板。 此功能要求暂存所有模板文件并使其可通过远程 URI（例如 GitHub 或 Azure 存储帐户）进行访问。 通过 Azure PowerShell 或 Azure CLI 使用 URI 调用主模板时，子部署 URI 是父级和 relativePath 的组合。

> [!NOTE]
> 当创建 templateSpec 时，`relativePath` 属性引用的任何模板都将通过 Azure PowerShell 或 Azure CLI 打包在 templateSpec 资源中。 它不要求暂存文件。 有关详细信息，请参阅[创建具有链接模板的模板规范](./template-specs.md#create-a-template-spec-with-linked-templates)。

假设文件夹结构如下所示：

![资源管理器链接模板相对路径](./media/linked-templates/resource-manager-linked-templates-relative-path.png)

以下模板展示了 mainTemplate.json 如何部署上图中所示的 nestedChild.json。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "childLinked",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "children/nestedChild.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

在以下部署中，上面的模板中链接模板的 URI 是 **https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/children/nestedChild.json** 。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

---

若要使用在 Azure 存储帐户中存储的相对路径来部署链接模板，请使用 `QueryString`/`query-string` 参数指定要与 TemplateUri 参数一起使用的 SAS 令牌。 只有 Azure CLI 2.18 版或更高版本和 Azure PowerShell 5.4 版或更高版本支持此参数。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

---

请确保 QueryString 中没有前导“？”。 在组装部署的 URI 时，部署会添加一个“？”。

## <a name="template-specs"></a>模板规格

你可创建一个[模板规范](template-specs.md)，将主模板及其链接模板打包到可部署的单个实体中，而不是在可访问的终结点上维护链接模板。 模板规格是 Azure 订阅中的资源。 这使你可以轻松地与组织中的用户安全地共享模板。 可使用 Azure 基于角色的访问控制 (Azure RBAC) 来授予对模板规格的访问权限。此功能目前以预览版提供。

有关详细信息，请参阅：

- [教程：创建具有链接模板的模板规格](./template-specs-create-linked.md)。
- [教程：将模板规格部署为链接模板](./template-specs-deploy-linked-template.md)。

## <a name="dependencies"></a>依赖项

与其他资源类型一样，你可以在链接模板之间设置依赖关系。 如果某个链接模板中的资源必须在第二个链接模板中的资源之前部署，请设置第二个模板依赖于第一个模板。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>contentVersion

无需为 `templateLink` 或 `parametersLink` 属性提供 `contentVersion` 属性。 如果未提供 `contentVersion`，则会部署模板的当前版本。 如果提供内容版本值，它必须与链接的模板中的版本相匹配；否则，部署失败并产生错误。

## <a name="using-variables-to-link-templates"></a>使用变量来链接模板

前面的示例演示了用于模板链接的硬编码 URL 值。 这种方法可能适用于某个简单的模板，但不适用于一组大型模块化模板。 相反，可以创建一个存储主模板的基 URL 的静态变量，并从基 URL 动态创建用于链接模板的 URL。 这种方法的好处是可以轻松地移动或派生模板，因为只需在主模板中更改静态变量。 主模板会在整个分解后的模板中传递正确的 URI。

以下示例演示如何使用基 URL 来创建两个用于链接模板的 URL（`sharedTemplateUrl` 和 `vmTemplateUrl`）。

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/application-workloads/postgre/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

还可以使用 [deployment()](template-functions-deployment.md#deployment) 获取当前模板的基 URL，并使用该 URL 来获取同一位置其他模板的 URL。 如果模板位置发生变化或者想要避免对模板文件中的 URL 进行硬编码，则此方法非常有用。 仅当链接到带有 URL 的远程模板时，才会返回 `templateLink` 属性。 如果使用的是本地模板，该属性不可用。

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

最终，会在 `templateLink` 属性的 `uri` 属性中使用变量。

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>使用副本

若要使用嵌套的模板创建资源的多个实例，请在 `Microsoft.Resources/deployments` 资源的级别添加 `copy` 元素。 或者，如果范围为 `inner`，则可以在嵌套模板中添加副本。

以下示例模板展示了如何将 `copy` 与嵌套的模板配合使用。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2021-04-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy": {
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "expressionEvaluationOptions": {
        "scope": "inner"
      },
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2021-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "StorageV2"
            // Copy works here when scope is inner
            // But, when scope is default or outer, you get an error
            // "copy": {
            //   "name": "storagecopy",
            //   "count": 2
            // }
          }
        ]
      }
    }
  }
]
```

## <a name="get-values-from-linked-template"></a>从链接模板中获取值

若要从链接模板中获取输出值，请使用如下所示的语法检索属性值：`"[reference('deploymentName').outputs.propertyName.value]"`。

从链接模板获取输出属性时，属性名称不能包含短划线。

以下示例演示如何引用链接模板和检索输出值。 链接模板返回一条简单的消息。 首先，让我们看看链接模板：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

主模板部署链接模板并获取返回值。 请注意，该模板按名称引用部署资源，并使用链接模板返回的属性的名称。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

以下示例显示一个模板，该模板部署公共 IP 地址并返回该公共 IP 的 Azure 资源的资源 ID：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

在部署负载均衡器时，若要使用前面所述模板中的公共 IP 地址，请链接到该模板，并声明对 `Microsoft.Resources/deployments` 资源的依赖性。 负载均衡器上的公共 IP 地址设置为链接模板的输出值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>部署历史记录

资源管理器将每个模板作为部署历史记录中的单独部署进行处理。 包含三个链接模板或嵌套模板的主模板在部署历史记录中显示为：

![部署历史记录](./media/linked-templates/deployment-history.png)

部署后，可以使用历史记录中这些不同的条目来检索输出值。 以下模板创建一个公共 IP 地址并输出该 IP 地址：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2021-02-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

以下模板链接到前面所述的模板。 它创建三个公共 IP 地址。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

部署后，可使用以下 PowerShell 脚本检索输出值：

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

还可以使用 Bash shell 中的 Azure CLI 脚本：

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>保护外部模板

尽管链接模板必须可从外部使用，但它无需向公众正式发布。 可以将模板添加到只有存储帐户所有者可以访问的专用存储帐户。 然后，在部署期间创建共享访问签名 (SAS) 令牌来启用访问。 将该 SAS 令牌添加到链接模板的 URI。 即使令牌作为安全字符串传入，链接模板的 URI（包括 SAS 令牌）也会记录在部署操作中。 若要限制公开，请设置令牌的到期时间。

也可将参数文件限制为通过 SAS 令牌进行访问。

目前，无法链接到位于 [Azure 存储防火墙](../../storage/common/storage-network-security.md)后面的存储帐户中的模板。

> [!IMPORTANT]
> 请考虑创建[模板规格](template-specs.md)，而不是使用 SAS 令牌保护链接模板。模板规格将主模板及其链接模板作为资源安全地存储在 Azure 订阅中。 使用 Azure RBAC 向需要部署模板的用户授予访问权限。

以下示例演示在链接到模板时如何传递 SAS 令牌：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "securestring" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

在 PowerShell 中，使用以下命令获取容器的令牌并部署模板。 注意，`containerSasToken` 参数是在模板中定义的。 它不是 `New-AzResourceGroupDeployment` 命令中的参数。

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

对于 Bash Shell 中的 Azure CLI，使用以下代码获取容器的令牌并部署模板：

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>示例模板

下面的示例展示了链接的模板的常见用途。

|主模板  |链接的模板 |说明  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[链接的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | 从链接的模板返回字符串。 |
|[使用公共 IP 地址的负载均衡器](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[链接的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |从链接的模板返回公共 IP 地址并在负载均衡器中设置该值。 |
|[多个 IP 地址](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [链接的模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |在链接模板中创建多个公共 IP 地址。  |

## <a name="next-steps"></a>后续步骤

- 若要完成教程，请参阅[教程：部署链接模板](./deployment-tutorial-linked-template.md)。
- 若要了解如何为资源定义部署顺序，请参阅[在 ARM 模板中定义资源的部署顺序](./resource-dependency.md)。
- 若要了解如何定义一个资源但要创建它的多个实例，请参阅 [ARM 模板中的资源迭代](copy-resources.md)。
- 有关在存储帐户中设置模板和生成 SAS 令牌的步骤，请参阅[使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)或[使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)。
