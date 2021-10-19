---
title: 创建和部署模板规格
description: 介绍如何创建模板规格并与组织中的其他用户共享。
ms.topic: conceptual
ms.date: 10/05/2021
ms.author: tomfitz
ms.custom: devx-track-azurepowershell
author: tfitzmac
ms.openlocfilehash: 8d8b582cdae8b387774402869eccf903a1b394b2
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613427"
---
# <a name="azure-resource-manager-template-specs"></a>Azure 资源管理器模板规格

模板规格是一种资源类型，用于在 Azure 中存储 Azure 资源管理器模板（ARM 模板），以便之后进行部署。 通过该资源类型，你可以与组织中的其他用户共享 ARM 模板。 与其他任何 Azure 资源一样，也可使用 Azure 基于角色的访问控制 (Azure RBAC) 来共享模板规格。

Microsoft.Resources/templateSpecs 是模板规格的资源类型。 它包含一个主模板和任意数量的链接模板。 Azure 将模板规格安全存储在资源组中。 模板规格支持[版本控制](#versioning)。

若要部署模板规格，请使用标准 Azure 工具（如 PowerShell）、Azure CLI、Azure 门户、REST 和其他受支持的 SDK 和客户端。 请使用针对模板使用的相同命令。

> [!NOTE]
> 若要将模板规格与 Azure PowerShell 一起使用，必须安装[版本 5.0.0 或更高版本](/powershell/azure/install-az-ps)。 若要将其与 Azure CLI 一起使用，请使用[版本 2.14.2 或更高版本](/cli/azure/install-azure-cli)。

设计部署时，应始终考虑资源的生命周期，并将具有相似生命周期的资源分组到单个模板规范。例如，你的部署包括多个 Cosmos DB 实例，其中每个实例都包含自己的数据库和容器。 如果数据库和容器不经常发生变化，则需要创建一个模板规范，使其包括 Cosmo DB 实例及其基础数据库和容器。 然后，便可以在模板中使用条件语句以及复制循环来创建这些资源的多个实例。

### <a name="microsoft-learn"></a>Microsoft Learn

若要详细了解模板规格和实际操作指南，请参阅 **Microsoft Learn** 中的 [使用模板规格发布可重用基础结构代码库](/learn/modules/arm-template-specs)。

## <a name="why-use-template-specs"></a>为什么使用模板规格？

模板规格具有以下优势：

* 可以使用标准 ARM 模板作为模板规格。
* 可以通过 Azure RBAC 而不是 SAS 令牌来管理访问。
* 用户可以在没有模板的写入访问权限的情况下部署模板规格。
* 可以将模板规格集成到现有的部署过程中，例如 PowerShell 脚本或 DevOps 管道。

使用模板规格，可以创建规范化的模板并与组织中的团队共享。 模板规格是安全的，因为 Azure 资源管理器可使用它进行部署，而没有正确权限的用户则无法访问。 用户只需具有模板规格的读取访问权限即可部署模板，因此可以在不允许其他人进行修改的情况下共享该模板。

如果 GitHub 存储库或存储帐户中当前有模板，在尝试共享和使用这些模板时会遇到一些困难。 若要部署模板，需要使模板要么可公开访问，要么使用 SAS 令牌管理访问。 为了避免此限制，用户可能会创建本地副本，这些副本最终会偏离原始模板。 模板规格简化了共享模板。

模板规格中包含的模板应由组织中的管理员按照组织的要求和指南进行验证。

## <a name="create-template-spec"></a>创建模板规格

以下示例显示了一个用于在 Azure 中创建存储帐户的简单模板。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

创建模板规格时，会将 PowerShell 或 CLI 命令传递给主模板文件。 如果主模板引用链接模板，则命令将查找并打包它们以创建模板规格。若要了解详细信息，请参阅[创建具有链接模板的模板规格](#create-a-template-spec-with-linked-templates)。

使用以下命令创建模板规格：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0a -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

可以使用以下命令查看订阅中的所有模板规格：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

可以使用以下命令查看模板规格的详细信息（包括其版本）：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0a"
```

---

## <a name="deploy-template-spec"></a>部署模板规格

创建模板规格后，具有对模板规格的读取访问权限的用户可以部署它。 有关授予访问权限的信息，请参阅[教程：使用 Azure PowerShell 授予组对 Azure 资源的访问权限](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)。

可通过门户、PowerShell、Azure CLI 部署模板规格，或者在更大型的模板部署中将其作为链接模板进行部署。 组织中的用户可以将模板规格部署到 Azure 中的任何范围（资源组、订阅、管理组或租户）。

可通过提供模板规格的资源 ID 来部署该模板规格，而无需传递模板的路径或 URI。 资源 ID 采用以下格式：

/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}

请注意，资源 ID 包括模板规格的版本名称。

例如，可以使用以下命令部署模板规格。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

实际上，通常需要运行 `Get-AzTemplateSpec` 或 `az ts show` 来获取要部署的模板规格的 ID。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0a).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0a" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

还可以打开以下格式的 URL 以部署模板规格：

```url
https://portal.azure.com/#create/Microsoft.Template/templateSpecVersionId/%2fsubscriptions%2f{subscription-id}%2fresourceGroups%2f{resource-group-name}%2fproviders%2fMicrosoft.Resources%2ftemplateSpecs%2f{template-spec-name}%2fversions%2f{template-spec-version}
```

## <a name="parameters"></a>参数

将参数传递到模板规格与将参数传递到 ARM 模板的过程是完全相同的。 以内联方式添加参数值或将参数值添加到参数文件中。

若要以内联方式传递参数，请使用：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

若要创建本地参数文件，请使用：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

并使用以下命令传递该参数文件：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="versioning"></a>版本控制

创建模板规格时，需要为其提供版本名称。 循环访问模板代码时，可以更新现有版本（获取修补程序）或发布新版本。 版本是文本字符串。 可以选择遵循任意版本控制系统，包括语义化版本控制。 模板规格的用户可提供部署模板规格时要使用的版本名称。

## <a name="use-tags"></a>使用标记

可以通过[标记](../management/tag-resources.md)对资源进行逻辑组织。 可以使用 Azure PowerShell 和 Azure CLI 将标记签添加到模板规格中：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts update \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

使用指定的版本参数（而不使用标记参数）创建或修改模板规格时：

- 如果模板规格存在且具有标记，但是版本不存在，则新版本将继承与现有模板规格相同的标记。

使用指定的标记参数和版本参数创建或修改模板规格时：

- 如果模板规格和版本都不存在，则将标记添加到新模板规格和新版本中。
- 如果模板规格存在，但版本不存在，则仅将标记添加到新版本中。
- 如果同时存在模板规格和版本，则标记仅适用于版本。

当使用指定的标记参数修改模板但未指定版本参数时，仅将标记添加到模板规格中。

## <a name="create-a-template-spec-with-linked-templates"></a>创建具有链接模板的模板规格

如果模板规格的主模板引用了链接模板，则 PowerShell 和 CLI 命令可以自动查找并打包本地驱动器中的链接模板。 无需手动配置存储帐户或存储库即可托管模板规格 - 模板规格资源中包含了所有内容。

以下示例中包含了具有两个链接模板的主模板。 该示例只是模板的摘录。 请注意，它使用名为 `relativePath` 的属性链接到其他模板。 必须为部署资源使用 `2020-06-01` 的 `apiVersion` 或更高版本。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

在上一个示例中执行用于创建模板规格的 PowerShell 或 CLI 命令时，该命令会查找三个文件 - 主模板、Web 应用模板 (`webapp.json`) 和数据库模板 (`database.json`) - 并将它们打包到模板规格中。

有关详细信息，请参阅[教程：创建具有链接模板的模板规格](template-specs-create-linked.md)。

## <a name="deploy-template-spec-as-a-linked-template"></a>将模板规格部署为链接模板

创建模板规格之后，可以轻松从 ARM 模板或其他模板规格中重复使用它。可以通过将模板规格的资源 ID 添加到模板来链接该模板规范。 部署主模板时，会自动部署链接的模板规格。 此行为使你能够开发模块化模板规范并根据需要重复利用。

例如，可以创建一个用于部署网络资源的模板模板规格和一个用于部署存储资源的模板规格。 在 ARM 模板中，可随时在需要配置网络或存储资源时链接到这两个模板规格。

以下示例与前面的示例的类似，不同的是使用 `id` 属性链接到模板规格，而不是使用 `relativePath` 属性链接到本地模板。 使用 `2020-06-01` 作为部署资源的 API 版本。 在此示例中，模板规格位于名为 templateSpecsRG 的资源组中。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0a')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0a')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

有关链接模板规格的详细信息，请参阅[教程：将模板规格部署为链接模板](template-specs-deploy-linked-template.md)。

## <a name="next-steps"></a>后续步骤

* 若要创建和部署模板规格，请参阅[快速入门：创建和部署模板规格](quickstart-create-template-specs.md)。

* 有关链接模板规格中的模板的详细信息，请参阅[教程：创建具有链接模板的模板规格](template-specs-create-linked.md)。

* 有关将模板规格部署为链接模板的详细信息，请参阅[教程：将模板规格部署为链接模板](template-specs-deploy-linked-template.md)。
