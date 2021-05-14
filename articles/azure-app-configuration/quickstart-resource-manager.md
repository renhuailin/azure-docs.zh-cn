---
title: 使用 Azure 资源管理器模板（ARM 模板）创建 Azure 应用程序配置存储
titleSuffix: Azure App Configuration
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure 应用程序配置存储。
author: GrantMeStrength
ms.author: jken
ms.date: 10/16/2020
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 2bdd989f79a8a24e47698e187bfc45559e873237
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108763966"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Azure 应用程序配置存储

本快速入门介绍如何：

- 使用 Azure 资源管理器模板（ARM 模板）部署应用程序配置存储。
- 使用 ARM 模板在应用程序配置存储中创建键值。
- 从 ARM 模板读取应用程序配置存储中的键值。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/)。 它创建新的应用程序配置存储，其中包含两个键值。 然后，它使用 `reference` 函数输出这两个键值资源的值。 通过这种方式读取键的值，就能在模板的其他位置使用键值。

本快速入门使用 `copy` 元素来创建多个键值资源实例。 若要详细了解 `copy` 元素，请参阅 [ARM 模板中的资源迭代](../azure-resource-manager/templates/copy-resources.md)。

> [!IMPORTANT]
> 此模板需要的应用程序配置资源提供程序版本为 `2020-07-01-preview` 或更高版本。 此版本使用 `reference` 函数来读取键值。 从 `2020-07-01-preview` 版本开始，将不再提供以前的版本中用于读取键值的 `listKeyValue` 函数。

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

该模板中定义了两个 Azure 资源：

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores)：创建应用程序配置存储。
- [Microsoft.AppConfiguration/configurationStores/keyValues](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores/keyvalues)：在应用程序配置存储中创建一个键值。

> [!TIP]
> `keyValues` 资源的名称是键和标签的组合。 键和标签由 `$` 分隔符联接。 标签是可选的。 在上面的示例中，名称为 `myKey` 的 `keyValues` 资源创建了一个不带标签的键值。
>
> 使用百分号编码（也称为 URL 编码），就能在键或标签中包含 ARM 模板资源名称中不允许使用的字符。 `%` 也不是允许使用的字符，因此在其位置使用了 `~`。 若要正确对名称编码，请按照以下步骤操作：
>
> 1. 应用 URL 编码
> 2. 将 `~` 替换为 `~7E`
> 3. 将 `%` 替换为 `~`
>
> 例如，若要创建键名为 `AppName:DbEndpoint` 且标签名为 `Test` 的键值对，资源名应为 `AppName~3ADbEndpoint$Test`。

> [!NOTE]
> 借助应用程序配置，可通过虚拟网络上的[专用链接](concept-private-endpoint.md)访问键值数据。 默认情况下，启用此功能后，通过公用网络对你的应用程序配置数据的所有请求都会被拒绝。 ARM 模板在你的虚拟网络外部运行，因此不允许从 ARM 模板访问数据。 若要在使用专用链接时允许从 ARM 模板访问数据，可使用以下 Azure CLI 命令启用公共网络访问。 在这种情况下，请务必考虑启用公用网络访问的安全隐患。
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="deploy-the-template"></a>部署模板

选择下图登录到 Azure 并打开一个模板。 该模板创建应用程序配置存储，其中包含两个键值。

[![“部署到 Azure”](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

你也可以使用以下 PowerShell cmdlet 部署模板。 键值将位于 PowerShell 控制台的输出中。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户搜索框中，键入“应用程序配置”。 从列表中选择“应用程序配置”。
1. 选择新创建的应用程序配置资源。
1. 在“操作”下，单击“配置资源管理器” 。
1. 验证有两个键值。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中所述的资源组、应用程序配置存储和所有相关资源，请将其删除。 如果将来还打算使用该应用程序配置存储，可以跳过删除它的步骤。 如果不打算继续使用该存储，请运行以下 cmdlet，删除本快速入门中创建的所有资源：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>后续步骤

若要了解如何将功能标志和 Key Vault 引用添加到应用程序配置存储，请查看以下 ARM 模板示例。

- [101-app-configuration-store-ff](https://azure.microsoft.com/resources/templates/101-app-configuration-store-ff/)
- [101-app-configuration-store-keyvaultref](https://azure.microsoft.com/resources/templates/101-app-configuration-store-keyvaultref/)
