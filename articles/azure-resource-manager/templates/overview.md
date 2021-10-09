---
title: 模板概述
description: 介绍使用 Azure 资源管理器模板（ARM 模板）部署资源的好处。
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 02731a2f37fda2758c9b9e9980b56a5017ed3506
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637568"
---
# <a name="what-are-arm-templates"></a>什么是 ARM 模板？

在迁移到云的过程中，许多团队都采用了敏捷开发方法。 这些团队的工作快速迭代。 他们需要反复将其解决方案部署到云，并需要知道其基础结构处于一种可靠的状态。 随着基础结构成为迭代过程的一部分，运营与开发之间的划分已经消失。 团队需要通过统一的过程来管理基础结构和应用程序代码。

为了解决这些难题，可将部署自动化，并运用基础结构即代码。 在代码中定义需要部署的基础结构。 基础结构代码将成为项目的一部分。 与应用程序代码一样，可将基础结构代码存储在源存储库中，并控制其版本。 团队中的任何人都可以运行该代码并部署类似的环境。

若要针对 Azure 解决方案实现基础结构即代码，请使用 Azure 资源管理器模板（ARM 模板）。 该模板是一个定义项目基础结构和配置的 JavaScript 对象表示法 (JSON) 文件。 该模板使用声明性语法，使你可以指明要部署的内容，而不需要编写一系列编程命令来创建内容。 在该模板中，指定要部署的资源以及这些资源的属性。

我们引入了一种名为“Bicep”的新语言，用于开发 ARM 模板 JSON。 Bicep 文件和 JSON 模板提供的功能相同。 你可以在两种语言之间转换模板。 Bicep 提供的语法更容易用于创建模板。 有关详细信息，请参阅[什么是 Bicep？](../bicep/overview.md)。

若要了解如何开始使用 ARM 模板，请参阅以下视频。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Enablement/How-and-why-to-learn-about-ARM-templates/player]

## <a name="why-choose-arm-templates"></a>为什么选择 ARM 模板？

如果要尝试在使用 ARM 模板和其他基础结构即代码服务之间做出选择，请考虑使用模板的以下优点：

* **声明性语法**：ARM 模板允许以声明方式创建和部署整个 Azure 基础结构。 例如，你不仅可以部署虚拟机，还可以部署网络基础结构、存储系统和可能需要的任何其他资源。

* **可反复效果**：在整个开发生命周期内反复部署基础结构，并确保以一致的方式部署资源。 模板是幂等的，这意味着，可以多次部署同一模板，并获得处于相同状态的相同资源类型。 可以开发一个模板来表示所需的状态，而无需开发大量的独立模板来表示更新。

* **业务流程**：无需担心有序操作的复杂性。 资源管理器会协调相互依赖的资源的部署，以按正确的顺序创建这些资源。 在可能的情况下，资源管理器将会并行部署资源，因此，其完成速度比串行部署更快。 通过一个命令部署模板，而无需使用多个强制性命令。

   ![模板部署的比较](./media/overview/template-processing.png)

* **模块化文件**：可将模板分解为较小的可重用组件，并在部署时将其链接到一起。 还可以在一个模板中嵌套另一个模板。

* **创建任何 Azure 资源**：可以立即在模板中使用新的 Azure 服务和功能。 一旦资源提供程序引入了新资源，你就可以通过模板立即部署这些资源。 在使用新服务之前，无需等待工具或模块完成更新。

* **扩展性**：使用 [部署脚本](deployment-script-template.md)时，可以将 PowerShell 或 Bash 脚本添加到模板。 使用部署脚本可扩展在部署过程中设置资源的功能。 脚本可以包含在模板中，也可存储在外部源中并在模板中引用。 使用部署脚本可以在单个 ARM 模板中完成端到端环境设置。

* **测试**：可以使用 ARM 模板工具包 (arm-ttk) 对模板进行测试，确保模板符合建议的准则。 此测试工具包是一个 PowerShell 脚本，可从 [GitHub](https://github.com/Azure/arm-ttk) 下载。 使用此工具包，可以更轻松地使用模板语言开发专门技术。

* **预览更改**：可以使用 [模拟操作](./deploy-what-if.md)在部署模板前预览更改。 通过 What-if，你可查看将创建、更新或删除的资源，以及将更改的任何资源属性。 模拟操作会检查环境的当前状态，因此无需管理状态。

* **内置验证**：只有在通过验证后才会部署模板。 资源管理器在开始部署之前会检查模板，以确保部署成功。 部署不太可能会在半完成状态时停止。

* **受跟踪的部署**：在 Azure 门户中，可以查看部署历史记录并获取有关模板部署的信息。 可以查看已部署的模板、已传入的参数值，以及任何输出值。 其他基础结构即代码服务不是通过门户跟踪的。

   ![部署历史记录](./media/overview/deployment-history.png)

* **策略即代码**：[Azure Policy](../../governance/policy/overview.md) 是一个用于自动化监管的策略即代码框架。 如果使用 Azure 策略，在通过模板进行部署时，将会针对不合规的资源执行策略修正。

* **部署蓝图**：你可以利用 Microsoft 提供的 [蓝图](../../governance/blueprints/overview.md)来达到监管和合规性标准。 这些蓝图包括各种体系结构的预生成模板。

* **CI/CD 集成**：可以将模板集成到持续集成和持续部署 (CI/CD) 工具中，这些工具可以自动执行发布管道，以实现快速可靠的应用程序和基础结构更新。 通过 Azure DevOps 和资源管理器模板任务，可以使用 Azure Pipelines 持续生成和部署 ARM 模板项目。 若要了解详细信息，请参阅[使用管道的 VS 项目](add-template-to-azure-pipelines.md)和[教程：使用 Azure Pipelines 持续集成 Azure 资源管理器模板](./deployment-tutorial-pipeline.md)。

* **可导出的代码**：可以通过导出资源组的当前状态或查看特定部署所用的模板，来获取现有资源组的模板。 查看[导出的模板](export-template-portal.md)是了解模板语法的有用方法。

* **创作工具**：可以使用 [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md) 和模板工具扩展来创作模板。 你将获得 Intellisense、语法突出显示、内联帮助以及其他许多语言功能。 除了 Visual Studio Code 以外，还可以使用 [Visual Studio](create-visual-studio-deployment-project.md)。

## <a name="template-file"></a>模板文件

在模板中，可以编写[模板表达式](template-expressions.md)来扩展 JSON 的功能。 这些表达式使用资源管理器提供的[函数](template-functions.md)。

模板包含以下节：

* [参数](./parameters.md) - 在部署过程中提供值，以便可将同一模板用于不同的环境。

* [变量](./variables.md) - 定义在模板中重复使用的值。 可以从参数值构造变量。

* [用户定义的函数](./user-defined-functions.md) - 创建自定义函数用于简化模板。

* [资源](resource-declaration.md) - 指定要部署的资源。

* [输出](./outputs.md) - 从已部署的资源返回值。

## <a name="template-deployment-process"></a>模板部署过程

部署模板时，资源管理器会将模板转换为 REST API 操作。 例如，当资源管理器收到具有以下资源定义的模板：

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-04-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "StorageV2",
    "properties": {}
  }
]
```

它会将该定义转换为以下 REST API 操作，然后，该操作将发送到 Microsoft.Storage 资源提供程序：

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2019-04-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "StorageV2",
  "properties": {}
}
```

请注意，在资源模板中设置的 apiVersion 将用作 REST 操作的 API 版本。 你可以重复部署模板并可以相信它会继续工作。 通过使用相同的 API 版本，你就无需担心可能在更高版本中引入的中断性变更。

若要部署模板，请使用以下任一选项：

* [Azure 门户](deploy-portal.md)
* [Azure CLI](deploy-cli.md)
* [PowerShell](deploy-powershell.md)
* [REST API](deploy-rest.md)
* [GitHub 存储库中的按钮](deploy-to-azure-button.md)
* [Azure Cloud Shell](deploy-cloud-shell.md)

## <a name="template-design"></a>模板设计

模板和资源组的定义方式全由你决定，解决方案的管理方式也是如此。 例如，可以通过单个模板在单个资源组中部署三层式应用程序。

![三层模板](./media/overview/3-tier-template.png)

但无需在单个模板中定义整个基础结构。 通常，合理的做法是将部署要求划分成一组有针对性的模板。 可以轻松地将这些模板重复用于不同的解决方案。 若要部署特定的解决方案，请创建链接所有所需模板的主模板。 下图显示如何通过包含三个嵌套模板的父模板部署三层式解决方案。

![嵌套层模板](./media/overview/nested-tiers-template.png)

如果希望层具有不同的生命周期，可将这三个层部署到不同的资源组。 请注意，资源仍可链接到其他资源组中的资源。

![层模板](./media/overview/tier-templates.png)

有关嵌套模板的信息，请参阅[将链接模板与 Azure 资源管理器配合使用](linked-templates.md)。

## <a name="share-templates"></a>共享模板

创建模板后，你可能希望与组织中的其他用户共享此模板。 [模板规格](template-specs.md)使你能够将模板存储为资源类型。 可使用基于角色的访问控制来管理对模板规格的访问。具有对模板规格的读取访问权限的用户可以部署它，但无法更改模板。

此方法意味着可以安全地共享符合组织标准的模板。

## <a name="next-steps"></a>后续步骤

* 有关引导你完成模板创建过程的分步教程，请参阅[教程：创建和部署第一个 ARM 模板](template-tutorial-create-first-template.md)。
* 要通过 Microsoft Learn 上的一组引导式模块了解 ARM 模板，请参阅[使用 ARM 模板在 Azure 中部署和管理资源](/learn/paths/deploy-manage-resource-manager-templates/)。
* 有关模板文件中的属性的信息，请参阅[了解 ARM 模板的结构和语法](./syntax.md)。
* 若要了解如何导出模板，请参阅[快速入门：使用 Azure 门户创建和部署 ARM 模板](quickstart-create-templates-use-the-portal.md)。
* 有关常见问题的解答，请参阅[有关 ARM 模板的常见问题解答](./frequently-asked-questions.yml)。
