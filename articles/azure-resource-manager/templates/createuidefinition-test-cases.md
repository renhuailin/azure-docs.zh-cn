---
title: Azure 资源管理器测试工具包的 createUiDefinition.json 测试用例
description: 介绍由 Azure 资源管理器模板测试工具包运行的 createUiDefinition.json 测试。
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 573f7db7c1f6fd3d45531e8be15db3d8affc8eba
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392027"
---
# <a name="test-cases-for-createuidefinitionjson"></a>createUiDefinition.json 的测试用例

本文介绍 [createUiDefinition.json](../managed-applications/create-uidefinition-overview.md) 文件的[模板测试工具包](test-toolkit.md)运行的测试。 这些示例包括测试名称以及通过或未通过测试的代码示例 。

该工具包包括适用于 Azure 资源管理器模板（ARM 模板）的[测试用例](template-test-cases.md)，以及名为“azuredeploy.json”或“maintemplate.json”的主模板文件 。 当目录包含“createUiDefinition.json”文件时，将为 UI 控件运行特定测试。 有关如何运行测试或如何运行特定测试的详细信息，请参阅[测试参数](test-toolkit.md#test-parameters)。

“createUiDefinition.json”文件使用[元素](../managed-applications/create-uidefinition-elements.md)和[函数](../managed-applications/create-uidefinition-functions.md)创建自定义用户界面 (UI) 控件。

## <a name="verify-template-parameter-allows-values"></a>验证模板参数允许值

测试名称：允许值实际应得到允许

此测试检查主模板的参数中是否允许“createUiDefinition.json”中每个控件的值。 参数按名称在主模板和“createUiDefinition.json”文件之间进行映射。

主模板的参数必须接受来自控件的 `allowedValues` 的值。 该测试还会检查是否在“createUiDefinition.json”`outputs` 部分中引用了该控件。

此测试检查主模板和“createUiDefinition.json”文件。 “createUiDefinition.json”文件的示例显示在主模板示例之后。

下面的示例未通过，因为主模板的参数名称 `combo` 与控件的参数名称 `comboBox` 不匹配。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "combo": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

下面的示例未通过，因为主模板的参数类型 `int` 不接受控件的 `string` 值。 如果主模板的参数定义了 `defaultValue`，则它在控件的 `allowedValues` 中必须是有效的 `value`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('combo')]"
    }
  }
}
```

下面的示例通过，因为主模板的参数名称与控件的参数名称匹配。 模板的参数类型是 `string`，其 `defaultValue` 在控件的 `allowedValues` 中指定。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    }
  },
  "resources": [],
  "outputs": {
    "comboBoxOutput": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    }
  }
}
```

此示例的“createUiDefinition.json”文件：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "demoComboBox",
        "label": "demoComboBoxLabel",
        "elements": [
          {
            "name": "comboBox",
            "type": "Microsoft.Common.DropDown",
            "label": "Example drop down",
            "defaultValue": "Value two",
            "toolTip": "This is a tool tip",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Value one",
                  "description": "The value to select for option 1.",
                  "value": "one"
                },
                {
                  "label": "Value two",
                  "description": "The value to select for option 2.",
                  "value": "two"
                }
              ],
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "comboBox": "[steps('demoComboBox').comboBox]"
    }
  }
}
```

## <a name="output-controls-must-exist"></a>输出控件必须存在

测试名称：输出中的控件必须存在

`outputs` 部分中使用的控件必须存在于“createUiDefinition.json”中其他位置的元素中。 在 `outputs` 中引用的名称必须与 `basics[]` 或 `steps[]` 中使用的名称相匹配。

下面的示例未通过。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "nameDoesNotMatchOutput",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="properties-must-include-values"></a>属性必须包含值

测试名称：CreateUIDefinition 不能有空格

属性必须包含值。 需要的属性必须使用有效值。 应删除空的可选属性。 测试允许空白 `"basics": []`、`"steps": []` 或 `defaultValue`。

下面的示例未通过，因为 `label`、`placeholder` 和 `toolTip` 为空。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "",
        "placeholder": "",
        "defaultValue": "",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

下面的示例通过，因为 `label` 和 `toolTip` 具有值，并已删除 `placeholder`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "defaultValue": "",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="use-valid-schema-and-version"></a>使用有效的架构和版本

测试名称：CreateUIDefinition 应具有架构

“createUiDefinition.json”文件必须包含 `$schema` 属性，并使用有效的 `$schema` 和 `version`。 `$schema` 和 `version` 中的版本号必须匹配。

下面的示例未通过。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.9.9-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.9.9-preview"
}
```

下面的示例通过，因为它使用最新的 `$schema` 和 `version`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-credential-confirmation"></a>不要隐藏凭据确认

测试名称：不应隐藏凭据确认

此测试检查是否已确认 [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) 或 [Microsoft.Compute.CredentialsCombo](../managed-applications/microsoft-compute-credentialscombo.md) 的凭据。 应将 `hideConfirmation` 属性设置为 `false`，以便确认可见。

下面的示例未通过，因为 `hideConfirmation` 为 `true`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": true
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

下面的示例通过，因为 `hideConfirmation` 为 `false`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "credentials",
        "type": "Microsoft.Compute.CredentialsCombo",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": {
          "password": "Type your credentials"
        },
        "constraints": {
          "required": true,
          "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
          "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
        },
        "options": {
          "hideConfirmation": false
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "credentials": "[basics('credentials')]"
    }
  }
}
```

## <a name="use-correct-handler"></a>使用正确的处理程序

测试名称：处理程序必须正确

在“createUiDefinition.json”文件中使用 `Microsoft.Azure.CreateUIDef` 或 `Microsoft.Compute.MultiVm`。

下面的示例未通过。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.",
  "version": "0.1.2-preview"
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview"
}
```

## <a name="dont-hide-existing-resources"></a>不要隐藏现有资源

测试名称：必须正确处理 HideExisting

如果 `hideExisting` 设置为 `false` 或省略，则 `outputs` 必须包含 `resourceGroup` 和 `newOrExisting`。 `hideExisting` 的默认值是 `false`。

包括 `hideExisting` 的控件类型示例是 [Microsoft.Storage.StorageAccountSelector](../managed-applications/microsoft-storage-storageaccountselector.md)、[Microsoft.Network.PublicIpAddressCombo](../managed-applications/microsoft-network-publicipaddresscombo.md) 或 [Microsoft.Network.VirtualNetworkCombo](../managed-applications/microsoft-network-virtualnetworkcombo.md)。

下面的示例未通过。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]"
    }
  }
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "storage",
        "type": "Microsoft.Storage.StorageAccountSelector",
        "label": "Storage account",
        "toolTip": "This is a demo storage account",
        "defaultValue": {
          "name": "storageaccount01",
          "type": "Premium_LRS"
        },
        "options": {
          "hideExisting": false
        },
        "visible": false
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "resourceGroup": "[basics('storage').resourceGroup]",
      "newOrExisting": "[basics('storage').newOrExisting]"
    }
  }
}
```

## <a name="use-location-in-outputs"></a>使用输出中的位置

测试名称：位置应在输出中

该 `outputs` 部分应包含使用[位置](../managed-applications/create-ui-definition-referencing-functions.md#location)函数的位置。

下面的示例未通过，因为 `outputs` 不包含位置。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="include-control-outputs-in-template-parameters"></a>在模板参数中包含控件输出

测试名称：模板参数中必须包含输出

该测试检查“createUiDefinition.json”是否包含 `outputs` 部分。 该测试还会检查在主模板的 `parameters` 部分中是否定义了这些 `outputs`。 名称必须匹配，因为参数按名称在“createUiDefinition.json”和主模板之间进行映射。

此测试检查主模板和“createUiDefinition.json”文件。 “createUiDefinition.json”文件的示例显示在主模板示例之后。

下面的示例未通过，因为主模板不包含“createUiDefinition.json”文件的 `outputs` 部分中的 `comboBox` 参数。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

下面的示例通过，因为主模板包含 `comboBox` 参数。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string",
      "defaultValue": "two"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

此示例的“createUiDefinition.json”文件：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

## <a name="parameters-without-default-must-exist-in-outputs"></a>输出中必须存在没有默认值的参数

测试名称：CreateUIDefinition 中必须存在没有默认值的参数

“createUiDefinition.json”文件的 `outputs` 部分中必须存在主模板中没有默认值的参数。

此测试检查主模板和“createUiDefinition.json”文件。 “azuredeploy.json”文件的示例显示在控件的示例之后。

下面的示例未通过，因为“createUiDefinition.json”文件的 `outputs` 不包含主模板的参数 `comboBox`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]"
    }
  }
}
```

下面的示例通过，因为“createUiDefinition.json”在 `outputs` 中包含 `comboBox`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "comboBox": "[basics('comboBox')]",
      "location": "[location()]"
    }
  }
}
```

此示例的“azuredeploy.json”文件。 `comboBox` 参数没有默认值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "comboBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "comboBox": {
      "type": "string",
      "value": "[parameters('comboBox')]"
    },
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

## <a name="use-secure-parameter-with-password-box"></a>结合使用密码框和安全参数

测试名称：必须对密码参数使用密码文本框

此测试检查主模板的 `parameters` 和“createUiDefinition.json”`outputs` 中是否定义了 [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) 元素。 密码框的主模板的参数类型必须为 `secureString` 或 `secureObject`。

此测试检查主模板和“createUiDefinition.json”文件。 “createUiDefinition.json”文件的示例显示在主模板示例之后。

下面的示例未通过，因为主模板的 `passwordBox` 参数是 `string`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

下面的示例通过，因为主模板的 `passwordBox` 参数是 `secureString`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "passwordBox": {
      "type": "secureString"
    },
    "location": {
      "type": "string"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    }
  }
}
```

此示例的“createUiDefinition.json”文件：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="password-box-requires-minimum-length"></a>密码框要求最小长度

测试名称：PasswordBoxes 必须具有最小长度

该测试检查 [Microsoft.Common.PasswordBox](../managed-applications/microsoft-common-passwordbox.md) 元素是否使用需要至少 12 个字符的 `regex` 的 `constraints`。

下面的示例未通过，因为没有 `constraints`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

下面的示例通过，因为 `regex` 需要至少 12 个字符。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "passwordBox",
        "type": "Microsoft.Common.PasswordBox",
        "label": {
          "password": "Password",
          "confirmPassword": "Confirm password"
        },
        "toolTip": "Type a password",
        "constraints": {
          "required": true,
          "regex": "^[a-zA-Z0-9]{12,}$",
          "validationMessage": "Password must be at least 12 characters long, contain only numbers and letters"
        }
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "passwordBox": "[basics('passwordBox')]"
    }
  }
}
```

## <a name="text-box-must-use-validation"></a>文本框必须使用验证

测试名称：文本框格式良好

使用文本框验证来检查包含 `regex` 和 `message` 的 `constraints`。

下面的示例未通过。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="tooltip-must-exist-with-a-value"></a>toolTip 必须存在并包含值

测试名称：应存在工具提示

此测试检查 `toolTip` 属性是否存在并包含值。

下面的示例未通过。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": ""
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "comboBox",
        "type": "Microsoft.Common.DropDown",
        "label": "Example drop down",
        "toolTip": "This is a tool tip"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "comboBox": "[basics('comboBox')]"
    }
  }
}
```

## <a name="dont-set-a-default-user-name"></a>不要设置默认用户名

测试名称：用户名不应具有默认值

该测试检查是否为 [Microsoft.Compute.UserNameTextBox](../managed-applications/microsoft-compute-usernametextbox.md) 设置了 `defaultValue`。

下面的示例未通过，因为提供了 `defaultValue`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "defaultValue": "admin",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "userNameBox",
        "type": "Microsoft.Compute.UserNameTextBox",
        "label": "User name",
        "toolTip": "Enter your user name",
        "osPlatform": "Windows"
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "userNameBox": "[basics('userNameBox')]"
    }
  }
}
```

## <a name="use-message-with-validations"></a>使用验证消息

测试名称：验证必须具有消息

此测试检查“createUiDefinition.json”中的任何 `validations` 是否包含 `message`。

下面的示例未通过，因为 `regex` 验证没有 `message`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$"
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "textBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Text box",
        "toolTip": "Type 1-30 alphanumeric characters",
        "placeholder": "Type your text here",
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z]{1,30}$",
              "message": "Only 1-30 characters alphanumeric characters are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "textBox": "[basics('textBox')]"
    }
  }
}
```

## <a name="virtual-machine-sizes-must-match"></a>虚拟机大小必须匹配

测试名称：VM 大小必须与模板匹配

此测试检查 [Microsoft.Compute.SizeSelector](../managed-applications/microsoft-compute-sizeselector.md) 是否在“createUiDefinition.json”`outputs` 和主模板的 `parameters` 部分中。 指定 `defaultValue` 的主模板参数必须与控件的 `allowedSizes` 中的值匹配。

此测试检查主模板和“createUiDefinition.json”文件。 “createUiDefinition.json”文件的示例显示在主模板示例之后。

下面的示例未通过，因为主模板的 `defaultValue` 与 `allowedSizes` 中的值不匹配。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D9"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

下面的示例通过，因为主模板的 `defaultValue` 与 `allowedSizes` 中的值匹配。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D3"
    }
  },
  "resources": [],
  "outputs": {
    "location": {
      "type": "string",
      "value": "[parameters('location')]"
    },
    "vmSize": {
      "type": "string",
      "value": "[parameters('vmSize')]"
    }
  }
}
```

此示例的“createUiDefinition.json”文件：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "vmSize",
        "type": "Microsoft.Compute.SizeSelector",
        "label": "VM Size",
        "toolTip": "Select a virtual machine size",
        "recommendedSizes": [
          "Standard_D1"
        ],
        "constraints": {
          "allowedSizes": [
            "Standard_D1",
            "Standard_D2",
            "Standard_D3"
          ]
        },
        "osPlatform": "Windows",
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "location": "[location()]",
      "vmSize": "[basics('vmSize')]"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

- 若要创建 Azure 门户用户界面，请参阅[适用于 Azure 托管应用程序的创建体验的 CreateUiDefinition.json](../managed-applications/create-uidefinition-overview.md)。
- 若要使用“创建 UI 定义沙盒”，请参阅[测试 Azure 托管应用程序的门户界面](../managed-applications/test-createuidefinition.md)。
- 有关 UI 控件详细信息，请参阅 [CreateUiDefinition 元素](../managed-applications/create-uidefinition-elements.md)和 [CreateUiDefinition 函数](../managed-applications/create-uidefinition-functions.md)。
- 若要详细了解 ARM 模板测试，请参阅 [ARM 模板的测试用例](template-test-cases.md)。
