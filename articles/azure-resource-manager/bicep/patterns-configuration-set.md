---
title: 配置集模式
description: 描述配置集模式。
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: b2ccac6f646304d7d530e616a57c8490e17aba22
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446597"
---
# <a name="configuration-set-pattern"></a>配置集模式

不是定义大量单个参数，而是创建预定义值集。 在部署期间，选择要使用的值集。

## <a name="context-and-problem"></a>上下文和问题

单个 Bicep 文件通常可定义多个资源。 每个资源可能需要根据部署到的环境使用不同的配置。 例如，你可以生成一个 Bicep 文件，用于部署应用服务计划和应用，以及存储帐户。 其中每个资源都有多个选项，这些选项会影响其成本、可用性和复原能力。 对于生产环境，需要使用一组优先考虑高可用性和复原能力的配置。 对于非生产环境，需要使用一组优先考虑成本降低问题的不同配置。

你可以为每个配置设置创建参数，但此方法存在一些缺点：

- 此方法会给模板用户带来负担，因为他们需要了解用于每个资源的值，以及设置每个参数所产生的影响。
- 模板中的参数数量会随着你定义的每个新资源的增加而上升。
- 用户可能选择未经测试或无法正常工作的参数值组合。

## <a name="solution"></a>解决方案

创建单个参数来指定环境类型。 根据参数值，使用变量自动选择每个资源的配置。

> [!NOTE]
> 此方法有时被称为“T 恤尺寸法”。 当你购买 T 恤时，没有大量长度、宽度、衣袖等选项。 你只需在小、中、大尺寸之间进行选择即可，T 恤设计师已根据该尺寸预定义那些度量值。

## <a name="example"></a>示例

假设有一个可部署到两类环境的模板：非生产环境和生产环境。 根据环境类型，所需的配置不同：

| 属性 | 非生产环境 | 生产环境 |
|-|-|-|
| **应用服务计划** |
| SKU 名称 | S2 | P2V3 |
| 容量（实例数） | 1 | 3 |
| **应用服务应用** |
| Always On | 已禁用 | 已启用 |
| **存储帐户** |
| SKU 名称 | Standard_LRS | Standard_ZRS |

你可对此模板使用配置集模式。

接受单个指示环境类型的参数，如生产或非生产环境。 使用 `@allowedValues` 参数修饰器确保模板用户仅提供预期值：

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="5-9" :::

然后创建映射变量，即根据环境类型定义特定配置的对象。 请注意，此变量具有两个名为 `Production` 和 `NonProduction` 的对象。 这些名称与前面示例中参数的允许值相匹配：

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="16-49" :::

定义资源时，可使用配置映射来定义资源属性：

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-configuration-set/main.bicep" range="51-74" highlight="4, 14, 23" :::

## <a name="considerations"></a>注意事项

- 在映射变量中，可考虑按资源对属性分组以简化其定义。
- 在映射变量中，可定义单独的属性值（如示例中的 `alwaysOn` 属性），或设置对象属性的对象变量（如示例中的 SKU 属性）。
- 可考虑对[资源条件](conditional-resource-deployment.md)使用配置集。 这样可让 Bicep 代码为特定环境部署某些资源，而不在其他环境中部署这些资源。

## <a name="next-steps"></a>后续步骤

[了解共享变量文件模式。](patterns-shared-variable-file.md)
