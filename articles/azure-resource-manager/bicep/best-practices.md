---
title: 学习开发 Bicep 文件时的最佳做法
description: 介绍创建 Bicep 文件时要遵循的做法，以便它们正常运行且易于维护。
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 46efbf883e6dc0409e7f8d5f8d379693079acafc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788431"
---
# <a name="best-practices-for-bicep"></a>有关 Bicep 的最佳做法

本文推荐开发 Bicep 文件时要遵循的做法。 这些做法使 Bicep 文件更易于理解和使用。

### <a name="microsoft-learn"></a>Microsoft Learn

若要详细了解 Bicep 最佳做法和实践指南，请参阅“Microsoft Learn”上的[构建用于协作的 Bicep 代码](/learn/modules/structure-bicep-code-collaboration/)。

## <a name="parameters"></a>parameters

* 对于参数声明，请进行恰当命名。 良好的名称使模板易于阅读和理解。 确保使用清晰、描述性的名称，并在命名中保持一致。

* 仔细考虑模板所使用的参数。 尝试将参数用于在不同部署之间更改的设置。 变量和硬编码值可用于在部署之间不会发生更改的设置。

* 请注意使用的默认值。 确保任何人都可以安全地部署默认值。 例如，考虑使用低成本的定价层和 SKU，以便将模板部署到测试环境的人员时不会产生大量不必要的成本。

* 请尽量少用 `@allowed` 修饰器。 如果过于广泛地使用此装饰器，可能会阻止有效部署。 在 Azure 服务添加 SKU 和增加大小时，允许列表可能不是最新的。 例如，在生产环境中仅允许使用高级 v3 SKU 可能有意义，但这会使你无法在非生产环境中使用相同的模板。

* 为参数提供说明是一种很好的做法。 尽量让这些说明有帮助，并提供有关模板所需参数值的重要信息。

  对于某些信息，还可以使用 `//` 注释。

* 可以将参数声明放在模板文件中的任何位置，但通常建议将它们放在文件顶部，这样会使 Bicep 代码易于阅读。

* 建议为控制命名的参数指定最小和最大字符长度。 这些限制有助于避免稍后在部署过程中出现错误。

有关 Bicep 参数的详细信息，请参阅 [Bicep 中的参数](parameters.md)。

## <a name="variables"></a>变量

* 对于变量名称，使用驼峰式大小写，例如 `myVariableName`。

* 定义变量时，不需要[数据类型](data-types.md)。 变量通过解析值推断类型。

* 可以使用 Bicep 函数来创建变量。

* 在 Bicep 文件中定义变量后，可以使用该变量的名称引用该值。

有关 Bicep 变量的详细信息，请参阅 [Bicep 中的变量](variables.md)。

## <a name="naming"></a>命名

* [uniqueString() 函数](bicep-functions-string.md#uniquestring)对于创建全局唯一的资源名称非常有用。 当你提供相同的参数时，它每次都会返回相同的字符串。 传入资源组 ID 意味着该字符串在每次部署到同一资源组时都相同，但在部署到不同资源组或订阅时会有所不同。

* 有时，`uniqueString()` 函数会创建以数字开头的字符串。 某些 Azure 资源（如存储帐户）不允许其名称以数字开头。 这个要求意味着使用字符串插值来创建资源名称是个好主意。 可向此唯一的字符串添加前缀。

* 一般最好使用模板表达式来创建资源名称。 许多 Azure 资源类型都有关于允许的字符及其名称长度的规则。 在模板中嵌入资源名称的创建，意味着使用该模板的任何人都无需自己记得遵守这些规则。

## <a name="resource-definitions"></a>资源定义

* 不是将复杂的表达式直接嵌入到资源属性中，而是使用变量来包含表达式。 这种方法使 Bicep 文件更易于阅读和理解。 这避免了因逻辑而使资源定义混乱。

* 尝试使用资源属性作为输出，而不是对资源的行为进行假设。 例如，如果需要将 URL 输出到应用服务应用，请使用应用的 defaultHostname 属性，而不是自行为 URL 创建一个字符串。 有时这些假设在其他环境中是不正确的，或者资源会改变它们的工作方式。 通过资源本身来了解其自己的属性会更安全。

* 最好对每个资源使用最新的 API 版本。 Azure 服务中的新增功能有时仅在更新的 API 版本中可用。

* 如果可能，请避免在 Bicep 文件中使用 [reference](./bicep-functions-resource.md#reference) 和 [resourceId](./bicep-functions-resource.md#resourceid) 函数。 可以使用符号名称访问 Bicep 中的任何资源。 例如，如果使用符号名称 toyDesignDocumentsStorageAccount 定义存储帐户，则可以使用表达式 `toyDesignDocumentsStorageAccount.id` 访问其资源 ID。 通过使用符号名称，可以在资源之间创建隐式依赖关系。

* 如果资源未部署在 Bicep 文件中，仍可以使用 `existing` 关键字获取对该资源的符号引用。

## <a name="child-resources"></a>子资源

* 避免深度嵌套过多的层。 过多的嵌套会使 Bicep 代码更难阅读和使用。

* 最好避免为子资源构建资源名称。 当 Bicep 了解资源之间的关系时，它将不再提供好处。 请改用 `parent` 属性或嵌套。

## <a name="outputs"></a>输出

* 请确保不要为敏感数据创建输出。 任何有权访问部署历史记录的人员都可以访问输出值。 它们不适合处理机密。

* 使用 `existing` 关键字查找已存在的资源的属性，而不是通过输出来传递属性值。 最佳做法是以这种方式查找来自其他资源的键，而不是通过输出传递这些键。 你将始终获得最新的数据。

有关 Bicep 输出的详细信息，请参阅 [Bicep 中的输出](outputs.md)。

## <a name="tenant-scopes"></a>租户范围

无法在[租户范围](deploy-to-tenant.md)创建策略或角色分配。 但是，如果需要在整个组织中授予访问权限或应用策略，可以将这些资源部署到根管理组。

## <a name="next-steps"></a>后续步骤

* 有关 Bicep 的介绍，请参阅 [Bicep 快速入门](quickstart-create-bicep-use-visual-studio-code.md)。
* 有关 Bicep 文件的各个部分的信息，请参阅[了解 Bicep 文件的结构和语法](file.md)。
