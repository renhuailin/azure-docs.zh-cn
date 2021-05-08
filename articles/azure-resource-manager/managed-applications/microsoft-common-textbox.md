---
title: TextBox UI 元素
description: 介绍了 Azure 门户的 Microsoft.Common.TextBox UI 元素。 用于添加无格式文本。
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124323"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI 元素

可用于添加无格式文本的用户界面 (UI) 元素。 `Microsoft.Common.TextBox` 元素是单行输入字段，但支持使用 `multiLine` 属性进行的多行输入。

## <a name="ui-sample"></a>UI 示例

`TextBox` 元素使用单行或多行文本框。

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Microsoft.Common.TextBox 元素单行文本框。":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Microsoft.Common.TextBox 元素多行文本框。":::

## <a name="schema"></a>架构

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
  "constraints": {
    "required": true,
    "validations": [
      {
        "regex": "^[a-z0-9A-Z]{1,30}$",
        "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
      },
      {
        "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
        "message": "Must start with 'contoso'."
      }
    ]
  },
  "visible": true
}
```

## <a name="sample-output"></a>示例输出

```json
"contoso123"
```

## <a name="remarks"></a>备注

- 使用 `toolTip` 属性在鼠标光标悬停在信息符号上时显示有关元素的文本。
- `placeholder` 属性是帮助文本，当用户开始编辑后会消失。 如果同时定义了 `placeholder` 和 `defaultValue`，会优先显示 `defaultValue`。
- `multiLine` 属性为布尔值（`true` 或 `false`）。 若要使用多行文本框，请将该属性设置为 `true`。 如果不需要多行文本框，请将该属性设置为 `false`，或排除该属性。 对于新行，JSON 输出显示 `\n` 作为换行符。 多行文本框接受 `\r` 作为回车符 (CR)，接受 `\n` 作为换行符 (LF)。 例如，默认值可以包含用于指定 CRLF 的 `\r\n`。
- 如果  设置为 true，则文本框必须包含值才能成功通过验证  。 默认值为 `false`。
- `validations` 属性是一个数组，可在其中添加条件，用于检查文本框中提供的值。
- `regex` 属性是一个 JavaScript 正则表达式模式。 在已指定的情况下，此文本框的值必须与模式匹配才能成功通过验证。 默认值为 `null`。 有关正则表达式语法的详细信息，请参阅[正则表达式快速参考](/dotnet/standard/base-types/regular-expression-language-quick-reference)。
- `isValid` 属性包含一个计算结果为 `true` 或 `false` 的表达式。 在表达式中，定义确定文本框是否有效的条件。
- `message` 属性是当文本框的值未通过验证时会显示的一个字符串。
- 当 `required` 设置为 `false` 时可以为 `regex` 指定值。 在这种情况下，文本框并非必须具有值才能成功通过验证。 如果指定了一个值，则它必须与正则表达式模式匹配。

## <a name="examples"></a>示例

以下示例演示如何使用单行文本框和多行文本框。

### <a name="single-line"></a>单行

以下实例结合使用 [Microsoft.Solutions.ArmApiControl](microsoft-solutions-armapicontrol.md) 控件和文本框来检查资源名称的可用性。

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>多行

此示例使用 `multiLine` 属性创建包含占位符文本的多行文本框。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

- 有关创建 UI 定义的简介，请参阅[适合 Azure 托管应用程序的创建体验的 CreateUiDefinition.json](create-uidefinition-overview.md)。
- 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
