---
title: 共享变量文件模式
description: 介绍共享变量文件模式。
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: eb9a24c0c5b6b7bd037fe5da13fe108878f55a15
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648309"
---
# <a name="shared-variable-file-pattern"></a>共享变量文件模式

此模式可减少 Bicep 文件中共享值的重复。 用户可从 Bicep 文件中的共享 JSON 文件加载这些值。 使用数组时，可将共享值与 Bicep 代码中特定于部署的值连接在一起。

## <a name="context-and-problem"></a>上下文和问题

编写 Bicep 代码时，可能会使用在一组 Bicep 文件中重复使用的常用变量。 每次声明资源时，用户均可复制这些值，比如在 Bicep 文件之间复制和粘贴这些值。 但是，此方法容易出错，并且如果需要更改，则需要更新每个资源定义，才能使其与其他资源定义保持同步。

此外，使用定义为数组的变量时，可能还需在多个 Bicep 文件中使用一组常用值，并且还需为要部署的资源添加特定值。 将共享变量与特定于资源的变量混合使用时，有人更难弄清这两类变量之间的区别。

## <a name="solution"></a>解决方案

用户可以创建一个 JSON 文件，其中包含需要共享的变量。 使用 [`json()` 函数](bicep-functions-object.md#json)和 [`loadTextContent()` 函数](bicep-functions-files.md#loadtextcontent)加载此文件并访问这些变量。 对于数组变量，可使用 [`concat()` 函数](bicep-functions-array.md#concat)函数将共享值与特定资源的所有自定义值组合在一起。

## <a name="example-1-naming-prefixes"></a>示例 1：命名前缀

假设有多个定义资源的 Bicep 文件。 你需要为所有资源使用一致的命名前缀。

定义一个 JSON 文件，其中包含在整个公司中应用的常用命名前缀：

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-prefixes.json" :::

在 Bicep 文件中，声明一个导入共享命名前缀的变量：

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="1" :::

定义资源名称时，通过字符串内插将共享名称前缀与唯一名称后缀连接在一起：

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="3-4" :::

## <a name="example-2-network-security-group-rules"></a>示例 2：网络安全组规则

假设有多个 Bicep 文件定义其自己的网络安全组 (NSG)。 你需使用一组必须应用于每个 NSG 的常用安全规则，然后使用必须添加的特定于应用程序的规则。

定义一个 JSON 文件，其中包含在整个公司中应用的常用安全规则：

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-rules.json" :::

在 Bicep 文件中，声明一个导入共享安全规则的变量：

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="5" :::

创建一个变量数组，以表示此特定 NSG 的自定义规则：

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="6-21" :::

定义 NSG 资源。 使用 `concat()` 函数将两个数组组合在一起并设置 `securityRules` 属性：

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="23-29" highlight="5" :::

## <a name="considerations"></a>注意事项

- 使用此方法时，需将 JSON 文件放入 Bicep 所生成的 ARM 模板中。 Bicep 所生成 JSON ARM 模板的文件限制为 4MB，因此避免使用大型共享变量文件非常重要。
- 确保共享变量数组不与每个 Bicep 文件中指定的数组值冲突。 例如，使用配置集模式定义网络安全组时，请确保不使用多条规则来定义相同的优先级和方向。

## <a name="next-steps"></a>后续步骤

[了解配置集模式。](patterns-configuration-set.md)
