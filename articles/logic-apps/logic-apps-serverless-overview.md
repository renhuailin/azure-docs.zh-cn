---
title: 更多的应用开发，更少的基础结构管理
description: Azure 无服务器可帮助你专注于创建基于云的应用，同时在使用 Azure 逻辑应用和 Azure Functions 时减少管理基础结构的支出
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 335a553897f2487eef317852f23cd475a92b2f81
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362673"
---
# <a name="azure-serverless-overview-create-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure 无服务器概述：使用 Azure 逻辑应用和 Azure Functions 创建基于云的应用和解决方案

虽然无服务器并不意味着“没有服务器”，但 Azure 无服务器可帮助你减少管理基础结构方面的支出。 在传统的应用开发中，你可能会花费大量时间和精力来讨论和解决托管、缩放和监视解决方案，以满足应用要求和需求。 通过使用无服务器应用和解决方案，可以在应用或解决方案中更轻松地处理这些问题。 无服务器还具有其他优势，例如开发速度更快、代码更少、简单性和缩放灵活性。 所有这些功能都使你可以专注于业务逻辑。 此外，无服务器通常根据使用情况计费或收费。 因此，如果没有使用，就不会产生费用。 有关更多信息，请详细阅读 [Azure 无服务器](https://azure.microsoft.com/solutions/serverless/)。

本文简要介绍了 Azure 中的核心无服务器产品/服务，即 Azure 逻辑应用和 Azure Functions。 这两种服务都符合前面所述的原则，可帮助你使用尽可能少的代码构建可靠的云应用和解决方案。

有关更多介绍性信息，请访问 [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)和 [Azure Functions](https://azure.microsoft.com/services/functions/) 的 Azure 页面。 有关更多详细信息，请参阅[什么是 Azure 逻辑应用](logic-apps-overview.md)和[什么是 Azure Functions](../azure-functions/functions-overview.md) 文档页。

## <a name="azure-logic-apps"></a>Azure 逻辑应用

此服务提供简化的方式来设计、开发和编排在云中运行和缩放的自动化事件驱动集成工作流。 借助 Azure 逻辑应用，可以使用可视化设计器将业务流程快速建模为工作流。 工作流始终以触发器作为第一步。 在触发器后，一个或多个操作在工作流中运行后续操作。 这些操作可以包含各种操作组合，包括条件逻辑和数据转换。

若要在不编写任何代码的情况下将工作流连接到其他 Azure 服务、Microsoft 服务、基于云的环境和本地环境，可以通过从[数百个连接器](/connectors/connector-reference/connector-reference-logicapps-connectors/)（均由 Microsoft 管理）中进行选择，将预生成的触发器和操作添加到工作流中。 每个连接器实际上是一个代理或封装 API 的包装器，基础服务通过它与 Azure 逻辑应用通信。 例如，Office 365 Outlook 连接器提供名为“新电子邮件到达时”的触发器。 对于无服务器应用和解决方案，可以使用 Azure 逻辑应用来编排在 Azure Functions 中创建的多个函数。 通过此操作，可以轻松地将各种函数作为单个进程调用，尤其是当该进程需要使用外部 API 或系统时。

如果没有连接器可以满足你的需求，可以使用内置的 HTTP 操作或请求触发器来与任何服务终结点通信。 或者，可以使用现有 API 创建自己的连接器。

根据选择的逻辑应用资源类型，关联的工作流在多租户 Azure 逻辑应用、单租户 Azure 逻辑应用或专用集成服务环境 (ISE) 中运行。 每种方法都有自己的功能、优势和计费模型。 Azure 门户提供了开始创建逻辑应用工作流的最快方法。 但是，你也可以使用其他工具，例如 Visual Studio Code、Visual Studio、Azure PowerShell 等。 有关详细信息，请参阅[什么是 Azure 逻辑应用](logic-apps-overview.md)？

若要开始使用 Azure 逻辑应用，请尝试[使用快速入门以在 Azure 门户中创建第一个逻辑应用工作流](quickstart-create-first-logic-app-workflow.md)。 或者，尝试[在 Visual Studio 中使用 Azure 逻辑应用和 Azure Functions 创建示例无服务器应用的这些步骤](create-serverless-apps-visual-studio.md)。

有关其他信息，请查看以下文档：

* [什么是 Azure 逻辑应用？](logic-apps-overview.md)
* [关于 Azure 逻辑应用中的连接器](../connectors/apis-list.md)
* [连接器 - Azure 逻辑应用、Microsoft Power Automate、Microsoft Power Apps](/connectors/connectors)
* [适用于 Azure 逻辑应用的单租户与多租户和集成服务环境的比较情况](single-tenant-overview-compare.md)
* [适用于 Azure 逻辑应用的使用量计量、计费和定价模型](logic-apps-pricing.md)

## <a name="azure-functions"></a>Azure Functions

此服务提供了一种在云中编写和运行代码或函数片段的简化方法。 可以只编写当前问题所需的代码，而无需设置完整的应用或所需的基础结构，从而加快开发速度并提高工作效率。 使用所选的开发语言，例如 C#、Java、JavaScript、PowerShell、Python 和 TypeScript。 只需为代码运行的时间付费，并且 Azure 会根据需要进行缩放。

若要开始使用 Azure Functions，请尝试[在 Azure 门户中创建第一个 Azure 函数](../azure-functions/functions-create-function-app-portal.md)。

有关其他信息，请查看以下文档：

* [什么是 Azure Functions？](../azure-functions/functions-overview.md)
* [Azure Functions 入门](../azure-functions/functions-get-started.md)
* [Azure Functions 中支持的语言](../azure-functions/supported-languages.md)
* [Azure Functions 主机选项](../azure-functions/functions-scale.md)
* [Azure Functions 定价](../azure-functions/pricing.md)

## <a name="get-started-with-serverless-apps-in-azure"></a>开始使用 Azure 中的无服务器应用

Azure 提供丰富的工具，用于开发、部署和管理无服务器应用。 可以使用 Azure 门户、Visual Studio 或 [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) 创建无服务器应用。 在构建应用之后，可以[使用 Azure 资源管理器模板快速部署该应用](logic-apps-deploy-azure-resource-manager-templates.md)。 Azure 还提供监视功能，该功能可以通过 Azure 门户、API 或 SDK 或适用于 Azure Monitor 日志和 Application Insights 的集成工具来访问。

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中使用 Azure 逻辑应用和 Azure Functions 创建示例无服务器应用](create-serverless-apps-visual-studio.md)
* [使用无服务器产品/服务创建 Customer Insights 仪表板](logic-apps-scenario-social-serverless.md)