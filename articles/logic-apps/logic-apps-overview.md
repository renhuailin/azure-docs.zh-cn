---
title: Azure 逻辑应用概述
description: 逻辑应用是一个云平台，用于构建自动化工作流，以使用最少的代码集成适合企业应用场景的应用、数据、服务和系统。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: overview
ms.custom: mvc
ms.date: 04/26/2021
ms.openlocfilehash: 377abf4809e01c3931de393ff97718e0638c5c36
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065487"
---
# <a name="what-is-azure-logic-apps"></a>什么是 Azure 逻辑应用

[逻辑应用](https://azure.microsoft.com/services/logic-apps)是一种基于云的平台，可通过生成和运行自动化的[工作流](#logic-app-concepts)来帮助你快速轻松地集成应用、数据、系统和服务。 逻辑应用是 [Azure Integration Services](https://azure.microsoft.com/product-categories/integration/) 的一部分，它提供了一种更简单的方法，用于跨云、本地和混合环境为企业和企业对企业 (B2B) 应用场景提供高度可缩放的集成解决方案。

以下列表仅描述了可使用逻辑应用服务自动执行的部分示例任务、业务流程和工作负载：

* 跨本地系统和云服务路由并处理客户订单。
* 当发生特定事件时，使用 Office 365 计划和发送电子邮件通知。
* 将上传的文件从 SFTP 或 FTP 服务器移至 Azure 存储。
* 监视推文，分析观点，针对需要查看的项目创建警报或任务。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Introducing-Azure-Logic-Apps/player]

要安全访问各种数据源并对其实时运行操作，请从 [Microsoft 托管连接器](#logic-app-concepts)的[不断增长的库](/connectors/connector-reference/connector-reference-logicapps-connectors)中进行选择，例如：

* Azure 服务，例如 Blob 存储和服务总线
* Office 服务，例如 Outlook、Excel 和 SharePoint
* 数据库服务器，例如 SQL 和 Oracle
* 企业系统，例如 SAP 和 IBM MQ
* 文件共享，例如 FTP 和 SFTP

要与任何服务终结点通信、运行自己的代码、组织工作流或处理数据，可以使用[内置的触发器和操作](#logic-app-concepts)，它们会在逻辑应用服务中以本机方式运行。 例如，内置触发器包括请求、HTTP 和定期。 内置操作包括 Condition、For each、Execute JavaScript 代码，以及调用 Azure 函数、Azure 中托管的 Web 应用或 API 应用以及其他逻辑应用工作流的操作。

针对 B2B 集成场景，逻辑应用纳入了 [BizTalk Server](/biztalk/core/introducing-biztalk-server) 中的功能。 你可以创建一个[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)在其中定义贸易合作伙伴、协议、架构、地图和其他 B2B 项目。 将此帐户与逻辑应用关联后，可以生成使用这些项目的工作流并使用 AS2、EDIFACT 和 X12 等协议交换消息。

要详细了解工作流可以访问和使用应用、数据、服务和系统的方式，请参阅以下文档：

* [适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
* [适用于 Azure 逻辑应用的托管连接器](../connectors/built-in.md)
* [Azure 逻辑应用的内置触发器和操作](../connectors/managed.md)
* [使用 Azure 逻辑应用的 B2B 企业集成解决方案](logic-apps-enterprise-integration-overview.md)

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>关键术语

* **工作流**：定义任务或流程的一系列步骤，从单个触发器开始，后跟一个或多个操作

* **触发器**：启动每个工作流的第一步，该步骤指定在工作流中运行任何操作之前要满足的条件。 例如，触发事件可能是在收件箱中收到电子邮件，或者在存储帐户中检测到新文件。

* **操作**：触发器后面的每个后续步骤，这些步骤运行工作流中的某个操作

* **托管连接器**：Microsoft 托管的连接器，用于访问特定应用、数据、服务或系统。 大多数托管连接器要求首先从工作流创建连接并验证身份，然后才能使用这些连接器。

  例如，可以使用触发器启动工作流，或包含适用于 Azure Blob 存储、Office 365、Salesforce 或 SFTP 服务器的操作。 有关详细信息，请查看[适用于 Azure 逻辑应用的托管连接器](../connectors/managed.md)。

* **内置触发器或操作**：本机运行的逻辑应用操作，它为你提供了一种方法来控制工作流的计划或结构、运行自己的代码、管理或处理数据或完成工作流中的其他任务。 大多数内置操作不与任何服务或系统相关联。 还有许多操作不要求首先从工作流创建连接并验证身份。 内置操作还适用于一些服务、系统和协议，例如 Azure Functions、Azure API 管理、Azure 应用服务等。

  例如，使用“定期”触发器时，可以启动计划表上的几乎任何工作流。 或者在使用“请求”触发器时，可以让工作流在被调用以前都保持等待状态。 有关详细信息，请查看[适用于 Azure 逻辑应用的内置触发器和操作](../connectors/built-in.md)。

<a name="how-do-logic-apps-work"></a>

## <a name="how-logic-apps-work"></a>逻辑应用的工作原理

在逻辑应用中，每个工作流始终以单个[触发器](#logic-app-concepts)开始。 当满足某个条件时，例如，当发生特定事件时或数据满足特定条件时，将触发触发器。 许多触发器包含用于控制工作流运行频率的[计划功能](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。 在触发器之后，一个或多个[操作](#logic-app-concepts)将运行加工、处理或转换数据等操作，这些操作可以贯穿于工作流，也可以推动工作流进入下一步。

例如，以下工作流从动态触发器开始，该动态触发器具有一个名为“在更新记录时”的内置条件。 这些操作包括转换 XML、调用更新数据的 Web 应用、评估控制要执行的操作的条件，以及使用结果发送电子邮件通知。 当触发器检测到满足条件的事件时，触发器将触发，工作流中的操作将开始运行。 每当触发器触发时，逻辑应用服务都会创建一个运行操作的工作流实例。

![逻辑应用设计器 - 示例工作流](./media/logic-apps-overview/azure-logic-apps-designer.png)

可以在 Azure 门户、Visual Studio 或 Visual Studio Code 中使用逻辑应用设计器以可视方式创建工作流。 每个工作流还具有使用 JavaScript 对象表示法 (JSON) 描述的基础定义。 如果愿意，可以在 Azure 门户、Visual Studio 或 Visual Studio Code 中使用代码视图编辑器来生成和自定义工作流。 对于某些创建和管理任务，逻辑应用提供 Azure PowerShell 和 Azure CLI 命令支持。 对于自动部署，逻辑应用支持 Azure 资源管理器模板。

## <a name="why-use-logic-apps"></a>使用逻辑应用的原因

随着企业逐渐转向数字化，逻辑应用平台应运而生。它可以提供预生成的 API 作为 Microsoft 托管的连接器，从而可以帮助你更轻松快捷地连接旧系统和新式、前沿的系统。 因此，你可以专注于应用的业务逻辑和功能， 不需担心应用的生成、托管、缩放、管理、维护和监视。 逻辑应用为你解决这一切。 另外，只需根据使用情况付费，具体取决于使用量[定价模型](../logic-apps/logic-apps-pricing.md)。

通常，无需编写任何代码。 但如果必须编写一些代码，则可使用 [Azure Functions](../azure-functions/functions-overview.md) 创建代码片段，然后通过工作流按需运行该代码。 另外，如果工作流需要与来自 Azure 服务、自定义应用或其他解决方案的事件交互，则可将 [Azure 事件网格](../event-grid/overview.md)与工作流配合使用，以便进行事件监视、路由和发布。

逻辑应用、Functions 和事件网格由 Microsoft Azure 全权托管，因此不必担心解决方案的生成、托管、缩放、管理、监视和维护。 由于能够创建[“无服务器”应用和解决方案](../logic-apps/logic-apps-serverless-overview.md)，因此只需关注业务逻辑。 这些服务可以按需自动缩放，加快集成速度，使用最少的代码生成可靠的云应用。

若要了解公司如何将逻辑应用与其他 Azure 服务和 Microsoft 产品配合使用，以便增强敏捷性并更加专注于核心业务，请查看这些[客户案例](https://aka.ms/logic-apps-customer-stories)。

下面更详细地介绍逻辑应用的功能和好处：

### <a name="visually-build-workflows-with-easy-to-use-tools"></a>使用易用的工具直观地生成工作流

使用可视化设计工具，既节省时间，又能简化复杂的流程。 在 Azure 门户中或 Visual Studio 中通过浏览器从头至尾使用逻辑应用设计器来生成工作流。 使用触发器启动工作流，并从[连接器库](../connectors/apis-list.md)添加任意数量的操作。

### <a name="get-started-faster-with-logic-apps-templates"></a>使用逻辑应用模板加快入门速度

从[模板库](../logic-apps/logic-apps-create-logic-apps-from-templates.md)选择预定义的工作流时，可以更快速地创建常用解决方案。 模板既有适用于软件即服务 (SaaS) 应用的简单连接，也有高级 B2B 解决方案，还有“兴趣型”模板。 了解如何[从预生成的模板创建逻辑应用](../logic-apps/logic-apps-create-logic-apps-from-templates.md)。

### <a name="connect-disparate-systems-across-different-environments"></a>跨不同的环境连接不同的系统

某些模式和流程描述起来容易，但难以在代码中实现。 逻辑应用工作流可用于跨本地环境和云环境无缝连接不同的系统。 例如，可以将云营销解决方案连接到本地计费系统，也可以使用企业服务总线集中进行跨 API 和系统的消息传送。 逻辑应用平台提供了一种快速、可靠且一致的方式来为这些场景提供可重复使用和可重新配置的解决方案。

### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>针对企业集成和 B2B 方案的一流支持

企业和组织使用符合行业标准但却不同的消息协议和格式（例如 EDIFACT、AS2 和 X12）进行电子方式的相互通信。 可以使用 [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md) 中的功能来生成工作流，以便将合作伙伴使用的消息格式转换成组织的系统能够解释和处理的格式。 逻辑应用可以通过加密和数字签名顺利且安全地处理这些交换。

一开始采用小规模，只需使用当前的系统和服务，然后按自己的步调逐渐扩大规模。 准备就绪以后，即可使用逻辑应用和 EIP 来获得以下功能和其他功能，从而实现集成方案并通过纵向扩展将其变为更成熟的方案：

* 在以下产品和服务的基础上进行生成：

  * [Microsoft BizTalk Server](/biztalk/core/introducing-biztalk-server)
  * [Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)
  * [Azure Functions](../azure-functions/functions-overview.md)
  * [Azure API 管理](../api-management/api-management-key-concepts.md)

* 处理 [XML 消息](../logic-apps/logic-apps-enterprise-integration-xml.md)

* 处理[平面文件](../logic-apps/logic-apps-enterprise-integration-flatfile.md)

* 使用 [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)、[AS2](../logic-apps/logic-apps-enterprise-integration-as2.md) 和 [X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 协议交换消息

* 使用[集成帐户](./logic-apps-enterprise-integration-create-integration-account.md)在一个位置存储和管理以下 B2B 项目和其他项目：

  * [合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)
  * [协议](../logic-apps/logic-apps-enterprise-integration-agreements.md) 
  * [XML 转换映射](../logic-apps/logic-apps-enterprise-integration-maps.md)
  * [XML 验证架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)

例如，如果使用 Microsoft BizTalk Server，则逻辑应用可以使用 [BizTalk Server 连接器](../connectors/managed.md#on-premises-connectors)与 BizTalk Server 通信。 然后，可以通过包含[集成帐户连接器](../connectors/managed.md#integration-account-connectors)（已在 Enterprise Integration Pack 中提供），在工作流中进行扩展或运行类似于 BizTalk 的操作。

从另一个方向看，BizTalk Server 可以使用[适用于逻辑应用的 Microsoft BizTalk Server 适配器](https://www.microsoft.com/download/details.aspx?id=54287)连接到逻辑应用并与其通信。 了解如何在 BizTalk Server 中[设置和使用 BizTalk 服务器适配器](/biztalk/core/logic-app-adapter)。

### <a name="write-once-reuse-often"></a>编写一次即可多次重复使用

将逻辑应用创建为 Azure 资源管理器模板，以便可以跨多个环境和区域[自动执行逻辑应用部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。

### <a name="access-resources-inside-azure-virtual-networks"></a>访问 Azure 虚拟网络中的资源

当你创建[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 时，逻辑应用可访问[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)中的受保护资源，例如虚拟机 (VM) 以及其他系统或服务。 ISE 是逻辑应用服务的专用实例，该实例使用专用资源并独立于“全局”多租户逻辑应用服务运行。

在你自己的专用实例中运行逻辑应用有助于降低其他 Azure 租户对应用性能的影响，此影响也称为[“邻近干扰”影响](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)。 ISE 还带来以下好处：

* 你自己的静态 IP 地址，它们不同于多租户服务中的逻辑应用共享的静态 IP 地址。 还可以设置单个公共的、静态的和可预测的出站 IP 地址，以便与目标系统通信。 这样就无需在每个 ISE 的那些目标系统上设置额外的防火墙开口。

* 增加了对运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求的限制。 有关详细信息，请参阅 [Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md)。

当你创建 ISE 时，Azure 会将该 ISE 注入或部署到 Azure 虚拟网络中。 然后，你可以将该 ISE 用作需要进行访问的逻辑应用和集成帐户的位置。 若要详细了解如何创建 ISE，请参阅[从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

### <a name="built-in-extensibility"></a>内置的扩展性

如果找不到所需的连接器以运行自定义代码，则可通过 [Azure Functions](../azure-functions/functions-overview.md) 根据需要创建和调用自己的代码片段，从而扩展逻辑应用。 创建自己的 [API](../logic-apps/logic-apps-create-api-app.md) 和[自定义连接器](../logic-apps/custom-connector-overview.md)，以便通过逻辑应用对其进行调用。

### <a name="pay-only-for-what-you-use"></a>只需为使用的服务付费
  
逻辑应用使用基于使用情况的[定价和计费](../logic-apps/logic-apps-pricing.md)，除非该逻辑应用是以前使用应用服务计划创建的。

通过以下介绍性视频详细了解逻辑应用：

* [Integration with Logic Apps - Go from zero to hero](https://channel9.msdn.com/Events/Build/2017/C9R17)（与逻辑应用集成 - 从无到有）
* [Enterprise integration with Microsoft Azure Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2188)（与 Microsoft Azure 逻辑应用进行企业集成）
* [Building advanced business processes with Logic Apps](https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3179)（使用逻辑应用生成高级业务流程）

## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>逻辑应用与 Functions、WebJobs 及 Power Automate 的区别在哪里？

所有这些服务都可以用来将不同的系统“粘贴”和连接到一起。 每项服务都有其优点和优势，因此若要快速生成可缩放且功能完备的集成系统，最好的方法是将这些服务的功能组合到一起。 有关详细信息，请参阅[在逻辑应用、Functions、WebJobs 和 Power Automate 之间进行选择](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)。

## <a name="get-started"></a>入门

逻辑应用是托管在 Microsoft Azure 上的许多服务之一。 因此在开始之前，你需要一个 Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

如果有 Azure 订阅，可以尝试此[创建第一个逻辑应用的快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md)。该逻辑应用通过 RSS 源监视网站上的新内容，在新内容出现时发送电子邮件。

## <a name="next-steps"></a>后续步骤

* [使用按计划的逻辑应用检查流量](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
* 详细了解 [Azure 的无服务器解决方案](../logic-apps/logic-apps-serverless-overview.md)
* 详细了解[通过 Enterprise Integration Pack 进行的 B2B 集成](../logic-apps/logic-apps-enterprise-integration-overview.md)
