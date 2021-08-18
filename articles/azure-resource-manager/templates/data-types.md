---
title: 模板中的数据类型
description: 介绍 Azure 资源管理器模板中可用的数据类型。
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 06/24/2021
ms.openlocfilehash: 4fc69126ee1f555e71e152a7c0369e4b81b8bf6a
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894223"
---
# <a name="data-types-in-arm-templates"></a>ARM 模板中的数据类型

本文介绍 Azure 资源管理器模板（ARM 模板）中支持的数据类型。

## <a name="supported-types"></a>支持的类型

在 ARM 模板中，可以使用以下数据类型：

* array
* bool
* int
* object
* secureObject
* secureString
* string

## <a name="arrays"></a>数组

数组以左中括号 (`[`) 开头，以右中括号 (`]`) 结尾。 数组可以在一行或多行中声明。 每个元素用逗号隔开。

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

"outputs": {
  "arrayOutput": {
    "type": "array",
    "value": "[variables('exampleArray')]"
  },
  "firstExampleArrayElement": {
    "type": "int",
    "value": "[parameters('exampleArray')[0]]"
  }
}
```

数组的元素可以是同一类型，也可以是不同类型。

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}

"outputs": {
  "arrayOutput": {
    "type": "array",
    "value": "[variables('mixedArray')]"
  },
  "firstMixedArrayElement": {
    "type": "string",
    "value": "[variables('mixedArray')[0]]"
  }
}
```

## <a name="booleans"></a>布尔型

指定布尔值时，请使用 `true` 或 `false`。 不要将值用引号括起。

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

## <a name="integers"></a>整数

指定整数值时，请勿使用引号。

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

对于作为内联参数传递的整数，值的范围可能受限于用于部署的 SDK 或命令行工具。 例如，使用 PowerShell 部署模板时，整数类型的范围可能为 -2147483648 到 2147483647。 为了避免此限制，请在[参数文件](parameter-files.md)中指定大的整数值。 资源类型会针对整数属性应用其自己的限制。

## <a name="objects"></a>对象

对象以左大括号 (`{`) 开头，以右大括号 (`}`) 结尾。 对象中的每个属性都包含 `key` 和 `value`。 `key` 和 `value` 用双引号括起来并由冒号 (`:`) 分隔。 每个属性用逗号隔开。

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

## <a name="strings"></a>字符串

字符串用双引号进行标记。

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

## <a name="secure-strings-and-objects"></a>安全字符串和对象

安全字符串使用与字符串相同的格式，安全对象使用与对象相同的格式。 将参数设置为安全字符串或安全对象时，参数的值不会保存到部署历史记录中，也不会记入日志。 但是，如果将该安全值设置为不应为安全值的属性，则该值不会受到保护。 例如，如果将安全字符串设置为标记，则该值将以纯文本的形式存储。 使用安全字符串作为密码和机密。

以下示例显示了两个安全参数。

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

## <a name="next-steps"></a>后续步骤

要了解模板语法，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。