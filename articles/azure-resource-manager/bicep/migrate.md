---
title: 迁移 Azure 资源和 JSON ARM 模板以使用 Bicep
description: 介绍迁移 Azure 资源和 JSON ARM 模板以使用 Bicep 时建议的工作流。
author: joshuawaddell
ms.author: jowaddel
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: e9ec55ac41cb30a902b337184cc75a4f976700bf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699383"
---
# <a name="migrate-to-bicep"></a>迁移到 Bicep

在 Bicep 中定义 Azure 资源有许多好处，包括：更简单的语法、模块化、自动依赖项管理、类型验证和 IntelliSense，以及改进的创作体验。

如果有现有的 JSON Azure 资源管理器模板（ARM 模板）和/或已部署资源，并且想要安全地将这些资源迁移到 Bicep，建议遵循建议的工作流，其中包括五个阶段：

:::image type="content" source="./media/migrate/five-phases.png" alt-text="将 Azure 资源迁移到 Bicep 的五阶段示意图：转换、迁移、重构、测试和部署。" border="false":::

此过程的第一步是将 Azure 资源捕获为 JSON 文件（如果不存在）。 然后，将 JSON 文件重新编译为初始 Bicep 文件，通过重构来改进该文件。 如果具有工作文件，可以使用一个过程进行测试和部署，以最大程度地降低对 Azure 环境进行中断性变更的风险。

:::image type="content" source="./media/migrate/migrate-bicep.png" alt-text="将 Azure 资源迁移到 Bicep 的建议工作流的关系图。" border="false":::

本文汇总了此建议工作流。 有关详细指导，请参阅 Microsoft Learn 上的[迁移 Azure 资源和 JSON ARM 模板以使用 Bicep](/learn/modules/migrate-azure-resources-bicep/)。

## <a name="phase-1-convert"></a>阶段1：转换

在将资源迁移到 Bicep 的转换阶段，目标是要捕获 Azure 资源的初始表示。 你在此阶段创建的 Bicep 文件并未完成，尚未准备好使用。 但是，该文件提供了开始迁移的起点。

转换阶段由两个步骤组成，按顺序完成这些步骤：

1. **捕获 Azure 资源的 JSON 表示形式。** 如果有现有 JSON 模板，要将其转换为 Bicep，第一步很简单 - 你已经有源模板。 如果要转换通过门户或其他工具部署的 Azure 资源，则需要导出资源定义，并将其转换为 Bicep。 可以使用 Azure 门户、Azure CLI 和 Azure PowerShell cmdlet 导出单个资源、多个资源以及整个资源组。

1. 使用反编译命令将 JSON 表示形式转换为 Bicep。 [Bicep 工具包含用于转换模板的 `decompile` 命令。](decompile.md) 可以从 Azure CLI 或 Bicep CLI 调用 `decompile` 命令。 反编译过程会尽最大程度来完成任务，但不能保证从 JSON 到 Bicep 的完整映射。 在使用文件部署资源之前，你可能需要修改生成的 Bicep 文件以满足模板最佳做法。

## <a name="phase-2-migrate"></a>阶段 2：迁移

在将资源迁移到 Bicep 的迁移阶段，目标是要创建可部署 Bicep 文件的草稿，并确保其可定义迁移范围内的所有 Azure 资源。

迁移阶段包括三个步骤，按顺序完成这些步骤：

1. **创建新的空白 Bicep 文件。** 最好是创建全新的 Bicep 文件。 转换阶段创建的文件是查看的参考点，但不应将其视为最终文件或按原样部署。

1. **从反编译的模板中复制每个资源。** 将已转换的 Bicep 文件中的每个资源逐个复制到新的 Bicep 文件。 此过程有助于逐个解决资源中的任何问题，并避免随着模板大小的增长而出现任何混淆。

1. 标识并重新创建任何缺失的资源。 并非所有 Azure 资源类型都可以通过 Azure 门户、Azure CLI 或 Azure PowerShell 进行导出。 例如，DependencyAgentWindows 和 MMAExtension（Microsoft Monitoring Agent）等虚拟机扩展就是不支持导出的资源类型。 对于未导出的任何资源（例如虚拟机扩展），需要在新的 Bicep 文件中重新创建这些资源。 可以使用多种工具和方法来重新创建资源，包括 [Azure 资源浏览器](/azure/azure-resource-manager/templates/view-resources?azure-portal=true#use-resource-explorer)、[Bicep 和 ARM 模板参考文档](/azure/templates/?azure-portal=true)以及 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates?azure-portal=true)网站。

## <a name="phase-3-refactor"></a>阶段 3：重构

在将资源迁移到 Bicep 的重构阶段，目标是提高 Bicep 代码的质量。 这些改进可以包括更改（如添加代码注释），让模板符合模板标准。

此部署阶段由八个步骤组成，可以按任意顺序完成这些步骤：

1. **查看资源 API 版本。** 导出 Azure 资源时，导出的模板可能没有资源类型的最新 API 版本。 如果将来部署需要特定属性，请更新 API 到相应的版本。 最好查看每个导出资源的 API 版本。

1. **在新的 Bicep 文件中查看 linter 建议。** 使用[适用于 Visual Studio Code 的 Bicep 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep&azure-portal=true)创建 Bicep 文件时，[Bicep linter](linter.md) 会自动运行，并突出显示代码中的建议和错误。 许多建议和错误都包含对问题应用快速修复的选项。 查看这些建议并调整 Bicep 文件。

1. 修改参数、变量和符号名称。 反编译程序生成的参数、变量名称和符号名称可能与标准命名约定不匹配。 审阅生成的名称并根据需要进行调整。

1. 简化表达式。 反编译过程并非总是能充分利用一些 Bicep 的功能。 审阅转换中生成的任何表达式，并对其进行简化。 例如，反编译的模板可能包含 `concat()` 或 `format()` 函数，可以通过使用[字符串内插](bicep-functions-string.md#concat)来简化这些函数。 查看来自 linter 的所有建议，并根据需要进行调整。

1. 审阅子资源和扩展资源。 使用 Bicep，有多种方法可以声明[子资源](child-resource-name-type.md)和[扩展资源](scope-extension-resources.md)，包括串联资源名称、使用 `parent` 关键字和使用嵌套资源。 请考虑在反编译后查看这些资源，并确保结构满足标准。 例如，请确保不要使用字符串串联来创建子资源名称，而应使用 `parent` 属性或嵌套资源。 同样，子网可以作为虚拟网络的属性来引用，也可以作为单独的资源进行引用。

1. 模块化。 如果要转换包含多个资源的模板，请考虑将各个资源类型分解为[模块](modules.md)，以达到简化的目的。 Bicep 模块有助于降低部署的复杂性，提高 Bicep 代码的可再用性。

    > [!NOTE]
    > 在 Bicep 部署中，可以使用 JSON 模板作为模块。 Bicep 可以识别 JSON 模块，并以类似于使用 Bicep 模块的方式引用它们。

1. 添加注释和说明。 好的 Bicep 代码是自文档化的。 Bicep 允许向代码添加注释和 `@description()` 属性，以帮助记录基础结构。 Bicep 支持使用 `//` 字符序列的单行注释和以 `/*` 开头并以 `*/` 结尾的多行注释。 可以对代码中的特定行和代码段添加注释。

1. 遵循 Bicep 最佳做法。 请确保 Bicep 文件遵循标准建议。 查看 [Bicep 最佳做法](best-practices.md)参考文档，了解可能丢失的任何内容。

## <a name="phase-4-test"></a>阶段 4：测试

在将资源迁移到 Bicep 的测试阶段，目标是要验证已迁移模板的完整性并执行测试部署。

测试阶段包括两个步骤，需要按顺序完成：

1. **运行 ARM 模板部署 What-if 操作。** 为了帮助在部署之前验证转换后的模板，你可以使用 [Azure 资源管理器模板部署 What-if 操作](../templates/deploy-what-if.md)。 它将环境的当前状态与模板中定义的理想状态进行比较。 该工具会输出将发生的变化的列表，但不会将变化应用到你的环境。 你可以对增量和完整模式部署都使用 What-if 分析。 即使计划使用增量模式部署模板，最好也是在完整模式下运行 What-if 操作。

1. **执行测试部署。** 在将转换后的 Bicep 模板引入生产环境之前，请考虑运行多个测试部署。 如果有多个环境（例如开发、测试和生产），可能需要先尝试将模板部署到其中一个非生产环境。 部署后，将原始资源与新的资源部署进行比较，以确保一致性。

## <a name="phase-5-deploy"></a>阶段 5：部署

在将资源迁移到 Bicep 的部署阶段，目标是将最终的 Bicep 文件部署到生产环境。

部署阶段由四个步骤组成，需要按顺序完成这些步骤：

1. **准备回滚计划。** 从失败的部署中恢复的能力至关重要。 制定回滚计划，以防环境发生任何中断性变更。 清点已部署的资源类型，例如虚拟机、Web 应用和数据库。 还应考虑每个资源的数据平面。 是否具有恢复虚拟机及其数据的方法？ 是否有办法恢复删除后的数据库？ 如果部署出现任何问题，制定良好的回滚计划有助于将停机时间降至最低。

1. **针对生产运行 What-if 操作。** 将最终的 Bicep 文件部署到生产环境之前，请针对生产环境运行 What-if 操作，确保使用生产参数值，并考虑记录结果。

1. 手动部署。 如果要在管道（例如 [Azure DevOps](add-template-to-azure-pipelines.md) 或 [GitHub Actions](deploy-github-actions.md)）中使用转换后的模板，请考虑先从本地计算机运行部署。 最好先验证模板的功能，然后再将模板添加到生产管道。 这样，就可以在出现问题时快速响应。

1. 运行冒烟测试。 部署完成后，运行一系列冒烟测试是一个不错的选择，简单的检查可验证应用程序或工作负载是否正常工作。 例如，测试 Web 应用是否可通过正常访问渠道访问，例如公共 Internet 或通过公司 VPN。 对于数据库，尝试建立数据库连接并执行一系列查询。 对于虚拟机，登录到虚拟机并确保所有服务都已启动并正在运行。

## <a name="next-steps"></a>后续步骤

若要详细了解 Bicep 反编译程序，请参阅[将 ARM 模板 JSON 反编译为 Bicep](decompile.md)。
