---
title: Linter 规则 - 首选内插
description: Linter 规则 - 首选内插
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 648b924b545e801f8a97ef5202f8022252ef94c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594340"
---
# <a name="linter-rule---prefer-interpolation"></a>Linter 规则 - 首选内插

Linter 在开发过程中提供指导，可让用户更轻松地强制实施编程标准。 当前的 linter 规则集最小，取自 [arm-ttk 测试用例](../templates/template-test-cases.md)：

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

有关详细信息，请参阅[使用 Bicep linter](./linter.md)。

## <a name="code"></a>代码

`prefer-interpolation`

## <a name="description"></a>说明

应使用字符串内插，而不是使用 concat 函数。

## <a name="examples"></a>示例

以下示例未能通过此测试，因为使用了 concat 函数。

```bicep
param suffix string = '001'
var vnetName = concat('vnet-', suffix)

resource vnet 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: vnetName
  ...
}
```

下面的示例通过了此测试。

```bicep
param suffix string = '001'
var vnetName = 'vnet-${suffix}'

resource vnet 'Microsoft.Network/virtualNetworks@2018-10-01' = {
  name: vnetName
  ...
}
```

## <a name="next-steps"></a>后续步骤

有关使用 Visual Studio Code 和 Bicep 扩展的详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。
