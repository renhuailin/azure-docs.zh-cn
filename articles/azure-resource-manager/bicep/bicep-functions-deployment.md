---
title: Bicep 函数 - 部署
description: 介绍 Bicep 文件中用于检索部署信息的函数。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 53e9f34e2d04f68add7babd8c12b4fd583015847
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111025968"
---
# <a name="deployment-functions-for-bicep"></a>Bicep 的部署函数

资源管理器提供以下函数，用于获取与 Bicep 文件的当前部署相关的值：

* [部署](#deployment)
* [环境](#environment)

若要从资源、资源组或订阅获取值，请参阅 [Resource functions](./bicep-functions-resource.md)（资源函数）。

## <a name="deployment"></a>部署

`deployment()`

返回有关当前部署操作的信息。

### <a name="return-value"></a>返回值

此函数返回部署期间传递的对象。 返回的对象中的属性因以下情况而异：

* 部署本地 Bicep 文件。
* 部署到资源组还是其他范围之一（[Azure 订阅](deploy-to-subscription.md)、[管理组](deploy-to-management-group.md)或[租户](deploy-to-tenant.md)）。

将本地 Bicep 文件部署到资源组时，该函数返回以下格式：

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

当部署到 Azure 订阅、管理组或租户时，返回对象包含 `location` 属性。 在部署本地 Bicep 文件时包含 location 属性。 格式为：

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

### <a name="example"></a>示例

下面的示例返回部署对象：

```bicep
output deploymentOutput object = deployment()
```

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

以下示例 Bicep 文件返回环境对象。

```bicep
output environmentOutput object = environment()
```

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

## <a name="next-steps"></a>后续步骤

* 有关 Bicep 文件中各部分的说明，请参阅[了解 Bicep 文件的结构和语法](./file.md)。
