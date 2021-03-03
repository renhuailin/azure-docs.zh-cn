---
title: 适用于 Azure 资源管理器模板的 Bicep 语言
description: 介绍通过 Azure 资源管理器模板将基础结构部署到 Azure 的 Bicep 语言。
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 6a2750dc99e82c9cf8c9b8b97d156d3a9fe30f31
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744567"
---
# <a name="what-is-bicep-preview"></a>什么是 Bicep (预览) ？

Bicep 是用来以声明方式部署 Azure 资源的一种语言。 它通过提供简洁的语法简化了创作体验，并可更好地支持模块化和代码重用。 Bicep 是一种特定于域的语言 (DSL) ，这意味着它是为特定方案或域设计的。 Bicep 不是用于编写应用程序的常规编程语言。

Bicep 是基于 Azure 资源管理器模板的透明抽象 (ARM 模板) 。 每个 Bicep 文件都编译为标准 ARM 模板。 ARM 模板中有效的资源类型、API 版本和属性在 Bicep 文件中有效。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-started"></a>入门

若要从 Bicep 开始，请 [安装工具](https://github.com/Azure/bicep/blob/main/docs/installing.md)。

安装这些工具后，请尝试 [Bicep 教程](./bicep-tutorial-create-first-bicep.md)。 本教程系列将指导您完成 Bicep 的结构和功能。 部署 Bicep 文件，并将 ARM 模板转换为等效的 Bicep 文件。

若要并行查看等效的 JSON 和 Bicep 文件，请参阅 [Bicep 板块](https://aka.ms/bicepdemo)。

如果要将现有 ARM 模板转换为 Bicep，请参阅将 [JSON 反编译为 Bicep](compare-template-syntax.md#decompile-json-to-bicep)。

## <a name="bicep-improvements"></a>Bicep 改进

与等效的 JSON 相比，Bicep 提供了更简单、更简洁的语法。 不使用 `[...]` 表达式。 相反，你可以直接调用函数、从参数和变量获取值以及引用资源。 有关语法的完整比较，请参阅 [比较 JSON 和模板的 Bicep](compare-template-syntax.md)。

Bicep 自动管理资源之间的依赖关系。 `dependsOn`如果在其他资源声明中使用了资源的符号名称，则可以避免设置。

通过 Bicep，可以将项目分解为多个模块。

Bicep 文件的结构比 JSON 模板更灵活。 可以在文件中的任意位置声明参数、变量和输出。 在 JSON 中，你必须在模板的相应部分中声明所有参数、变量和输出。

Bicep 的 VS Code 扩展提供了更丰富的验证和 intellisense。 例如，该扩展插件具有用于获取资源属性的 intellisense。

## <a name="faq"></a>常见问题解答

**为什么要创建新语言而不是使用现有语言？**

您可以将 Bicep 视为现有 ARM 模板语言的修订版本，而不是一种新语言。 语法已更改，但核心功能和运行时保持不变。

开发 Bicep 之前，我们考虑使用现有的编程语言。 我们决定目标受众会发现 Bicep，而不是从其他语言开始。

**为什么不将精力集中在 Terraform 或其他第三方基础结构上作为代码产品？**

不同的用户倾向于使用不同的配置语言和工具。 我们想要确保所有这些工具在 Azure 上都能提供出色的体验。 Bicep 是此工作的一部分。

如果你使用 Terraform，则无需进行切换。 Microsoft 致力于确保 Azure 上的 Terraform 是最佳的。

对于选择了 ARM 模板的客户，我们相信 Bicep 改进了创作体验。 Bicep 还可帮助将基础结构作为代码采用的客户进行过渡。

**仅适用于 Azure Bicep？**

Bicep 是一个 DSL，侧重于将完整的解决方案部署到 Azure。 满足目标要求使用 Azure 外部的某些 Api。 我们希望为这些方案提供扩展点。

**现有 ARM 模板会发生什么情况？**

它们将继续完全按始终使用的方式运行。 无需进行任何更改。 我们将继续支持底层 ARM 模板 JSON 语言。 Bicep 文件编译为 JSON，并将 JSON 发送到 Azure 以进行部署。

准备就绪后，可以 [将 JSON 文件转换为 Bicep](compare-template-syntax.md#decompile-json-to-bicep)。

## <a name="next-steps"></a>后续步骤

[Bicep 教程](./bicep-tutorial-create-first-bicep.md)入门。
