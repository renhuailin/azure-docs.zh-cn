---
title: Linter 规则 - 没有未使用的参数
description: Linter 规则 - 没有未使用的参数
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: e8304948c2b373d16a7fa48c38ac4bac9ec806df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699113"
---
# <a name="linter-rule---no-unused-parameters"></a>Linter 规则 - 没有未使用的参数

Linter 在开发过程中提供指导，可让用户更轻松地强制实施编程标准。 当前的 linter 规则集最小，取自 [arm-ttk 测试用例](../templates/template-test-cases.md)：

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

有关详细信息，请参阅[使用 Bicep linter](./linter.md)。

## <a name="code"></a>代码

`no-unused-params`

## <a name="description"></a>说明

为了尽量避免混淆，请删除模板中已定义但未使用的任何参数。 此测试将查找未在模板中使用的任何参数。 消除未使用的参数还可以更轻松地部署模板，因为无需提供不必要的值。

## <a name="next-steps"></a>后续步骤

有关使用 Visual Studio Code 和 Bicep 扩展的详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。
