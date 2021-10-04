---
title: Linter 规则 - 安全参数默认值
description: Linter 规则 - 安全参数默认值
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 4fa82fbbe74d9bf51d1eb498341b999a89e12978
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699027"
---
# <a name="linter-rule---secure-parameter-default"></a>Linter 规则 - 安全参数默认值

Linter 在开发过程中提供指导，可让用户更轻松地强制实施编程标准。 当前的 linter 规则集最小，取自 [arm-ttk 测试用例](../templates/template-test-cases.md)：

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

有关详细信息，请参阅[使用 Bicep linter](./linter.md)。

## <a name="code"></a>代码

`secure-parameter-default`

## <a name="description"></a>说明

不要为模板中的[安全参数](./parameters.md#secure-parameters)提供硬编码默认值，除非该参数为空或表达式包含对 [newGuid()](./bicep-functions-string.md#newguid) 的调用。

对包含敏感值（如密码）的参数使用 @secure() 修饰器。 当参数使用安全修饰器时，不会将参数的值记录或存储在部署历史记录中。 此操作可防止恶意用户发现敏感值。

但是，当你为受保护参数提供默认值时，可访问模板或部署历史记录的任何人都可以发现该值。

## <a name="examples"></a>示例

下面的示例未通过此测试：

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

下面的示例通过了此测试：

```bicep
@secure()
param adminPassword string
```

```bicep
@secure()
param adminPassword string = ''
```

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>后续步骤

有关使用 Visual Studio Code 和 Bicep 扩展的详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。
