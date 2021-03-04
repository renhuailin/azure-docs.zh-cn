---
title: 模板中的数据类型
description: 介绍 Azure 资源管理器模板中可用的数据类型。
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125053"
---
# <a name="data-types-in-arm-templates"></a>ARM 模板中的数据类型

本文介绍 Azure 资源管理器模板中支持的数据类型 (ARM 模板) 。 它涵盖 JSON 和 Bicep 数据类型。

## <a name="supported-types"></a>支持的类型

在 ARM 模板中，可以使用以下数据类型：

* array
* bool
* int
* 对象
* secureObject-Bicep 中的修饰符指示
* secureString-Bicep 中的修饰符指示
* 字符串

## <a name="arrays"></a>数组

数组以左中括号 (`[`) 开头，以右中括号 (`]`) 结尾。

在 JSON 中，数组可以在一行或多个行中声明。 每个元素都由逗号分隔。

在 Bicep 中，必须在多个行中声明一个数组。 不要在值之间使用逗号。

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

数组的元素可以是同一类型，也可以是不同的类型。

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

指定布尔值时，请使用 `true` 或 `false` 。 不要将值括在引号中。

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

指定整数值时，请不要使用引号。

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

对象以左大括号 (`{`) 开头，以右大括号 (`}`) 结尾。 对象中的每个属性都包含键和值。 键和值用冒号分隔 (`:`) 。

在 JSON 中，该键用双引号引起来。 每个属性都由逗号分隔。

在 Bicep 中，该键不用引号括起来。 不要在属性之间使用逗号。

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

在 JSON 中，字符串用双引号进行标记。 在 Bicep 中，字符串标有单引号。

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

下面的示例演示两个安全参数：

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

若要了解有关模板语法的信息，请参阅 [了解 ARM 模板的结构和语法](template-syntax.md)。
