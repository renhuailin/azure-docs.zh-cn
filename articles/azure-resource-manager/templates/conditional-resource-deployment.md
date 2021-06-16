---
title: 使用模板进行条件部署
description: 介绍如何在 Azure 资源管理器模板（ARM 模板）中有条件地部署资源。
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: 352ee71fea77608ae27552630a7d302b215374a1
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969495"
---
# <a name="conditional-deployment-in-arm-templates"></a>使用 ARM 模板进行条件部署

有时，需要选择在 Azure 资源管理器模板（ARM 模板）中部署资源。 使用 `condition` 元素指定是否部署资源。 条件的值解析为 true 或 false。 如果值为 true，则创建了该资源。 如果值为 false，则未创建该资源。 值只能应用到整个资源。

> [!NOTE]
> 条件部署不会级联到[子资源](child-resource-name-type.md)。 如果要有条件地部署资源及其子资源，需要对每种资源类型应用相同的条件。

## <a name="deploy-condition"></a>部署条件

你可以传入一个指示是否部署资源的参数值。 以下示例按条件部署 DNS 区域。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

如需查看更复杂的示例，请参阅 [Azure SQL 逻辑服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-logical-server)。

## <a name="new-or-existing-resource"></a>新资源或现有资源

可以使用条件部署来创建新资源或使用现有资源。 以下示例演示如何部署新的存储帐户或使用现有存储帐户。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

当参数 `newOrExisting` 设置为 **new** 时，条件的计算结果为 true。 将部署存储帐户。 但是，当 `newOrExisting` 设置为 **existing** 时，条件的计算结果为 false，并且不部署存储帐户。

有关使用 `condition` 元素的完整示例模板，请参阅[具有新的或现有虚拟网络、存储和公共 IP 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-new-or-existing-conditions)。

## <a name="runtime-functions"></a>运行时函数

如果对条件性部署的资源使用 [reference](template-functions-resource.md#reference) 或 [list](template-functions-resource.md#list) 函数，则会对该函数进行评估，即使资源尚未部署。 如果该函数引用某个不存在的资源，则会出现错误。

请使用 [if](template-functions-logical.md#if) 函数，以确保仅当资源已部署时，才根据条件评估函数。 请查看示例模板的 [if 函数](template-functions-logical.md#if)，该模板将 `if` 和 `reference` 用于进行条件部署的资源。

[将资源设置为依赖于](./resource-dependency.md)条件资源，这与设置任何其他资源完全一样。 条件资源未部署时，Azure 资源管理器会自动将其从所需依赖项中删除。

## <a name="complete-mode"></a>完整模式

如果以[完整模式](deployment-modes.md)部署模板并且由于 `condition` 的计算结果为 false 而未部署资源，则结果取决于用于部署模板的 REST API 版本。 如果使用 2019-05-10 之前的版本，则 **不会删除** 该资源。 如果使用 2019-05-10 或更高版本，则 **会删除** 该资源。 最新版本的 Azure PowerShell 和 Azure CLI 在条件为 false 时会删除该资源。

## <a name="next-steps"></a>后续步骤

* 有关介绍条件部署的 Microsoft Learn 模块，请参阅[使用高级 ARM 模板功能管理复杂云部署](/learn/modules/manage-deployments-advanced-arm-template-features/)。
* 有关创建模板的建议，请参阅 [ARM 模板的最佳做法](./best-practices.md)。
* 要创建资源的多个实例，请参阅 [ARM 模板中的资源迭代](copy-resources.md)。