---
title: Bicep 函数 - 逻辑
description: 介绍 Bicep 文件中用于确定逻辑值的函数。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 8fe09057ea293300754fc35614c00a9b808da247
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744910"
---
# <a name="logical-functions-for-bicep"></a>Bicep 的逻辑函数

资源管理器为 Bicep 提供 `bool` 函数。 

Azure 资源管理器模板中的大多数逻辑函数在 Bicep 中都被替换为[逻辑运算符](./operators-logical.md)。

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

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)演示如何对字符串或整数使用 bool。

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

* 对于大多数逻辑操作，请参阅 [Bicep 逻辑运算符](./operators-logical.md)。
* 有关 Bicep 文件中各部分的说明，请参阅[了解 Bicep 文件的结构和语法](./file.md)。
