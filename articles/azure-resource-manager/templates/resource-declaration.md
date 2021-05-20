---
title: 在模板中声明资源
description: 介绍如何在 Azure 资源管理器模板（ARM 模板）中声明资源以进行部署。
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101744461"
---
# <a name="resource-declaration-in-arm-templates"></a>ARM 模板中的资源声明

若要通过 Azure 资源管理器模板（ARM 模板）部署资源，请添加资源声明。 为 JSON 模板使用 `resources` 数组，或为 Bicep 使用 `resource` 关键字。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>设置资源类型和版本

将资源添加到模板时，请首先设置资源类型和 API 版本。 这些值决定了可用于资源的其他属性。

下面的示例演示了如何为存储帐户设置资源类型和 API 版本。 示例不显示完整的资源声明。

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

对于 Bicep，请设置资源的符号名称。 在前面的示例中，符号名称为 `sa`。 可以使用任何值作为符号名称，但它不能与模板中的其他资源、参数或变量相同。 符号名称与资源名称不同。 使用符号名称可以轻松地引用模板其他部分中的资源。

## <a name="set-resource-name"></a>设置资源名称

每个资源都有一个名称。 设置资源名称时，请注意[资源名称的规则和限制](../management/resource-name-rules.md)。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>设置位置

许多资源需要一个位置。 可以通过 intellisense 或[模板引用](/azure/templates/)确定资源是否需要位置。 以下示例添加用于存储帐户的位置参数。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

有关详细信息，请参阅[在 ARM 模板中设置资源位置](resource-location.md)。

## <a name="set-tags"></a>设置标记

可以在部署期间对资源应用标记。 可以通过标记对部署的资源进行逻辑组织。 有关指定标记的不同方法的示例，请参阅 [ARM 模板标记](../management/tag-resources.md#arm-templates)。

## <a name="set-resource-specific-properties"></a>设置特定于资源的属性

上述属性对于大多数资源类型都是通用的。 设置这些值后，需要设置特定于所部署的资源类型的属性。

使用 intellisense 或[模板参考](/azure/templates/)确定哪些属性可用以及哪些属性是必需的。 下面的示例将为存储帐户设置其余属性。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

## <a name="next-steps"></a>后续步骤

* 若要有条件地部署资源，请参阅 [ARM 模板中的条件部署](conditional-resource-deployment.md)。
* 若要设置资源依赖项，请参阅[在 ARM 模板中定义部署资源的顺序](define-resource-dependency.md)。
