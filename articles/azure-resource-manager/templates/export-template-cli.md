---
title: 在 Azure CLI 中导出模板
description: 使用 Azure CLI 从订阅中的资源导出 Azure 资源管理器模板。
ms.topic: conceptual
ms.date: 09/03/2021
ms.openlocfilehash: b60c7ae3787b2b22b09eb844a9206b9530fb68df
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480869"
---
# <a name="use-azure-cli-to-export-a-template"></a>使用 Azure CLI 导出模板

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

本文介绍如何通过 Azure CLI 导出模板。 有关其他选项，请参阅：

* [使用 Azure 门户导出模板](export-template-portal.md)
* [使用 Azure PowerShell 导出模板](export-template-powershell.md)
* [从资源组导出 REST API](/rest/api/resources/resourcegroups/exporttemplate) 和[从部署历史记录导出 REST API](/rest/api/resources/deployments/export-template)。

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>从资源组导出模板

成功设置资源组后，可以导出资源组的 Azure 资源管理器模板。

若要导出资源组中的所有资源，请使用 [az group export](/cli/azure/group#az_group_export) 并提供资源组名称。

```azurecli-interactive
az group export --name demoGroup
```

该脚本在控制台上显示模板。 若要保存到文件，请使用：

```azurecli-interactive
az group export --name demoGroup > exportedtemplate.json
```

可以选择要导出的资源，而不是导出资源组中的所有资源。

若要导出一个资源，请传递该资源 ID。

```azurecli-interactive
storageAccountID=$(az resource show --resource-group demoGroup --name demostg --resource-type Microsoft.Storage/storageAccounts --query id --output tsv)
az group export --resource-group demoGroup --resource-ids $storageAccountID
```

若要导出多个资源，请传递以空格分隔的资源 ID。 若要导出所有资源，请不要指定此参数或提供“*”。

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccountID1 $storageAccountID2
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

如果在导出模板时使用 `--skip-resource-name-params` 参数，则模板中不包括资源名称的参数。 而是，资源名称直接在资源上设置为其当前值。 无法在部署过程中自定义该名称。

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

如果在导出模板时使用 `--include-parameter-default-value` 参数，则模板参数包括设置为当前值的默认值。 可以使用该默认值，也可以通过传入不同的值来覆盖默认值。

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

若要从资源组部署获取模板，请使用 [az deployment group export](/cli/azure/deployment/group#az_deployment_group_export) 命令。 指定要检索的部署的名称。 有关获取部署名称的帮助，请参阅[使用 Azure 资源管理器查看部署历史记录](deployment-history.md)。

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment 
```

模板将显示在控制台中。 若要保存文件，请使用：

```azurecli-interactive
az deployment group export --resource-group demoGroup --name demoDeployment > demoDeployment.json
```

若要在其他级别部署模板，请使用：

* [Save-AzDeploymentTemplate](/cli/azure/deployment/sub#az_deployment_sub_export)（用于向订阅的部署）
* [az deployment mg export](/cli/azure/deployment/mg#az_deployment_mg_export)（用于向管理组的部署）
* [az deployment tenant export](/cli/azure/deployment/tenant#az_deployment_tenant_export)（用于向租户的部署）


## <a name="next-steps"></a>后续步骤

- 了解如何通过 [Azure 门户](export-template-portal.md)、[Azure PowerShell](export-template-powershell.md)或 [REST API](/rest/api/resources/resourcegroups/exporttemplate) 导出模板。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](./syntax.md)。
- 若要了解如何开发模板，请参阅[分步教程](../index.yml)。