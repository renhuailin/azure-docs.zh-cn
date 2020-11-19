---
title: 将资源部署到资源组
description: 介绍如何在 Azure 资源管理器模板中部署资源。 它演示如何将多个资源组作为目标。
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 5e33f0d505759944ccaf2233aa122b6ab701c91f
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917420"
---
# <a name="resource-group-deployments-with-arm-templates"></a>具有 ARM 模板的资源组部署

本文介绍如何将部署范围限定为资源组。 使用 Azure 资源管理器模板 (ARM 模板) 用于部署。 本文还介绍了如何在部署操作中扩展资源组之外的范围。

## <a name="supported-resources"></a>支持的资源

大多数资源可部署到资源组。 有关可用资源的列表，请参阅 [ARM 模板引用](/azure/templates)。

## <a name="schema"></a>架构

对于模板，请使用以下架构：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

对于参数文件，请使用：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>部署命令

若要部署到资源组，请使用资源组部署命令。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

对于 Azure CLI，请使用 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)。 以下示例会部署一个模板来创建资源组：

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

对于 PowerShell 部署命令，请使用 [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。 以下示例会部署一个模板来创建资源组：

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

有关部署 ARM 模板的部署命令和选项的更多详细信息，请参阅：

* [使用 ARM 模板和 Azure 门户部署资源](deploy-portal.md)
* [使用 ARM 模板和 Azure CLI 部署资源](deploy-cli.md)
* [使用 ARM 模板和 Azure PowerShell 部署资源](deploy-powershell.md)
* [利用 ARM 模板和 Azure 资源管理器部署资源 REST API](deploy-rest.md)
* [使用部署按钮从 GitHub 存储库部署模板](deploy-to-azure-button.md)
* [从 Cloud Shell 部署 ARM 模板](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>部署范围

部署到资源组时，可以将资源部署到：

* 操作中的目标资源组
* 同一订阅或其他订阅中的其他资源组
* 租户中的任何订阅
* 资源组的租户
* [扩展资源](scope-extension-resources.md) 可以应用于资源

部署模板的用户必须有权访问指定的作用域。

本部分说明如何指定不同的范围。 可以在单个模板中组合这些不同的范围。

### <a name="scope-to-target-resource-group"></a>作用域到目标资源组

若要将资源部署到目标资源，请将这些资源添加到模板的 resources 节中。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

有关示例模板，请参阅 [部署到目标资源组](#deploy-to-target-resource-group)。

### <a name="scope-to-resource-group-in-same-subscription"></a>作用域到同一订阅中的资源组

若要将资源部署到同一订阅中的其他资源组，请添加嵌套部署并包括 `resourceGroup` 属性。 如果未指定订阅 ID 或资源组，将使用父模板中的订阅和资源组。 在运行部署之前，所有资源组都必须存在。

在以下示例中，嵌套部署以名为 `demoResourceGroup` 的资源组为目标。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

有关示例模板，请参阅 [部署到多个资源组](#deploy-to-multiple-resource-groups)。

### <a name="scope-to-resource-group-in-different-subscription"></a>不同订阅中的资源组的范围

若要将资源部署到不同订阅中的资源组，请添加嵌套部署并包括 `subscriptionId` 和 `resourceGroup` 属性。 在以下示例中，嵌套部署以名为 `demoResourceGroup` 的资源组为目标。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

有关示例模板，请参阅 [部署到多个资源组](#deploy-to-multiple-resource-groups)。

### <a name="scope-to-subscription"></a>作用域到订阅

若要将资源部署到订阅，请添加嵌套部署并包括 `subscriptionId` 属性。 订阅可以是目标资源组的订阅，也可以是租户中的任何其他订阅。 此外， `location` 为嵌套部署设置属性。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-subscription.json" highlight="9,10,14":::

有关示例模板，请参阅 [创建资源组](#create-resource-group)。

### <a name="scope-to-tenant"></a>作用域到租户

可以通过将设置为来在租户中创建资源 `scope` `/` 。 部署模板的用户必须具有 [所需的访问权限才能在租户上进行部署](deploy-to-tenant.md#required-access)。

你可以使用嵌套的部署 `scope` 并 `location` 设置。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-tenant.json" highlight="9,10,14":::

或者，你可以 `/` 为某些资源类型（例如管理组）设置作用域。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-create-mg.json" highlight="12,15":::

## <a name="deploy-to-target-resource-group"></a>部署到目标资源组

若要在目标资源组中部署资源，请在模板的 **resources** 节中定义这些资源。 以下模板在部署操作中指定的资源组中创建存储帐户。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

## <a name="deploy-to-multiple-resource-groups"></a>部署到多个资源组

可以在单个 ARM 模板中部署到多个资源组。 若要将与父模板不同的资源组作为目标，请使用[嵌套或链接模板](linked-templates.md)。 在部署资源类型中，为要将嵌套模板部署到的订阅 ID 和资源组指定值。 资源组可以存在于不同的订阅中。

> [!NOTE]
> 在单个部署中可以部署到 800 个资源组。 通常情况下，此限制意味着，在嵌套或链接的部署中可以部署到为父模板指定的一个资源组和最多 799 个资源组。 但是，如果父模板仅包含嵌套或链接的模板，并且本身不部署任何资源，则在嵌套或链接的部署中最多可包含 800 个资源组。

以下示例部署两个存储帐户。 第一个存储帐户部署到在部署操作中指定的资源组。 第二个存储帐户部署到在 `secondResourceGroup` 和 `secondSubscriptionID` 参数中指定的资源组：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

如果将 `resourceGroup` 设置为不存在的资源组的名称，则部署会失败。

若要测试上述模板并查看结果，请使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要将两个存储帐户部署到 **同一订阅** 中的两个资源组，请使用：

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

若要将两个存储帐户部署到 **两个订阅**，请使用：

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Set-AzContext -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Set-AzContext -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要将两个存储帐户部署到 **同一订阅** 中的两个资源组，请使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

若要将两个存储帐户部署到 **两个订阅**，请使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="create-resource-group"></a>创建资源组

从资源组部署中，可以切换到订阅级别并创建资源组。 以下模板将存储帐户部署到目标资源组，并在指定的订阅中创建新的资源组。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "newResourceGroupName": {
            "type": "string"
        },
        "nestedSubscriptionID": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "demoSubDeployment",
            "location": "westus",
            "subscriptionId": "[parameters('nestedSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-06-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

* 若要通过示例来了解如何为 Azure 安全中心部署工作区设置，请参阅 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
