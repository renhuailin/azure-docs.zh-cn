---
title: Azure 资源管理器测试工具包的参数文件测试用例
description: 介绍 Azure 资源管理器模板测试工具包运行的参数文件测试。
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 37fefd84a6385e003a4bb501a789003e8c63d461
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393489"
---
# <a name="test-cases-for-parameter-files"></a>参数文件测试用例

本文介绍使用[模板测试工具包](test-toolkit.md)对[参数文件](parameter-files.md)运行的测试。 例如，对名为 azuredeploy.parameters.json 的文件运行的测试。 这些示例包括测试名称以及通过或未通过测试的代码示例 。 有关如何运行测试或如何运行特定测试的详细信息，请参阅[测试参数](test-toolkit.md#test-parameters)。

该工具包包括适用于 Azure 资源管理器模板（ARM 模板）的[测试用例](template-test-cases.md)，以及名为 azuredeploy.json 或 maintemplate.json 的主模板文件 。

## <a name="use-valid-contentversion"></a>使用有效的 contentVersion

测试名称：DeploymentParameters 应包含 ContentVersion

`contentVersion` 必须包含 `1.0.0.0` 格式的字符串，且仅使用数字。

以下示例未通过测试，因为缺少 `contentVersion`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

以下示例未通过测试，因为 `contentVersion` 不是字符串。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": {},
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
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
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="file-must-include-parameters"></a>文件必须包含 parameters

测试名称：DeploymentParameters 应包含 Parameters

参数文件必须包含 `parameters` 节。

以下示例未通过测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="use-valid-schema-version"></a>使用有效架构版本

测试名称：DeploymentParameters 应包含 Schema

参数文件必须包含有效架构版本。

参数文件有两个有效架构版本：

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#`

以下示例未通过测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2021-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
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
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="parameters-must-contain-values"></a>参数必须包含值

测试名称：DeploymentParameters 应包含 Value

参数必须包含 `value` 或 `reference`。 对于机密（例如密码），密钥保管库将使用参数文件中的 `reference`。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](key-vault-parameter.md)。

以下示例未通过测试，因为 `stgAcctName` 不包含 `value`。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {}
  }
}
```

以下示例通过了测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

- 若要了解测试工具包，请参阅[使用 ARM 模板测试工具包](test-toolkit.md)。
- 有关 ARM 模板测试，请参阅 [ARM 模板的测试用例](template-test-cases.md)。
- 有关 createUiDefinition 测试，请参阅 [createUiDefinition.json 的测试用例](createUiDefinition-test-cases.md)。
- 若要了解所有文件的测试，请参阅[所有文件的测试用例](all-files-test-cases.md)。
