---
title: Azure 资源管理器测试工具包的参数文件测试用例
description: 描述 Azure 资源管理器模板测试工具包为所有文件运行的测试。
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 6af9a7c149aacbd50537086ae70a8d845604d2ef
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393473"
---
# <a name="test-cases-for-all-files"></a>所有文件的测试用例

本文介绍了使用[模板测试工具包](test-toolkit.md)针对所有 JavaScript 对象表示法 (JSON) 文件运行的测试。 这些示例包括测试名称以及通过或未通过测试的代码示例 。 有关如何运行测试或如何运行特定测试的详细信息，请参阅[测试参数](test-toolkit.md#test-parameters)。

## <a name="use-valid-json-syntax"></a>使用有效的 JSON 语法

测试名称：JSONFiles Should Be Valid

此测试检查所有 JSON 文件是否包含有效的语法。 例如，azuredeploy.json、azuredeploy.parameters.json 或 createUiDefinition.js 文件  。 如果测试失败，你将看到其他测试的失败或警告，或者 JSON 分析。

### <a name="template-file-example"></a>模板文件示例

下面的示例失败，因为在 azuredeploy.json 中，`parameters`、`comboBox` 和 `location` 缺少前导大括号 (`{`)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters":
    "comboBox":
      "type": "string"
    },
    "location":
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

以下示例通过了测试。

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

### <a name="parameter-file-example"></a>参数文件示例

下面的示例将会失败，因为 azuredeploy.parameters.js 使用没有 `value` 的参数。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value":
    }
  }
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "value": "westus"
    }
  }
}
```

### <a name="createuidefintion-example"></a>CreateUiDefintion 示例

下面的示例将会失败，因为在 createUiDefinition.json 中，`outputs` 缺少前导大括号 (`{`)。

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
    "outputs":
      "comboBox": "[basics('comboBox')]",
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

## <a name="next-steps"></a>后续步骤

- 若要了解测试工具包，请参阅[使用 ARM 模板测试工具包](test-toolkit.md)。
- 有关 ARM 模板测试，请参阅 [ARM 模板的测试用例](template-test-cases.md)
- 若要测试参数文件，请参阅[参数文件的测试用例](parameters.md)。
- 有关 createUiDefinition 测试，请参阅 [createUiDefinition.json 的测试用例](createUiDefinition-test-cases.md)
