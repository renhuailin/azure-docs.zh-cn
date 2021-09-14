---
title: 在 Azure PowerShell 中导出模板
description: 使用 Azure PowerShell 从订阅中的资源导出 Azure 资源管理器模板。
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: 15a81b845811f870be783807cf680c724413bf6c
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479143"
---
# <a name="use-azure-powershell-to-export-a-template"></a>使用 Azure PowerShell 导出模板

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

本文介绍如何通过 Azure PowerShell 导出模板。 有关其他选项，请参阅：

* [使用 Azure CLI 导出模板](export-template-cli.md)
* [使用 Azure 门户导出模板](export-template-portal.md)
* [从资源组导出 REST API](/rest/api/resources/resourcegroups/exporttemplate) 和[从部署历史记录导出 REST API](/rest/api/resources/deployments/export-template)。

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>从资源组导出模板

设置资源组后，可以导出资源组的 Azure 资源管理器模板。 

若要导出资源组中的所有资源，请使用 [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) cmdlet 并提供资源组名称。

```azurepowershell-interactive
Export-AzResourceGroup -ResourceGroupName demoGroup
```

它将模板另存为本地文件。

可以选择要导出的资源，而不是导出资源组中的所有资源。

若要导出一个资源，请传递该资源 ID。

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

若要导出多个资源，请在数组中传递资源 ID。

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

导出模板时，可以指定是否在模板中使用参数。 默认情况下，包含资源名称的参数，但它们没有默认值。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

如果在导出模板时使用 `-SkipResourceNameParameterization` 参数，则模板中不包括资源名称的参数。 而是，资源名称直接在资源上设置为其当前值。 无法在部署过程中自定义该名称。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

如果在导出模板时使用 `-IncludeParameterDefaultValue` 参数，则模板参数包括设置为当前值的默认值。 可以使用该默认值，也可以通过传入不同的值来覆盖默认值。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

## <a name="save-template-from-deployment-history"></a>从部署历史记录保存模板

可以将部署中的模板保存到部署历史记录中。 获取的模板正是用于部署的模板。 

若要从资源组部署获取模板，请使用 [Save-AzResourceGroupDeploymentTemplate](/powershell/module/az.resources/save-azresourcegroupdeploymenttemplate) cmdlet。 指定要检索的部署的名称。 有关获取部署名称的帮助，请参阅[使用 Azure 资源管理器查看部署历史记录](deployment-history.md)。

```azurepowershell-interactive
Save-AzResourceGroupDeploymentTemplate -ResourceGroupName demoGroup -DeploymentName demoDeployment
```

模板使用部署的名称保存为本地文件。

若要在其他级别部署模板，请使用：

* 用于订阅的部署的 [Save-AzDeploymentTemplate](/powershell/module/az.resources/save-azdeploymenttemplate)
* 用于管理组的部署的 [Save-AzManagementGroupDeploymentTemplate](/powershell/module/az.resources/save-azmanagementgroupdeploymenttemplate)
* 用于租户的部署的 [Save-AzTenantDeploymentTemplate](/powershell/module/az.resources/save-aztenantdeploymenttemplate)

## <a name="next-steps"></a>后续步骤

- 了解如何通过 [Azure CLI](export-template-cli.md)、[Azure 门户](export-template-portal.md)或 [REST API](/rest/api/resources/resourcegroups/exporttemplate) 导出模板。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](./syntax.md)。
- 若要了解如何开发模板，请参阅[分步教程](../index.yml)。