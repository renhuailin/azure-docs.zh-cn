---
title: Bicep 函数 - 文件
description: 介绍 Bicep 文件中用于从文件加载内容的函数。
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 28b28086986eb4e871cc6ed8c315c3802e721840
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359834"
---
# <a name="file-functions-for-bicep"></a>Bicep 的文件函数

本文介绍用于从外部文件加载内容的 Bicep 函数。

## <a name="loadfileasbase64"></a>loadFileAsBase64

`loadFileAsBase64(filePath)`

将文件加载为 base64 字符串。

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| filePath | 是 | 字符串 | 要加载的文件的路径。 该路径相对于已部署的 Bicep 文件。 |

### <a name="remarks"></a>注解

如果要在部署中包含二进制内容，请使用此函数。 不要将文件手动编码为 base64 字符串并将其添加到 Bicep 文件中，而应使用此函数来加载文件。 将 Bicep 文件编译为 JSON 模板时，将加载该文件。 在部署期间，JSON 模板将包含该文件的内容作为硬编码的字符串。

此函数需要 Bicep 0.4.412 或更高版本。 

允许的最大文件大小为 96 Kb。

### <a name="return-value"></a>返回值

base64 字符串形式的文件。

## <a name="loadtextcontent"></a>loadTextContent

`loadTextContent(filePath, [encoding])`

加载指定文件的字符串形式的内容。 

命名空间：[sys](bicep-functions.md#namespaces-for-functions)。

### <a name="parameters"></a>参数

| 参数 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| filePath | 是 | 字符串 | 要加载的文件的路径。 该路径相对于已部署的 Bicep 文件。 |
| encoding | 否 | 字符串 | 文件编码。 默认值为 `utf-8`。 可用选项为：`iso-8859-1`、`us-ascii`、`utf-16`、`utf-16BE` 或 `utf-8`。  |

### <a name="remarks"></a>注解

如果你的内容存储在单独的文件中，请使用此函数。 可以使用此函数加载内容，而无需将内容复制到 Bicep 文件中。 例如，可以从文件加载部署脚本。 将 Bicep 文件编译为 JSON 模板时，将加载该文件。 在部署期间，JSON 模板将包含该文件的内容作为硬编码的字符串。

加载 JSON 文件时，可以将 [json](bicep-functions-object.md#json) 函数与 loadTextContent 函数一起使用以创建 JSON 对象。 在 VS Code 中，可以使用 Intellisense 来分析加载的对象的属性。 例如，可以创建一个文件，其中包含可在多个 Bicep 文件之间共享的值。 本文中演示了一个示例。

此函数需要 Bicep 0.4.412 或更高版本。

允许的最大文件大小为 131,072 个字符（包括行尾字符）。

### <a name="return-value"></a>返回值

字符串形式的文件内容。

### <a name="examples"></a>示例

以下示例从文件加载脚本，并将它用作部署脚本。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loaddeploymentscript.bicep" highlight="13" :::

在下一个示例中，你将创建一个 JSON 文件，其中包含要用于网络安全组的值。

::: code language="json" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/nsg-security-rules.json" :::

加载该文件并将其转换为 JSON 对象。 使用该对象向资源赋值。

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loadsharedrules.bicep" highlight="3,13-21" :::

可以在部署网络安全组的其他 Bicep 文件中重复使用值文件。

## <a name="next-steps"></a>后续步骤

* 有关 Bicep 文件中各部分的说明，请参阅[了解 Bicep 文件的结构和语法](./file.md)。
