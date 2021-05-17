---
title: 模板中的数据类型
description: 介绍 Azure 资源管理器模板中可用的数据类型。
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125053"
---
# <a name="data-types-in-arm-templates"></a>ARM 模板中的数据类型

本文介绍 Azure 资源管理器模板（ARM 模板）中支持的数据类型。 本文涵盖 JSON 和 Bicep 数据类型。

## <a name="supported-types"></a>支持的类型

在 ARM 模板中，可以使用以下数据类型：

* array
* bool
* int
* object
* secureObject - Bicep 中的修饰符指示
* secureString - Bicep 中的修饰符指示
* string

## <a name="arrays"></a>数组

数组以左中括号 (`[`) 开头，以右中括号 (`]`) 结尾。

在 JSON 中，数组可以在一行或多行中声明。 每个元素用逗号隔开。

在 Bicep 中，一个数组必须在多行中声明。 不要在值之间使用逗号。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

数组的元素可以是同一类型，也可以是不同类型。

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>布尔型

指定布尔值时，请使用 `true` 或 `false`。 不要将值用引号括起。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>整数

指定整数值时，请勿使用引号。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

对于作为内联参数传递的整数，值的范围可能受限于用于部署的 SDK 或命令行工具。 例如，使用 PowerShell 部署模板时，整数类型的范围可能为 -2147483648 到 2147483647。 为了避免此限制，请在[参数文件](parameter-files.md)中指定大的整数值。 资源类型会针对整数属性应用其自己的限制。

## <a name="objects"></a>对象

对象以左大括号 (`{`) 开头，以右大括号 (`}`) 结尾。 对象中的每个属性都包含键和值。 键和值用冒号 (`:`) 分隔。

在 JSON 中，键用双引号括起。 每个属性用逗号隔开。

在 Bicep 中，键不能用引号括起来。 不要在属性之间使用逗号。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>字符串

在 JSON 中，字符串用双引号进行标记。 在 Bicep 中，字符串用单引号进行标记。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>安全字符串和对象

安全字符串使用与字符串相同的格式，安全对象使用与对象相同的格式。 将参数设置为安全字符串或安全对象时，参数的值不会保存到部署历史记录中，也不会记入日志。 但是，如果将该安全值设置为不应为安全值的属性，则该值不会受到保护。 例如，如果将安全字符串设置为标记，则该值将以纯文本的形式存储。 使用安全字符串作为密码和机密。

使用 Bicep，可将 `@secure()` 修饰符添加到字符串或对象。

以下示例显示了两个安全参数：

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>后续步骤

要了解模板语法，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
