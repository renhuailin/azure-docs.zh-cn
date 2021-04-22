---
title: Azure 资源管理器模板的 Bicep 语言
description: 介绍通过 Azure 资源管理器模板将基础结构部署到 Azure 的 Bicep 语言。
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 74028c682b48a492c2e8f13bef538d1694370cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955901"
---
# <a name="what-is-bicep-preview"></a>什么是 Bicep（预览版）？

Bicep 是以声明方式部署 Azure 资源的一种语言。 可以使用 Bicep（而不是 JSON）来开发 Azure 资源管理器模板（ARM 模板）。 Bicep 通过提供简洁的语法、更好的代码重用支持和改进的类型安全性来简化创作体验。 Bicep 是一种特定于域的语言 (DSL)，这意味着它是针对特定方案或域而设计的。 它并非旨在用作编写应用程序的常规编程语言。

用于创建模板的 JSON 语法可能是详细的，需要复杂的表达式。 Bicep 改进了该体验，而不会丢失 JSON 模板的任何功能。 它是基于 ARM 模板的 JSON 的透明抽象。 每个 Bicep 文件都编译为标准 ARM 模板。 ARM 模板中有效的资源类型、API 版本和属性在 Bicep 文件中有效。 当前版本有几个[已知限制](#known-limitations)。

Bicep 目前为预览版。 若要跟踪工作的状态，请参阅 [Bicep 项目存储库](https://github.com/Azure/bicep)。

若要了解 Bicep，请参阅以下视频。

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>入门

若要从 Bicep 开始，请[安装工具](bicep-install.md)。

安装这些工具后，请尝试 [Bicep 教程](./bicep-tutorial-create-first-bicep.md)。 本教程系列将指导你完成 Bicep 的结构和功能。 部署 Bicep 文件，并将 ARM 模板转换为等效的 Bicep 文件。

若要并排查看等效的 JSON 和 Bicep 文件，请参阅 [Bicep 操场](https://aka.ms/bicepdemo)。

如果要将现有 ARM 模板转换为 Bicep，请参阅[在 JSON 和 Bicep 之间转换 ARM 模板](bicep-decompile.md)。

## <a name="bicep-improvements"></a>Bicep 改进

与等效的 JSON 相比，Bicep 提供了更简单且更简洁的语法。 不使用 `[...]` 表达式。 而是直接调用函数，并从参数和变量获取值。 为每个已部署的资源指定一个符号名称，这样就可以轻松地在模板中引用该资源。

例如，以下 JSON 返回资源属性的输出值。

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Bicep 中的等效输出表达式更易于编写。 以下示例使用模板中定义的资源的符号名称 publicIP 返回相同的属性：

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

有关语法的完整比较，请参阅[比较模板的 JSON 和 Bicep](compare-template-syntax.md)。

Bicep 自动管理资源之间的依赖关系。 在其他资源声明中使用了资源的符号名称时，可以避免设置 `dependsOn`。

通过 Bicep，可以将项目分解为多个模块。

Bicep 文件的结构比 JSON 模板更灵活。 可以在文件中的任意位置声明参数、变量和输出。 在 JSON 中，你必须在模板的相应部分中声明所有参数、变量和输出。

Bicep 的 VS Code 扩展提供丰富的验证和智能感知。 例如，可以使用扩展的智能感知来获取资源的属性。

## <a name="known-limitations"></a>已知的限制

当前存在以下限制：

* 无法在复制循环上设置模式或批大小。
* 无法合并循环和条件。
* 不支持单行对象和数组（如 `['a', 'b', 'c']`）。

## <a name="faq"></a>常见问题解答

**为什么创建新语言，而不是使用现有语言？**

可以将 Bicep 视为现有 ARM 模板语言（而不是新语言）的修订版本。 语法已更改，但核心功能和运行时保持相同。

开发 Bicep 之前，我们考虑使用现有的编程语言。 我们认为目标受众会更容易了解 Bicep，而不是从其他语言入手。

**为什么不将精力集中在 Terraform 或其他第三方基础结构即代码产品/服务上？**

不同的用户倾向于使用不同的配置语言和工具。 我们希望确保所有这些工具在 Azure 上都能提供出色的体验。 Bicep 是此工作的一部分。

如果你很乐意使用 Terraform，则无需进行切换。 Microsoft 致力于确保 Azure 上的 Terraform 是最佳的。

对于选择了 ARM 模板的客户，我们相信 Bicep 可改进创作体验。 Bicep 还可帮助未将基础结构用作代码的客户进行转换。

**Bicep 仅适用于 Azure？**

Bicep 是一个 DSL，侧重于将完整的解决方案部署到 Azure。 满足该目标要求使用 Azure 外部的某些 API。 我们希望为这些方案提供扩展点。

**现有 ARM 模板会发生什么情况？**

它们会继续完全按照始终使用的方式运行。 不需要进行任何更改。 我们将继续支持基础 ARM 模板 JSON 语言。 Bicep 文件编译为 JSON，并将 JSON 发送到 Azure 以进行部署。

准备就绪后，可以[将 JSON 文件转换为 Bicep](bicep-decompile.md)。

## <a name="next-steps"></a>后续步骤

开始使用 [Bicep 教程](./bicep-tutorial-create-first-bicep.md)。
