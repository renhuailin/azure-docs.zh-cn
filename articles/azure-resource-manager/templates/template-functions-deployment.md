---
title: 模板函数 - 部署
description: 介绍可在 Azure 资源管理器模板（ARM 模板）中使用的用于检索部署信息的函数。
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 12f449cd70f0ccbeeb0d232299f38fb814a8cb24
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124733331"
---
# <a name="deployment-functions-for-arm-templates"></a>ARM 模板的部署函数

资源管理器提供了以下函数，用于获取与 Azure 资源管理器模板（ARM 模板）的当前部署相关的值：

* [部署](#deployment)
* [环境](#environment)
* [parameters](#parameters)
* [variables](#variables)

若要从资源、资源组或订阅获取值，请参阅 [Resource functions](template-functions-resource.md)（资源函数）。

## <a name="deployment"></a>部署

`deployment()`

返回有关当前部署操作的信息。

### <a name="return-value"></a>返回值

此函数返回部署期间传递的对象。 返回的对象中的属性因以下情况而异：

* 部署模板或模板规格。
* 你部署的模板是本地文件，还是通过 URI 访问的远程文件。
* 部署到资源组还是其他范围之一（[Azure 订阅](deploy-to-subscription.md)、[管理组](deploy-to-management-group.md)或[租户](deploy-to-tenant.md)）。

将本地模板部署到资源组时，该函数返回以下格式：

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

将远程模板部署到资源组时，该函数返回以下格式：

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "uri": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

将模板规格部署到资源组时：该函数返回以下格式：

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "id": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

当部署到 Azure 订阅、管理组或租户时，返回对象包含 `location` 属性。 部署本地模板或外部模板时包含 location 属性。 格式为：

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="remarks"></a>备注

可以根据父模板的 URI，使用 `deployment()` 链接到另一个模板。

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

如果从门户中的部署历史记录重新部署模板，则该模板将部署为本地文件。 部署函数不返回 `templateLink` 属性。 如果模板依赖于 `templateLink` 来构建指向另一个模板的链接，请不要使用门户进行重新部署， 而是使用最初部署模板时使用的命令。

### <a name="example"></a>示例

下面的示例返回部署对象。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/deployment.json":::

前面的示例返回以下对象：

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

对于订阅部署，以下示例返回部署对象。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/deploymentsubscription.json":::

## <a name="environment"></a>环境

`environment()`

返回有关用于部署的 Azure 环境的信息。

### <a name="return-value"></a>返回值

此函数返回当前 Azure 环境的属性。 以下示例显示了全局 Azure 的属性。 主权云可能会返回略有不同的属性。

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>示例

以下示例模板返回环境对象。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/environment.json":::

前面的示例在部署到全局 Azure 时返回以下对象：

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>参数

`parameters(parameterName)`

返回一个参数值。 指定的参数名称必须已在模板的 parameters 节中定义。

在 Bicep 中，通过使用参数的符号名称直接引用参数。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |string |要返回的参数名称。 |

### <a name="return-value"></a>返回值

指定的参数的值。

### <a name="remarks"></a>备注

通常，使用参数设置资源值。 以下示例将 Web 站点的名称设置为在部署过程中传递的参数值。

```json
"parameters": {
  "siteName": {
    "type": "string"
  }
}, "resources": [
  {
    "type": "Microsoft.Web/Sites",
    "apiVersion": "2016-08-01",
    "name": "[parameters('siteName')]",
    ...
  }
]
```

### <a name="example"></a>示例

以下示例演示了 parameters 函数的简化用法。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/parameters.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| stringOutput | String | 选项 1 |
| intOutput | int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | 选项 1 |

如需详细了解如何使用参数，请参阅 [ARM 模板中的参数](./parameters.md)。

## <a name="variables"></a>variables

`variables(variableName)`

返回变量的值。 指定的变量名称必须已在模板的 variables 节中定义。

在 Bicep 中，通过使用变量的符号名称直接引用变量。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| variableName |是 |String |要返回的变量名称。 |

### <a name="return-value"></a>返回值

指定的变量的值。

### <a name="remarks"></a>备注

通常，使用变量通过只构造一次复杂值来简化模板。 以下示例构造存储帐户的唯一名称。

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "dependsOn": [
      "[variables('storageName')]"
    ],
    ...
  }
],

```

### <a name="example"></a>示例

以下示例返回不同的变量值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/deployment/variables.json":::

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

如需详细了解如何使用变量，请参阅 [ARM 模板中的变量](./variables.md)。

## <a name="next-steps"></a>后续步骤

* 有关 ARM 模板中各部分的说明，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
