---
title: Bicep 中的数据类型
description: 描述 Bicep 中可用的数据类型
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: f520e314aff783a78e1656c16721f0fb8504215b
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221687"
---
# <a name="data-types-in-bicep"></a>Bicep 中的数据类型

本文介绍 [Bicep](./overview.md) 中支持的数据类型。

## <a name="supported-types"></a>支持的类型

在 Bicep 中，可使用以下数据类型：

* array
* bool
* int
* object
* secureObject - Bicep 中的修饰符指示
* secureString - Bicep 中的修饰符指示
* string

## <a name="arrays"></a>数组

数组以左中括号 (`[`) 开头，以右中括号 (`]`) 结尾。 在 Bicep 中，一个数组必须在多行中声明。 不要在值之间使用逗号。

在数组中，每个项都由[任意类型](bicep-functions-any.md)表示。 可具有其每个项的数据类型都相同的数组，也可具有包含不同数据类型的数组。

下面的示例演示一个整数数组和一个不同类型的数组。

```bicep
var integerArray = [
  1
  2
  3
]

var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

Bicep 中的数组基于 0。 在下面的示例中，表达式 `exampleArray[0]` 的计算结果为 1，`exampleArray[2]` 的计算结果为 3。 索引器的索引本身可能是另一个表达式。 表达式 `exampleArray[index]` 的计算结果为 2。 只允许对数组类型的表达式使用整数索引器。

```bicep
var index = 1

var exampleArray = [
  1
  2
  3
]
```

## <a name="booleans"></a>布尔型

指定布尔值时，请使用 `true` 或 `false`。 不要将值用引号括起。

```bicep
param exampleBool bool = true
```

## <a name="integers"></a>整数

指定整数值时，请勿使用引号。

```bicep
param exampleInt int = 1
```

对于作为内联参数传递的整数，值的范围可能受限于用于部署的 SDK 或命令行工具。 例如，使用 PowerShell 部署 Bicep 时，整数类型的范围可能为 -2147483648 到 2147483647。 为了避免此限制，请在[参数文件](parameter-files.md)中指定大的整数值。 资源类型会针对整数属性应用其自己的限制。

目前不支持浮点、十进制或二进制格式。

## <a name="objects"></a>对象

对象以左大括号 (`{`) 开头，以右大括号 (`}`) 结尾。 在 Bicep 中，必须在多行中声明对象。 对象中的每个属性都包含键和值。 键和值用冒号 (`:`) 分隔。 对象允许任何类型的任何属性。

在 Bicep 中，键不能用引号括起来。 请不要在属性之间使用逗号。

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

属性访问器用于访问对象的属性。 它们是使用 `.` 运算符构造的。

```bicep
var a = {
  b: 'Dev'
  c: 42
  d: {
    e: true
  }
}

output result1 string = a.b // returns 'Dev' 
output result2 int = a.c // returns 42
output result3 bool = a.d.e // returns true
```

属性访问器可用于任何对象，包括对象类型和对象文字的参数和变量。 对非对象类型的表达式使用属性访问器是错误的。

还可以使用 `[]` 语法来访问属性。 下面的示例将返回 `Development`。

```bicep
var environmentSettings = {
  dev: {
    name: 'Development'
  }
  prod: {
    name: 'Production'
  }
}

output accessorResult string = environmentSettings['dev'].name
```

## <a name="strings"></a>字符串

在 Bicep 中，字符串用单引号标记，并且必须在单行中声明。 允许码位介于 0 和 10FFFF 之间的所有 Unicode 字符 。

```bicep
param exampleString string = 'test value'
```

下表列出了一组必须由反斜杠 (`\`) 字符转义的保留字符：

| 转义序列 | 表示的值 | 注释 |
|:-|:-|:-|
| \\ | \ ||
| \' | ' ||
| \n | 换行 (LF) ||
| \r | 匹配回车符 (CR) ||
| \t | 制表符 ||
| \u{x} | Unicode 码位 x | x 表示介于 0 和 10FFFF（包含这两个值）之间的十六进制码位  。 允许前导零。 FFFF 上方的码位作为代理项对发出。
| \$ | $ | 只有在后跟 { 时，才需要进行转义。 |

```bicep
// evaluates to "what's up?"
var myVar = 'what\'s up?'
```

Bicep 中的所有字符串都支持内插。 若要注入表达式，请用 ${ 和 *}` 将表达式括起来。 引用的表达式不能跨多行显示。

```bicep
var storageName = 'storage${uniqueString(resourceGroup().id)}
```

## <a name="multi-line-strings"></a>多行字符串

在 Bicep 中，多线串定义在 3 个单引号字符 (`'''`) 后跟可选换行符（开始序列）和 3 个单引号字符（`'''` - 结束序列）之间。 在开始序列和结束序列之间输入的字符是逐字读取的，无需或不可能进行转义。

> [!NOTE]
> 由于 Bicep 分析程序按原样读取所有字符，因此换行符可解释为 `\r\n` 或 `\n`，具体取决于 Bicep 文件的行尾。
> 多线串目前不支持内插。
> 不支持包含 `'''` 的多线串。

```bicep
// evaluates to "hello!"
var myVar = '''hello!'''

// evaluates to "hello!" because the first newline is skipped
var myVar2 = '''
hello!'''

// evaluates to "hello!\n" because the final newline is included
var myVar3 = '''
hello!
'''

// evaluates to "  this\n    is\n      indented\n"
var myVar4 = '''
  this
    is
      indented
'''

// evaluates to "comments // are included\n/* because everything is read as-is */\n"
var myVar5 = '''
comments // are included
/* because everything is read as-is */
'''

// evaluates to "interpolation\nis ${blocked}"
// note ${blocked} is part of the string, and is not evaluated as an expression
myVar6 = '''interpolation
is ${blocked}'''
```

## <a name="secure-strings-and-objects"></a>安全字符串和对象

安全字符串使用与字符串相同的格式，安全对象使用与对象相同的格式。 使用 Bicep，可将 `@secure()` 修饰符添加到字符串或对象。

将参数设置为安全字符串或安全对象时，参数的值不会保存到部署历史记录中，也不会记入日志。 但是，如果将该安全值设置为不应为安全值的属性，则该值不会受到保护。 例如，如果将安全字符串设置为标记，则该值将以纯文本的形式存储。 使用安全字符串作为密码和机密。

以下示例显示了两个安全参数：

```bicep
@secure()
param password string

@secure()
param configValues object
```

## <a name="next-steps"></a>后续步骤

若要了解 Bicep 的结构和语法，请参阅 [Bicep 文件结构](./file.md)。
