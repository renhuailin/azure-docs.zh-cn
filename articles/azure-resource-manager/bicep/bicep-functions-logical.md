---
title: Bicep 函数 - 逻辑
description: 介绍 Bicep 文件中用于确定逻辑值的函数。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: e3a811102c31301386135042c79c205eeb9f7f9a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758420"
---
# <a name="logical-functions-for-bicep"></a>Bicep 的逻辑函数

Bicep 提供了用于将值转换为布尔值的 `bool` 函数。

Azure 资源管理器模板中的大多数逻辑函数在 Bicep 中将被替换为[逻辑运算符](./operators-logical.md)。

## <a name="bool"></a>bool

`bool(arg1)`

将参数转换为布尔值。

### <a name="parameters"></a>parameters

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| arg1 |是 |字符串或整数 |要转换为布尔值的值。 |

### <a name="return-value"></a>返回值

转换后的值的布尔值。

### <a name="examples"></a>示例

以下示例演示如何对字符串或整数使用 bool。

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

上述示例中使用默认值的输出为：

| 名称 | 类型 | Value |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="next-steps"></a>后续步骤

* 有关涉及逻辑值的其他操作，请参阅[逻辑运算符](./operators-logical.md)。
