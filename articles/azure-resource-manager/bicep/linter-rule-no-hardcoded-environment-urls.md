---
title: Linter 规则 - 没有硬编码的环境 URL
description: Linter 规则 - 没有硬编码的环境 URL
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: d35736860dd672ffc00ea1d4cde52d8f4d4ef8c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594315"
---
# <a name="linter-rule---no-hardcoded-environment-url"></a>Linter 规则 - 没有硬编码的环境 URL

Linter 在开发过程中提供指导，可让用户更轻松地强制实施编程标准。 当前的 linter 规则集最小，取自 [arm-ttk 测试用例](../templates/template-test-cases.md)：

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

有关详细信息，请参阅[使用 Bicep linter](./linter.md)。

## <a name="code"></a>代码

`no-hardcoded-env-urls`

## <a name="description"></a>说明

不要在模板中对环境 URL 进行硬编码。 请改用[环境函数](../templates/template-functions-deployment.md#environment)在部署期间动态获取这些 URL。 有关被阻止的 URL 主机的列表，请参阅 [bicepconfig.json](https://github.com/Azure/bicep/blob/main/src/Bicep.Core/Configuration/bicepconfig.json) 中 `DisallowedHosts` 的默认列表。

## <a name="examples"></a>示例

下面的示例未通过此测试，因为 URL 已进行硬编码。

```bicep
var AzureURL = 'https://management.azure.com'
```

与 concat 或 uri 一起使用时，此测试也会失败。

```bicep
var AzureSchemaURL1 = concat('https://','gallery.azure.com')
var AzureSchemaURL2 = uri('gallery.azure.com','test')
```

下面的示例通过了此测试。

```bicep
var AzureSchemaURL = environment().gallery
```

## <a name="configuration"></a>配置

要禁止的 URL 主机集可以使用 bicepconfig.json 文件中的 disallowedHosts 属性进行自定义，如下所示：

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedHosts": [
            "gallery.azure.com",
            "management.core.windows.net",
            "management.azure.com",
            "database.windows.net",
            "core.windows.net",
            "login.microsoftonline.com",
            "graph.windows.net",
            "trafficmanager.net",
            "datalake.azure.net",
            "azuredatalakestore.net",
            "azuredatalakeanalytics.net",
            "vault.azure.net",
            "api.loganalytics.io",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "batch.core.windows.net"
          ]
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

有关使用 Visual Studio Code 和 Bicep 扩展的详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。
