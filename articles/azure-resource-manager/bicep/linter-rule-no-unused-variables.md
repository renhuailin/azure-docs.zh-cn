---
title: Linter 规则 - 没有未使用的变量
description: Linter 规则 - 没有未使用的变量
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 80ace33219024f19792ee909c092e498b645665e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594314"
---
# <a name="linter-rule---no-unused-variables"></a>Linter 规则 - 没有未使用的变量

Linter 在开发过程中提供指导，可让用户更轻松地强制实施编程标准。 当前的 linter 规则集最小，取自 [arm-ttk 测试用例](../templates/template-test-cases.md)：

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

有关详细信息，请参阅[使用 Bicep linter](./linter.md)。

## <a name="code"></a>代码

`no-unused-vars`

## <a name="description"></a>说明

为了尽量避免混淆，请删除已定义但未使用的任何变量。 此测试将查找未在模板中使用的任何变量。

## <a name="next-steps"></a>后续步骤

有关使用 Visual Studio Code 和 Bicep 扩展的详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。
