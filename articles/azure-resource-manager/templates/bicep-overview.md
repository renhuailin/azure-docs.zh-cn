---
title: 适用于 Azure 资源管理器模板的 Bicep 语言
description: 介绍了用于通过 Azure 资源管理器模板将基础结构部署到 Azure 的 Bicep 语言。
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: af207e6ca88eab50fe6030883379c87c0ec05691
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773740"
---
# <a name="what-is-bicep-preview"></a>什么是 Bicep（预览版）？

Bicep 是用来以声明方式部署 Azure 资源的一种语言。 可以使用 Bicep（而不是 JSON）来开发 Azure 资源管理器模板（ARM 模板）。 Bicep 通过提供简洁的语法、更好的代码重用支持和改进的类型安全性来简化创作体验。 Bicep 是一种特定于域的语言 (DSL)，这意味着它是为特定方案或域设计的。 它并非旨在用作编写应用程序的常规编程语言。

用于创建模板的 JSON 语法可能比较繁琐，需要使用复杂的表达式。 Bicep 改进了该体验，且没有丢失 JSON 模板的任何功能。 它是基于适用于 ARM 模板的 JSON 的透明抽象。 每个 Bicep 文件都会编译成标准的 ARM 模板。 在 ARM 模板中有效的资源类型、API 版本和属性在 Bicep 文件中有效。 当前版本有几个[已知限制](#known-limitations)。

Bicep 目前为预览版。 若要跟踪工作的状态，请参阅 [Bicep 项目存储库](https://github.com/Azure/bicep)。

若要了解 Bicep，请参阅以下视频。

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>入门

若要开始使用 Bicep，请[安装工具](bicep-install.md)。

安装这些工具后，请尝试 [Bicep 教程](./bicep-tutorial-create-first-bicep.md)。 本教程系列将指导你完成 Bicep 的结构和功能。 你将部署 Bicep 文件，并将 ARM 模板转换为等效的 Bicep 文件。

若要并排查看等效的 JSON 和 Bicep 文件，请参阅 [Bicep 操场](https://aka.ms/bicepdemo)。

如果要将现有 ARM 模板转换为 Bicep，请参阅[在 JSON 和 Bicep 之间转换 ARM 模板](bicep-decompile.md)。

## <a name="benefits-of-bicep-versus-other-tools"></a>Bicep 相较于其他工具的优势

与其他选项相比，Bicep 具有以下优势：

* **支持所有资源类型和 API 版本**：Bicep 直接支持 Azure 服务的所有预览版本和正式发布版本。 在资源提供程序引入新的资源类型和 API 版本后，就可以立即在 Bicep 文件中使用它们。 无需等待工具完成更新即可使用新服务。
* **创作体验**：使用 VS Code 创建 Bicep 文件时，可获得一流的创作体验。 编辑器提供丰富的类型安全、智能感知和语法验证。
* **模块化**：可以使用[模块](bicep-modules.md)将 Bicep 代码分解为可管理的部分。 该模块会部署一组相关资源。 通过模块，你可以重用代码和简化开发。 需要部署这些资源时，可以随时将模块添加到 Bicep 文件。
* **与 Azure 服务的集成**：Bicep 与 Azure 服务（如 Azure Policy、模板规格和蓝图）集成。
* **无需管理状态或状态文件**：所有状态均存储在 Azure 中。 用户可以相互协作，并确保按预期处理其更新。 在部署模板之前，请使用 [what-if 操作](template-deploy-what-if.md)来预览更改。
* **免费和开源**：Bicep 是完全免费的。 无需为高级功能付费。 它还受 Microsoft 支持部门的支持。

## <a name="bicep-improvements"></a>Bicep 改进

与等效的 JSON 相比，Bicep 提供了更简单且更简洁的语法。 你不需要使用 `[...]` 表达式， 而是可以直接调用函数，并从参数和变量获取值。 你将为每个已部署的资源指定一个符号名称，这样就可以轻松地在模板中引用该资源。

例如，以下 JSON 从某个资源属性返回输出值。

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Bicep 中等效的输出表达式更容易编写。 下面的示例使用模板中定义的资源的符号名称 **publicIP** 返回相同的属性：

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

有关语法的完整比较，请参阅[比较模板的 JSON 和 Bicep](compare-template-syntax.md)。

Bicep 自动管理资源之间的依赖关系。 当在其他资源声明中使用了某个资源的符号名称时，你不需要设置 `dependsOn`。

Bicep 文件的结构比 JSON 模板更灵活。 可以在文件中的任意位置声明参数、变量和输出。 在 JSON 中，你必须在模板的相应部分声明所有参数、变量和输出。

## <a name="known-limitations"></a>已知的限制

当前存在以下限制：

* 无法在复制循环上设置模式或批大小。
* 无法合并循环和条件。
* 不支持单行对象和数组（如 `['a', 'b', 'c']`）。

## <a name="faq"></a>常见问题解答

**为什么要创建一种新语言而不是使用现有语言？**

你可以将 Bicep 视为现有 ARM 模板语言的修订版本，而不是一种新语言。 语法已更改，但核心功能和运行时保持不变。

开发 Bicep 之前，我们考虑过使用现有的编程语言。 我们确信，目标受众会发现学习 Bicep 比开始使用另一种语言更容易。

**为什么不将精力集中在 Terraform 或其他第三方基础结构即代码产品/服务上？**

不同的用户喜欢使用不同的配置语言和工具。 我们想要确保所有这些工具在 Azure 上都能提供很棒的体验。 Bicep 是此工作的一部分。

如果你喜欢使用 Terraform，则无需进行切换。 Microsoft 致力于确保 Azure 上的 Terraform 实现最佳效果。

对于选择了 ARM 模板的客户，我们相信 Bicep 能够改进创作体验。 Bicep 还可帮助尚未采用基础结构即代码的客户进行过渡。

**Bicep 是否仅适用于 Azure？**

Bicep 是一种 DSL，专用于将完整的解决方案部署到 Azure。 要满足该目标，需要使用 Azure 外部的某些 API。 我们希望为这些方案提供扩展点。

**现有 ARM 模板会发生什么情况？**

它们继续像往常一样正常运行。 你不需要进行任何更改。 我们会继续支持基础 ARM 模板 JSON 语言。 Bicep 文件将编译为 JSON，JSON 将发送到 Azure 进行部署。

准备就绪后，你可以[将 JSON 文件转换为 Bicep](bicep-decompile.md)。

## <a name="next-steps"></a>后续步骤

开始使用 [Bicep 教程](./bicep-tutorial-create-first-bicep.md)。
