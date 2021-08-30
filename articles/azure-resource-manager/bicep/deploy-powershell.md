---
title: 使用 PowerShell 和 Bicep 部署资源
description: 使用 Azure Resource Manager 和 Azure PowerShell 将资源部署到 Azure。 Bicep 文件中定义了资源。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 2b5540d3b45b77cd39e014605780b98b867498fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744903"
---
# <a name="deploy-resources-with-bicep-and-azure-powershell"></a>使用 Bicep 和 Azure PowerShell 部署资源

本文介绍如何将 Azure PowerShell 与 Bicep 文件搭配来将资源部署到 Azure。 如果不熟悉部署和管理 Azure 解决方案的概念，请参阅 [Bicep 概述](overview.md)。

若要部署 Bicep 文件，你需要 [Azure PowerShell 版本 5.6.0 或更高版本](/powershell/azure/install-az-ps)。

## <a name="prerequisites"></a>先决条件

需要一个 Bicep 文件进行部署。 本文使用的本地文件名为 C:\MyTemplates\azuredeploy.bicep。

需安装 Azure PowerShell 并连接到 Azure：

- **在本地计算机上安装 Azure PowerShell cmdlet。** 有关详细信息，请参阅 [Azure PowerShell 入门](/powershell/azure/get-started-azureps)。
- 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 连接到 Azure。 如果有多个 Azure 订阅，则可能还需要运行 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)。 有关详细信息，请参阅[使用多个 Azure 订阅](/powershell/azure/manage-subscriptions-azureps)。

如果没有安装 PowerShell，则可使用 Azure Cloud Shell。 有关详细信息，请参阅[从 Azure Cloud Shell 部署 Bicep 文件](./deploy-cloud-shell.md)。

## <a name="deployment-scope"></a>部署范围

可将部署目标设定为资源组、订阅、管理组或租户。 根据部署范围使用不同的命令。

- 若要部署到 **资源组**，请使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)：

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-bicep>
  ```

- 若要部署到订阅，请使用 [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment)（`New-AzDeployment` cmdlet 的别名）：

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-bicep>
  ```

  有关订阅级部署的详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

- 若要部署到管理组，请使用 [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment)。

  ```azurepowershell
  New-AzManagementGroupDeployment -ManagementGroupId <management-group-id> -Location <location> -TemplateFile <path-to-bicep>
  ```

  有关管理组级部署的详细信息，请参阅[在管理组级别创建资源](deploy-to-management-group.md)。

- 若要部署到租户，请使用 [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)。

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-bicep>
  ```

  有关租户级别部署的详细信息，请参阅[在租户级别创建资源](deploy-to-tenant.md)。

对于每一个范围，部署模板的用户必须具有创建资源所必需的权限。

## <a name="deploy-local-bicep-file"></a>部署本地 Bicep 文件

可以部署本地计算机中的 Bicep 文件，也可以部署存储在外部的模板。 本节介绍如何部署本地 Bicep 文件。

如果要部署到不存在的资源组，请创建该资源组。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 名称不能以句点结尾。

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

若要部署本地 Bicep 文件，请在部署命令中使用 `-TemplateFile` 参数。 

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-bicep>
```

部署可能需要几分钟时间才能完成。

## <a name="deploy-remote-bicep-file"></a>部署远程 Bicep 文件

目前，Azure PowerShell 不支持部署远程 Bicep 文件。 使用 [Bicep CLI](./install.md#development-environment) 将 Bicep 文件编译为 JSON 模板，然后将 JSON 文件加载到远程位置。

## <a name="parameters"></a>parameters

若要传递参数值，可以使用内联参数或参数文件。

### <a name="inline-parameters"></a>内联参数。

若要传递内联参数，请使用 `New-AzResourceGroupDeployment` 命令提供参数的名称。 例如，若要将字符串和数组传递给 Bicep 文件，请使用：

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

还可以获取文件的内容并将该内容作为内联参数提供。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

当需要提供配置值时，从文件中获取参数值非常有用。 例如，可以[为 Linux 虚拟机提供 cloud-init 值](../../virtual-machines/linux/using-cloud-init.md)。

如果需要传入对象数组，请在 PowerShell 中创建哈希表并将其添加到数组中。 在部署过程中将该数组作为参数传递。

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile <path-to-bicep> `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>参数文件

你可能会发现，与在脚本中以内联值的形式传递参数相比，使用包含参数值的 JSON 文件更为容易。 参数文件可以是本地文件，也可以是具有可访问 URI 的外部文件。 Bicep 文件使用 JSON 参数文件。

有关参数文件的详细信息，请参阅[创建资源管理器参数文件](./parameter-files.md)。

若要传递本地参数文件，请使用 `TemplateParameterFile` 参数：

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-bicep> `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

若要传递外部参数文件，请使用 `TemplateParameterUri` 参数：

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile <path-to-bicep> `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.parameters.json
```

## <a name="preview-changes"></a>预览更改

在部署 Bicep 文件之前，可以预览 Bicep 文件将对环境做出的更改。 使用[假设操作](./deploy-what-if.md)验证 Bicep 文件是否进行预期更改。 假设操作还验证 Bicep 文件是否有错误。

## <a name="deploy-template-specs"></a>部署模板规格

目前，Azure PowerShell 不支持通过提供 Bicep 文件来创建模板规格。 但是，可以使用 [Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) 资源创建 Bicep 文件来部署模板规格。下面是一个[示例](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep)。 还可使用 Bicep CLI 将 Bicep 文件生成到 ARM 模板 JSON 中，然后使用 JSON 模板创建模板规格。

## <a name="deployment-name"></a>部署名称

部署 Bicep 文件时，可以为部署指定名称。 此名称可以帮助你从部署历史记录中检索该部署。 如果没有为部署提供名称，将使用 Bicep 文件的名称。 例如，如果部署一个名为 `azuredeploy.bicep` 的 Bicep，但未指定部署名称，则该部署将命名为 `azuredeploy`。

每次运行部署时，一个包含部署名称的条目会添加到资源组的部署历史记录中。 如果运行另一个部署并为其指定了相同的名称，则会将先前的条目替换为当前部署。 如果要在部署历史记录中保持唯一条目，请为每个部署指定唯一名称。

若要创建唯一名称，你可以分配一个随机数。

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

或者，添加日期值。

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

如果使用相同的部署名称对同一资源组运行并发部署，则仅会完成最后一个部署。 尚未完成的具有相同名称的任何部署都将被最后一个部署所替换。 例如，如果你运行一个名为 `newStorage` 的部署，它部署了一个名为 `storage1` 的存储帐户；与此同时，你运行了另一个名为 `newStorage` 的部署，它部署了一个名为 `storage2` 的存储帐户，则你将仅部署一个存储帐户。 生成的存储帐户名为 `storage2`。

但是，如果你运行一个名为 `newStorage` 的部署，它部署了一个名为 `storage1` 的存储帐户；在该部署完成时你又立即运行了另一个名为 `newStorage` 的部署，它部署了一个名为 `storage2` 的存储帐户，则你将有两个存储帐户。 一个名为 `storage1`，另一个名为 `storage2`。 但是，部署历史记录中只有一个条目。

为每个部署指定唯一的名称时，可以并发运行它们而不会发生冲突。 如果你运行一个名为 `newStorage1` 的部署，它部署了一个名为 `storage1` 的存储帐户；与此同时，你又运行了另一个名为 `newStorage2` 的部署，它部署了一个名为 `storage2` 的存储帐户，则部署历史记录中将有两个存储帐户和两个条目。

为避免与并发部署冲突并确保部署历史记录中的条目是唯一的，请为每个部署指定唯一的名称。

## <a name="next-steps"></a>后续步骤

- 若要在出错时回退到成功的部署，请参阅[出错时回退到成功的部署](../templates/rollback-on-error.md)。
- 若要了解如何在文件中定义参数，请参阅[了解 Bicep 文件的结构和语法](file.md)。
- 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用 ARM 模板](../templates/secure-template-with-sas-token.md)。