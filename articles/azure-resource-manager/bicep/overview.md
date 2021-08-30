---
title: 用于部署 Azure 资源的 Bicep 语言
description: 介绍了用于将基础结构部署到 Azure 的 Bicep 语言。 它通过使用 JSON 来开发模板，提供改进的创作体验。
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 28d42335901db5326be502be52fd4ce2209a7980
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724604"
---
# <a name="what-is-bicep"></a>什么是 Bicep？

Bicep 是一种特定于域的语言 (DSL)，使用声明性语法来部署 Azure 资源。 它提供简明的语法、可靠的类型安全性以及对代码重用的支持。 我们相信，Bicep 会针对你的 Azure 基础结构即代码解决方案提供最佳创作体验。

可以使用 Bicep 而非 JSON 来开发 Azure 资源管理器模板（ARM 模板）。 用于创建 ARM 模板的 JSON 语法可能比较繁琐，需要使用复杂的表达式。 Bicep 语法降低了这种复杂性，改进了开发体验。 Bicep 是基于 ARM 模板 JSON 的透明抽象，不会丢失任何 JSON 模板功能。 在部署过程中，Bicep CLI 将 Bicep 文件转换为 ARM 模板 JSON。

Bicep 不是用于编写应用程序的常规编程语言。 Bicep 文件声明 Azure 资源和资源属性，而不编写用于创建资源的编程命令序列。

在 ARM 模板中有效的资源类型、API 版本和属性在 Bicep 文件中有效。

若要跟踪 Bicep 工作的状态，请参阅 [Bicep 项目存储库](https://github.com/Azure/bicep)。

若要了解 Bicep，请参阅以下视频。

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>入门

若要开始使用 Bicep，请执行以下操作：

1. 安装工具。 请参阅[设置 Bicep 开发和部署环境](./install.md)。 或者，可使用 [VS Code Devcontainer/Codespaces 存储库](https://github.com/Azure/vscode-remote-try-bicep)获取预配置的创作环境。
2. 完成[快速入门](./quickstart-create-bicep-use-visual-studio-code.md)和 [Microsoft Learn Bicep 模块](./learn-bicep.md)。

若要将现有 ARM 模板反向编译为 Bicep，请参阅[将 ARM 模板反向编译为 Bicep](./decompile.md)。 可使用 [Bicep Playground](https://bicepdemo.z22.web.core.windows.net/) 并排查看 Bicep 和等效的 JSON。

有关 Bicep 示例，请参阅 [Bicep GitHub 存储库](https://github.com/Azure/bicep/tree/main/docs/examples)。

## <a name="benefits-of-bicep-versus-other-tools"></a>Bicep 相较于其他工具的优势

与其他选项相比，Bicep 具有以下优势：

- **支持所有资源类型和 API 版本**：Bicep 直接支持 Azure 服务的所有预览版本和正式发布版本。 在资源提供程序引入新的资源类型和 API 版本后，就可以立即在 Bicep 文件中使用它们。 无需等待工具完成更新即可使用新服务。
- 简单的语法：与等效的 JSON 模板相比，Bicep 文件更简洁且更易于阅读。 Bicep 不要求事先了解编程语言。 Bicep 语法是声明性的，可指定要部署的资源和资源属性。
- **创作体验**：使用 VS Code 创建 Bicep 文件时，可获得一流的创作体验。 编辑器提供丰富的类型安全、智能感知和语法验证。
- **模块化**：可以使用 [模块](./modules.md)将 Bicep 代码分解为可管理的部分。 该模块会部署一组相关资源。 通过模块，你可以重用代码和简化开发。 需要部署这些资源时，可以随时将模块添加到 Bicep 文件。
- **与 Azure 服务的集成**：Bicep 与 Azure 服务（如 Azure Policy、模板规格和蓝图）集成。
- **无需管理状态或状态文件**：所有状态均存储在 Azure 中。 用户可以相互协作，并确保按预期处理其更新。 在部署模板之前，请使用 [what-if 操作](./deploy-what-if.md)来预览更改。
- **免费和开源**：Bicep 是完全免费的。 无需为高级功能付费。 它还受 Microsoft 支持部门的支持。

## <a name="bicep-improvements"></a>Bicep 改进

与等效的 JSON 相比，Bicep 提供了更简单且更简洁的语法。 不使用括号表达式 `[...]`。 而是可以直接调用函数，并从参数和变量获取值。 你将为每个已部署的资源指定一个符号名称，这样就可以轻松地在模板中引用该资源。

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

## <a name="faq"></a>常见问题解答

**为什么要创建一种新语言而不是使用现有语言？**

你可以将 Bicep 视为现有 ARM 模板语言的修订版本，而不是一种新语言。 语法已更改，但核心功能和运行时保持不变。

开发 Bicep 之前，我们考虑过使用现有的编程语言。 我们确信，目标受众会发现学习 Bicep 比开始使用另一种语言更容易。

**为什么不将精力集中在 Terraform 或其他第三方基础结构即代码产品/服务上？**

不同的用户喜欢使用不同的配置语言和工具。 我们想要确保所有这些工具在 Azure 上都能提供很棒的体验。 Bicep 是此工作的一部分。

如果你喜欢使用 Terraform，则无需进行切换。 Microsoft 致力于确保 Azure 上的 Terraform 实现最佳效果。

对于选择了 ARM 模板的客户，我们相信 Bicep 能够改进创作体验。 Bicep 还可帮助尚未采用基础结构即代码的客户进行过渡。

这是否已准备好用于生产？

是。 从 0.3 版本开始，Microsoft 支持计划将为 Bicep 提供支持。 Bicep 具有与 ARM 模板相当的功能。 目前没有计划的中断性变更，但我们以后可能需要创建中断性变更。

**Bicep 是否仅适用于 Azure？**

目前，我们未打算将 Bicep 扩展到 Azure 之外。 我们希望为 Azure 提供全面支持并优化部署体验。

要满足该目标，需要使用 Azure 外部的某些 API。 我们希望为这些方案提供扩展点。

**现有 ARM 模板会发生什么情况？**

它们继续像往常一样正常运行。 你不需要进行任何更改。 我们会继续支持基础 ARM 模板 JSON 语言。 Bicep 文件将编译为 JSON，JSON 将发送到 Azure 进行部署。

准备就绪后，你可以[将 JSON 文件反向编译为 Bicep](./decompile.md)。

## <a name="known-limitations"></a>已知的限制

- 不支持单行对象和数组。 例如，`['a', 'b', 'c']` 不受支持。 有关详细信息，请参阅[数组](data-types.md#arrays)和[对象](data-types.md#objects)。
- 不支持将长行分解为多行。 例如：

    ```bicep
    resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
      ...
    }
    ```

    不能写作：

    ```bicep
    resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' =
        if (newOrExisting == 'new') {
          ...
        }
    ```

- 不支持 apiProfile 的概念（用于将单个 apiProfile 映射到每个资源类型的一组 apiVersion）。
- 不支持用户定义函数。

## <a name="next-steps"></a>后续步骤

从[快速入门](./quickstart-create-bicep-use-visual-studio-code.md)着手。
