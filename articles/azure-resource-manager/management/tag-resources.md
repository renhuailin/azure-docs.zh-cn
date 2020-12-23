---
title: 标记资源、资源组和订阅以便对其进行逻辑组织
description: 演示如何应用标记来组织 Azure 资源进行计费和管理。
ms.topic: conceptual
ms.date: 12/03/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e47d3acf15ce5e4f5cb70444419b76beb21ae98b
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558141"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>使用标记对 Azure 资源和管理层次结构进行组织

可将标记应用到 Azure 资源、资源组和订阅，以便有条理地将它们组织成分类。 每个标记均由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

有关如何实现标记策略的建议，请参阅[资源命名和标记决策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)。

> [!IMPORTANT]
> 标记名称对于操作不区分大小写。 系统会更新或检索具有标记名称的标记，而不考虑大小写。 但是，资源提供程序可能会保留为标记名称提供的大小写。 你将在成本报表中看到该大小写。
> 
> 标记值区分大小写。

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>所需访问权限

可以通过两种方式获取对标记资源的所需访问权限。

- 你可以对 " **Microsoft .resources/标记** " 资源类型具有写访问权限。 此访问权限允许您标记任何资源，即使您无权访问资源本身。 [标记参与者](../../role-based-access-control/built-in-roles.md#tag-contributor)角色授予此访问权限。 目前，标记参与者角色无法通过门户将标记应用到资源或资源组。 该角色可以通过门户将标记应用到订阅。 它支持通过 PowerShell 和 REST API 执行所有标记操作。  

- 您可以对资源本身具有写入访问权限。 [参与者](../../role-based-access-control/built-in-roles.md#contributor)角色授予所需的访问权限，以便将标记应用到任何实体。 要将标记仅应用于一种资源类型，请使用该资源的参与者角色。 例如，要将标记应用到虚拟机，请使用[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)。

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>应用标记

Azure PowerShell 提供两个用于应用标记的命令 - [New-AzTag](/powershell/module/az.resources/new-aztag) 和 [Update-AzTag](/powershell/module/az.resources/update-aztag)。 必须安装 Az.Resources 模块 1.12.0 或更高版本。 可以使用 `Get-Module Az.Resources` 检查自己的版本。 可以安装该模块，或[安装 Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 或更高版本。

New-AzTag 替换资源、资源组或订阅中的所有标记。 调用该命令时，请传入要标记的实体的资源 ID。

以下示例将一组标记应用到存储帐户：

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

该命令完成后，会发现该资源带有两个标记。

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

如果再次运行该命令（但这一次使用不同的标记），则会发现以前的标记已删除。

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

若要将标记添加到已经有标记的资源，请使用 Update-AzTag。 将 -Operation 参数设置为 Merge 。

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

你会发现，已将两个新标记添加到了两个现有标记中。

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

每个标记名称只能有一个值。 如果为标记提供新值，则即使使用合并操作，也会替换旧值。 以下示例将 Status 标记从 Normal 更改为 Green。

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

将 -Operation 参数设置为 Replace 时，会将现有标记替换为新的标记集 。

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

资源中只会保留新标记。

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

相同的命令也适用于资源组或订阅。 传入要标记的资源组或订阅的标识符。

若要将一组新标记添加到资源组，请使用：

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

若要更新资源组的标记，请使用：

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

若要将一组新标记添加到订阅，请使用：

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

若要更新订阅的标记，请使用：

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

一个资源组中可能有多个同名资源。 在这种情况下，可以通过以下命令设置每个资源：

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>列出标记

若要获取资源、资源组或订阅的标记，请使用 [Get-AzTag](/powershell/module/az.resources/get-aztag) 命令并传入实体的资源 ID。

若要查看资源的标记，请使用：

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

若要查看资源组的标记，请使用：

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

若要查看订阅的标记，请使用：

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>按标记列出

若要获取具有特定标记名称和值的资源，请使用：

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

若要获取具有特定标记名称和任意标记值的资源，请使用：

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

若要获取具有特定标记名称和值的资源组，请使用：

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>删除标记

若要删除特定的标记，请使用 Update-AzTag 并将 -Operation 设置为 Delete  。 传入要删除的标记。

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

指定的标记随即被删除。

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

若要删除所有标记，请使用 [Remove-AzTag](/powershell/module/az.resources/remove-aztag) 命令。

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>应用标记

Azure CLI 提供了两个命令用于应用标记- [az tag create](/cli/azure/tag#az_tag_create) 和 [az tag update](/cli/azure/tag#az_tag_update)。 您必须具有 Azure CLI 2.10.0 或更高版本。 可以使用 `az version` 检查自己的版本。 若要更新或安装，请参阅 [安装 Azure CLI](/cli/azure/install-azure-cli)。

**Az 标记 create** 将替换资源、资源组或订阅上的所有标记。 调用该命令时，请传入要标记的实体的资源 ID。

以下示例将一组标记应用到存储帐户：

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag create --resource-id $resource --tags Dept=Finance Status=Normal
```

该命令完成后，会发现该资源带有两个标记。

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Status": "Normal"
  }
},
```

如果再次运行该命令（但这一次使用不同的标记），则会发现以前的标记已删除。

```azurecli-interactive
az tag create --resource-id $resource --tags Team=Compliance Environment=Production
```

```output
"properties": {
  "tags": {
    "Environment": "Production",
    "Team": "Compliance"
  }
},
```

若要将标记添加到已包含标记的资源，请使用 `az tag update` 。 将 `--operation` 参数设置为 `Merge`。

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Dept=Finance Status=Normal
```

你会发现，已将两个新标记添加到了两个现有标记中。

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Normal",
    "Team": "Compliance"
  }
},
```

每个标记名称只能有一个值。 如果为标记提供新值，则即使使用合并操作，也会替换旧值。 以下示例将 Status 标记从 Normal 更改为 Green。

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Status=Green
```

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Green",
    "Team": "Compliance"
  }
},
```

将 `--operation` 参数设置为时 `Replace` ，现有标记将替换为新的标记集。

```azurecli-interactive
az tag update --resource-id $resource --operation Replace --tags Project=ECommerce CostCenter=00123 Team=Web
```

资源中只会保留新标记。

```output
"properties": {
  "tags": {
    "CostCenter": "00123",
    "Project": "ECommerce",
    "Team": "Web"
  }
},
```

相同的命令也适用于资源组或订阅。 传入要标记的资源组或订阅的标识符。

若要将一组新标记添加到资源组，请使用：

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag create --resource-id $group --tags Dept=Finance Status=Normal
```

若要更新资源组的标记，请使用：

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags CostCenter=00123 Environment=Production
```

若要将一组新标记添加到订阅，请使用：

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag create --resource-id /subscriptions/$sub --tags CostCenter=00123 Environment=Dev
```

若要更新订阅的标记，请使用：

```azurecli-interactive
az tag update --resource-id /subscriptions/$sub --operation Merge --tags Team="Web Apps"
```

### <a name="list-tags"></a>列出标记

若要获取资源、资源组或订阅的标记，请使用 [az tag list](/cli/azure/tag#az_tag_list) 命令并传入实体的资源 ID。

若要查看资源的标记，请使用：

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag list --resource-id $resource
```

若要查看资源组的标记，请使用：

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag list --resource-id $group
```

若要查看订阅的标记，请使用：

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag list --resource-id /subscriptions/$sub
```

### <a name="list-by-tag"></a>按标记列出

若要获取具有特定标记名称和值的资源，请使用：

```azurecli-interactive
az resource list --tag CostCenter=00123 --query [].name
```

若要获取具有特定标记名称和任意标记值的资源，请使用：

```azurecli-interactive
az resource list --tag Team --query [].name
```

若要获取具有特定标记名称和值的资源组，请使用：

```azurecli-interactive
az group list --tag Dept=Finance
```

### <a name="remove-tags"></a>删除标记

若要删除特定标记，请使用 `az tag update` 并将设置 `--operation` 为 `Delete` 。 传入要删除的标记。

```azurecli-interactive
az tag update --resource-id $resource --operation Delete --tags Project=ECommerce Team=Web
```

指定的标记随即被删除。

```output
"properties": {
  "tags": {
    "CostCenter": "00123"
  }
},
```

若要删除所有标记，请使用 [az tag delete](/cli/azure/tag#az_tag_delete) 命令。

```azurecli-interactive
az tag delete --resource-id $resource
```

### <a name="handling-spaces"></a>处理空格

如果标记名称或值包含空格，请将它们括在双引号中。

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags "Cost Center"=Finance-1222 Location="West US"
```

## <a name="templates"></a>模板

可以在使用资源管理器模板进行部署期间标记资源、资源组和订阅。

### <a name="apply-values"></a>应用值

以下示例部署具有三个标记的存储帐户。 两个标记（`Dept` 和 `Environment`）设置为文本值。 一个标记 (`LastDeployed`) 设置为默认值为当前日期的参数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>应用对象

可以定义一个存储多个标记的对象参数，并将该对象应用于标记元素。 此方法比上一个示例更灵活，因为对象可以使用不同的属性。 对象中的每个属性会成为资源的单独标记。 以下示例有一个名为 `tagValues` 的参数，该标记应用于标记元素。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>应用 JSON 字符串

要将多个值存储在单个标记中，请应用表示这些值的 JSON 字符串。 整个 JSON 字符串存储为一个标记，该标记不能超过 256 个字符。 以下示例有一个名为 `CostCenter` 的标记，其中包含 JSON 字符串中的多个值：  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>应用资源组中的标记

若要将资源组中的标记应用于资源，请使用 [resourceGroup()](../templates/template-functions-resource.md#resourcegroup) 函数。 获取标记值时，请使用 `tags[tag-name]` 语法而不是 `tags.tag-name` 语法，因为有些字符在点表示法中无法正确解析。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>将标记应用到多个资源组或订阅

可以通过部署 Microsoft.Resources/tags 资源类型，将标记添加到资源组或订阅。 标记会应用到此部署的目标资源组或订阅。 每次部署模板时，都要替换以前应用的任何标记。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

若要将标记应用到资源组，请使用 PowerShell 或 Azure CLI。 部署到要标记的资源组。

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

若要将标记应用到订阅，请使用 PowerShell 或 Azure CLI。 部署到要标记的订阅。

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

有关订阅部署的详细信息，请参阅[在订阅级别创建资源组和资源](../templates/deploy-to-subscription.md)。

以下模板将对象中的标记添加到资源组或订阅。

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>门户

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

若要通过 Azure REST API 处理标记，请使用：

* [标记 - 在范围内创建或更新](/rest/api/resources/tags/createorupdateatscope)（PUT 操作）
* [标记 - 在范围内更新](/rest/api/resources/tags/updateatscope)（PATCH 操作）
* [标记 - 在范围内获取](/rest/api/resources/tags/getatscope)（GET 操作）
* [标记 - 在范围内删除](/rest/api/resources/tags/deleteatscope)（DELETE 操作）

## <a name="inherit-tags"></a>继承标记

应用到资源组或订阅的标记不会由资源继承。 若要将订阅或资源组中的标记应用到资源，请参阅 [Azure 策略 - 标记](tag-policies.md)。

## <a name="tags-and-billing"></a>标记和计费

可使用标记对计费数据进行分组。 例如，如果针对不同组织运行多个 VM，可以使用标记根据成本中心对使用情况进行分组。 还可使用标记根据运行时环境对成本进行分类；例如，在生产环境中运行的虚拟机的计费使用情况。

可以通过 [Azure 资源使用情况和费率卡 api](../../cost-management-billing/manage/usage-rate-card-overview.md) 或使用逗号分隔值 (CSV) 文件来检索有关标记的信息。 从 Azure 门户下载使用情况文件。 有关详细信息，请参阅[下载或查看 Azure 帐单发票和每日使用数据](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)。 从 Azure 帐户中心下载使用情况文件时，选择 **版本 2**。 对于支持为账单提供标记的服务，标记会显示在“标记”列中。

有关 REST API 操作，请参阅 [Azure 计费 REST API 参考](/rest/api/billing/)。

## <a name="limitations"></a>限制

以下限制适用于标记：

* 并非所有资源类型都支持标记。 若要确定是否可以将标记应用到资源类型，请参阅 [Azure 资源的标记支持](tag-support.md)。
* 每个资源、资源组和订阅最多可以有 50 个标记名称/值对。 如果需要应用的标记超过最大允许数量，请使用 JSON 字符串作为标记值。 JSON 字符串可以包含多个应用于单个标记名称的值。 一个资源组或订阅可以包含多个资源，这些资源每个都有 50 个标记名称/值对。
* 标记名称不能超过 512 个字符，标记值不能超过 256 个字符。 对于存储帐户，标记名称不能超过 128 个字符，标记值不能超过 256 个字符。
* 不能将标记应用到云服务等经典资源。
* 标记名称不能包含以下字符：`<`、`>`、`%`、`&`、`\`、`?`、`/`

   > [!NOTE]
   > 目前，Azure DNS 区域和流量管理器服务也不允许在标记中使用空格。
   >
   > Azure Front Door 不支持在标记名称中使用 `#`。
   >
   > Azure 自动化和 Azure CDN 仅支持资源上的 15 个标记。

## <a name="next-steps"></a>后续步骤

* 并非所有资源类型都支持标记。 若要确定是否可以将标记应用到资源类型，请参阅 [Azure 资源的标记支持](tag-support.md)。
* 有关如何实现标记策略的建议，请参阅[资源命名和标记决策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)。
