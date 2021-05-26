---
title: Azure 逻辑应用概述
description: Azure 逻辑应用是一个云平台，用于自动化工作流，以便在几乎不使用代码的情况下集成应用、数据、服务和系统。 工作流可以在多租户、单租户或专用环境中运行。
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: overview
ms.custom: mvc
ms.date: 05/07/2021
ms.openlocfilehash: c971f184aad7bfaf6d36a00ec1fb95f474c3a61c
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376929"
---
# <a name="what-is-azure-logic-apps"></a>什么是 Azure 逻辑应用？

[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps)是一个基于云的平台，用于创建和运行集成应用、数据、服务和系统的自动化[工作流](#logic-app-concepts)。 借助此平台，可以快速为企业和企业到企业 (B2B) 方案开发高度可缩放的集成解决方案。 作为 [Azure Integration Services](https://azure.microsoft.com/product-categories/integration/) 的成员，逻辑应用简化了跨云、本地和混合环境连接旧式、新式和一流系统的方式。

以下列表仅描述了可使用逻辑应用服务自动执行的部分示例任务、业务流程和工作负载：

* 当发生特定事件（例如上传新文件时）使用 Office 365 计划并发送电子邮件通知。
* 跨本地系统和云服务路由并处理客户订单。
* 将上传的文件从 SFTP 或 FTP 服务器移至 Azure 存储。
* 监视推文，分析观点，针对需要查看的项目创建警报或任务。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Introducing-Azure-Logic-Apps/player]

根据选择和创建的逻辑应用资源类型，逻辑应用在多租户、单租户或专用集成服务环境中运行。 例如，对基于单租户的逻辑应用进行容器化时，可以将应用部署为容器，并可在 Azure Functions 可以运行的任何地方运行它们。 有关详细信息，请查看[逻辑应用的资源类型和主机环境差异](#resource-environment-differences)。

若要安全地实时访问并运行各种数据源上的操作，可以从 [400 多个且不断增长的 Azure 连接器生态系统](/connectors/connector-reference/connector-reference-logicapps-connectors)中选择[托管连接器](#logic-app-concepts)，以在工作流中使用，例如：

* Azure 服务，例如 Blob 存储和服务总线
* Office 365 服务，例如 Outlook、Excel 和 SharePoint
* 数据库服务器，例如 SQL 和 Oracle
* 企业系统，例如 SAP 和 IBM MQ
* 文件共享，例如 FTP 和 SFTP

要与任何服务终结点通信、运行自己的代码、组织工作流或处理数据，可以使用[内置的](#logic-app-concepts)触发器和操作，它们会在逻辑应用服务中以本机方式运行。 例如，内置触发器包括请求、HTTP 和定期。 内置操作包括 Condition、For each、Execute JavaScript 代码，以及调用 Azure 函数、Azure 中托管的 Web 应用或 API 应用以及其他逻辑应用工作流的操作。

针对 B2B 集成场景，逻辑应用纳入了 [BizTalk Server](/biztalk/core/introducing-biztalk-server) 中的功能。 若要定义企业到企业 (B2B) 项目，请创建用于存储这些项目的[集成帐户](#logic-app-concepts)。 将该帐户链接到逻辑应用后，工作流可以使用这些 B2B 项目并交换符合电子数据交换 (EDI) 和企业应用程序集成 (EAI) 标准的消息。

要详细了解工作流可以访问和使用应用、数据、服务和系统的方式，请参阅以下文档：

* [适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
* [适用于 Azure 逻辑应用的托管连接器](../connectors/built-in.md)
* [Azure 逻辑应用的内置触发器和操作](../connectors/managed.md)
* [使用 Azure 逻辑应用的 B2B 企业集成解决方案](logic-apps-enterprise-integration-overview.md)

<a name="logic-app-concepts"></a>

## <a name="key-terms"></a>关键术语

* 逻辑应用：想要开发工作流时要创建的 Azure 资源。 根据你的方案需求和解决方案要求，你可以创建在多租户 Azure 逻辑应用、单租户 Azure 逻辑应用或集成服务环境 (ISE) 中运行的逻辑应用。 有关详细信息，请查看[逻辑应用的资源类型和主机环境差异](#resource-environment-differences)。

* 工作流：定义任务或流程的一系列步骤，从单个触发器开始，后跟一个或多个操作。

* *触发器*：启动每个工作流的第一步，该步骤指定在工作流中运行任何操作之前要满足的条件。 例如，触发事件可能是在收件箱中收到电子邮件，或者在存储帐户中检测到新文件。

* 操作：触发器后面的每个后续步骤，这些步骤运行工作流中的某个操作。

* *内置触发器或操作*：本机运行的逻辑应用操作，它为你提供了一种方法来控制工作流的计划或结构、运行自己的代码、管理或处理数据或完成工作流中的其他任务。 大多数内置操作不与任何服务或系统相关联。 还有许多操作不要求首先从工作流创建连接并验证身份。 但是内置操作还适用于一些常用服务、系统和协议，例如 Azure Functions、Azure API 管理、Azure 应用服务等。

  例如，使用“定期”触发器时，可以启动计划表上的几乎任何工作流。 或者在使用“请求”触发器时，可以让工作流在被调用以前都保持等待状态。 有关详细信息，请查看[适用于 Azure 逻辑应用的内置触发器和操作](../connectors/built-in.md)。

* 托管连接器：围绕 REST API 预生成的代理或包装器，为工作流提供预先生成的触发器和操作，以访问特定应用、数据、服务或系统。 首先从工作流创建连接并验证身份，然后才能使用大多数托管连接器。

  例如，可以使用触发器启动工作流，或添加适用于 Azure Blob 存储、Office 365、Salesforce 或 SFTP 服务器的操作。 托管连接器由 Microsoft 托管和维护。 有关详细信息，请查看[适用于 Azure 逻辑应用的托管连接器](../connectors/managed.md)。

* 集成帐户：想要定义和存储 B2B 项目以便在在工作流中使用时要创建的 Azure 资源。 将该帐户链接到逻辑应用后，工作流可以使用这些 B2B 项目并交换符合电子数据交换 (EDI) 和企业应用程序集成 (EAI) 标准的消息。

  例如，你可以定义参与方、协议、架构、地图和其他 B2B 项目。 你可以创建使用这些项目的工作流，并通过协议（如 AS2、EDIFACT、X12 和 RosettaNet）交换消息。 有关详细信息，请查看[创建和管理 B2B 企业集成的集成帐户](logic-apps-enterprise-integration-create-integration-account.md)。

<a name="how-do-logic-apps-work"></a>

## <a name="how-logic-apps-work"></a>逻辑应用的工作原理

在逻辑应用中，每个工作流始终以单个[触发器](#logic-app-concepts)开始。 当满足某个条件时，例如，当发生特定事件时或数据满足特定条件时，将触发触发器。 许多触发器包含用于控制工作流运行频率的[计划功能](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。 在触发器之后，一个或多个[操作](#logic-app-concepts)将运行加工、处理或转换数据等操作，这些操作可以贯穿于工作流，也可以推动工作流进入下一步。

例如，以下工作流从动态触发器开始，该动态触发器具有一个名为“在更新记录时”的内置条件。 这些操作包括转换 XML、调用更新数据的 Web 应用、评估控制要执行的操作的条件，以及使用结果发送电子邮件通知。 当触发器检测到满足条件的事件时，触发器将触发，工作流中的操作将开始运行。 每当触发器触发时，逻辑应用服务都会创建一个运行操作的工作流实例。

![逻辑应用设计器 - 示例工作流](./media/logic-apps-overview/azure-logic-apps-designer.png)

可以在 Azure 门户、Visual Studio Code 或 Visual Studio 中使用逻辑应用设计器以可视方式创建工作流。 每个工作流还具有使用 JavaScript 对象表示法 (JSON) 描述的基础定义。 如果愿意，可更改此 JSON 定义来编辑工作流。 对于某些创建和管理任务，逻辑应用提供 Azure PowerShell 和 Azure CLI 命令支持。 对于自动部署，逻辑应用支持 Azure 资源管理器模板。

<a name="resource-environment-differences"></a>

## <a name="resource-type-and-host-environment-differences"></a>资源类型和主机环境差异

若要创建逻辑应用工作流，请根据你的方案、解决方案要求、所需功能以及要在其中运行工作流的环境来选择逻辑应用资源类型。

下表简要概述了原始逻辑应用（消耗版）资源类型和逻辑应用（标准版）资源类型之间的差异 。 你还将了解单租户模型与多租户和集成服务环境 (ISE) 模型在部署、托管和运行逻辑应用工作流方面的比较情况  。

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

## <a name="why-use-logic-apps"></a>使用逻辑应用的原因

逻辑应用集成平台提供预构建的 Microsoft 托管 API 连接器和内置操作，让你能够更轻松快速地连接和集成应用、数据、服务和系统。 可以更专注于设计和实现解决方案的业务逻辑和功能，而不是弄清楚如何访问资源。

通常不需要编写任何代码。 但是，如果需要编写代码，可以使用 [Azure Functions](../azure-functions/functions-overview.md) 创建代码片段，然后从工作流运行该代码。 还可使用[内联代码操作](logic-apps-add-run-inline-code.md)创建在工作流中运行的代码片段。 如果工作流需要与来自 Azure 服务、自定义应用或其他解决方案的事件进行交互，可以使用 [Azure 事件网格](../event-grid/overview.md)监视、路由和发布事件。

逻辑应用由 Microsoft Azure 全权托管，因此不必担心托管、缩放、管理、监视和维护用这些服务构建的解决方案。 使用这些功能创建[“无服务器”应用和解决方案](../logic-apps/logic-apps-serverless-overview.md)时，可以只关注业务逻辑和功能。 这些服务会自动缩放以满足你的需求，加快集成速度，并帮助你在几乎不使用代码的情况下构建可靠的云应用。

若要了解其他公司如何将逻辑应用与其他 Azure 服务和 Microsoft 产品配合使用，以便增强敏捷性并更加专注于核心业务，请查看这些[客户案例](https://aka.ms/logic-apps-customer-stories)。

以下部分详细介绍了逻辑应用中的功能和优势：

#### <a name="visually-create-and-edit-workflows-with-easy-to-use-tools"></a>使用易于使用的工具直观地创建和编辑工作流

使用逻辑应用中的可视化设计工具节省时间并简化复杂过程。 在 Azure 门户、Visual Studio Code 或 Visual Studio 中使用逻辑应用设计器从头到尾创建工作流。 只需使用触发器即可启动工作流，并从[连接器库](/connectors/connector-reference/connector-reference-logicapps-connectors)添加任意数量的操作。

如果要创建基于多租户的逻辑应用，请在[从模板库创建工作流](../logic-apps/logic-apps-create-logic-apps-from-templates.md)时更快地开始。 这些模板适用于常见的工作流模式，包括服务型软件 (SaaS) 应用的简单连接，以及高级 B2B 解决方案和“只为娱乐”模板。

#### <a name="connect-different-systems-across-various-environments"></a>跨各种环境连接不同系统

某些模式和流程描述起来容易，但难以在代码中实现。 逻辑应用平台可帮助跨云、本地和混合环境无缝连接不同的系统。 例如，你可以将云营销解决方案连接到本地计费系统，或使用 Azure 服务总线跨 API 和系统集中进行消息传送。 可以通过逻辑应用快速、可靠且一致地为这些方案提供可重复使用和重新配置的解决方案。

#### <a name="write-once-reuse-often"></a>编写一次即可多次重复使用

将逻辑应用创建为 Azure 资源管理器模板，以便可以跨多个环境和区域[设置并自动执行部署](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。

#### <a name="first-class-support-for-enterprise-integration-and-b2b-scenarios"></a>针对企业集成和 B2B 方案的一流支持

企业和组织使用符合行业标准但却不同的消息协议和格式（例如 EDIFACT、AS2、X12 和 RosettaNet）进行电子方式的相互通信。 使用逻辑应用支持的[企业集成功能](../logic-apps/logic-apps-enterprise-integration-overview.md)，你可以创建工作流，将贸易合作伙伴使用的消息格式转换为组织系统可以解释和处理的格式。 逻辑应用可以通过加密和数字签名顺利且安全地处理这些交换。

一开始可使用当前的系统和服务进行小规模集成，然后按自己的步调逐渐扩大规模。 准备就绪以后，即可使用逻辑应用来获得以下功能和其他功能，从而实现集成方案并通过纵向扩展将其变为更成熟的方案：

* 集成和构建 [Microsoft BizTalk Server](/biztalk/core/introducing-biztalk-server)、[Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)、[Azure Functions](../azure-functions/functions-overview.md)、[Azure API 管理](../api-management/api-management-key-concepts.md)等。
* 使用 [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)、[AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)、[X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 和 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 协议交换消息。
* 处理 [XML 消息](../logic-apps/logic-apps-enterprise-integration-xml.md)和[平面文件](../logic-apps/logic-apps-enterprise-integration-flatfile.md)。
* 创建[集成帐户](./logic-apps-enterprise-integration-create-integration-account.md)来存储和管理 B2B 项目，例如[参与方](../logic-apps/logic-apps-enterprise-integration-partners.md)、[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)、[转换映射](../logic-apps/logic-apps-enterprise-integration-maps.md)、[验证架构](../logic-apps/logic-apps-enterprise-integration-schemas.md)等。

例如，如果使用 Microsoft BizTalk Server，则工作流可以使用 [BizTalk Server 连接器](../connectors/managed.md#on-premises-connectors)与 BizTalk Server 通信。 然后，可以使用[集成帐户连接器](../connectors/managed.md#integration-account-connectors)在工作流中运行或扩展类似于 BizTalk 的操作。 从另一个方向看，BizTalk Server 可以使用[适用于逻辑应用的 Microsoft BizTalk Server 适配器](https://www.microsoft.com/download/details.aspx?id=54287)与工作流通信。 了解如何在 BizTalk Server 中[设置和使用 BizTalk 服务器适配器](/biztalk/core/logic-app-adapter)。

#### <a name="built-in-extensibility"></a>内置的扩展性

如果没有合适的连接器可用于运行所需的代码，则可以使用 [Azure Functions](../azure-functions/functions-overview.md) 从工作流创建和调用自己的代码片段。 创建自己的 [API](../logic-apps/logic-apps-create-api-app.md) 和[自定义连接器](../logic-apps/custom-connector-overview.md)，以便从工作流对其进行调用。

#### <a name="access-resources-inside-azure-virtual-networks"></a>访问 Azure 虚拟网络中的资源

当你创建[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 时，逻辑应用工作流可访问 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)中的受保护资源，例如虚拟机 (VM) 以及其他系统或服务。 ISE 是 Azure 逻辑应用服务的专用实例，该实例使用专用资源并独立于全局多租户 Azure 逻辑应用服务运行。

在你自己的专用实例中运行逻辑应用有助于降低其他 Azure 租户对应用性能的影响，此影响也称为[“邻近干扰”影响](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)。 ISE 还带来以下好处：

* 你自己的静态 IP 地址，它们不同于多租户服务中的逻辑应用共享的静态 IP 地址。 还可以设置单个公共的、静态的和可预测的出站 IP 地址，以便与目标系统通信。 这样就无需在每个 ISE 的那些目标系统上设置额外的防火墙开口。

* 增加了对运行持续时间、存储保留、吞吐量、HTTP 请求和响应超时、消息大小和自定义连接器请求的限制。 有关详细信息，请查看 [Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md)。

当你创建 ISE 时，Azure 会将该 ISE 注入或部署到 Azure 虚拟网络中。 然后，你可以将该 ISE 用作需要进行访问的逻辑应用和集成帐户的位置。 若要详细了解如何创建 ISE，请查看[从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

#### <a name="pricing-options"></a>定价选项

每个逻辑应用类型功能和运行位置（多租户、单租户、集成服务环境）各不相同，因此具有不同的[定价模型](../logic-apps/logic-apps-pricing.md)。 例如，基于多租户的逻辑应用使用消耗量定价，而集成服务环境中的逻辑应用使用固定定价。 详细了解逻辑应用的[定价和计量](../logic-apps/logic-apps-pricing.md)。

## <a name="how-does-logic-apps-differ-from-functions-webjobs-and-power-automate"></a>逻辑应用与 Functions、WebJobs 及 Power Automate 的区别在哪里？

所有这些服务都可帮助你连接及合并不同的系统。 每项服务都有其优点和优势，因此若要快速生成可缩放且功能完备的集成系统，最好的方法是将这些服务的功能组合到一起。 有关详细信息，请查看[在逻辑应用、Functions、WebJobs 和 Power Automate 之间进行选择](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)。

## <a name="get-started"></a>入门

在开始使用 Azure 逻辑应用之前，你需要一个 Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。 否则，请尝试此[创建第一个逻辑应用的快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md)，它通过 RSS 源监视网站上的新内容，并在新内容出现时发送电子邮件。

## <a name="other-resources"></a>其他资源

通过以下介绍性视频了解有关逻辑应用平台的详细信息：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Go-serverless-Enterprise-integration-with-Azure-Logic-Apps/player]
>
> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Connect-and-extend-your-mainframe-to-the-cloud-with-Logic-Apps/player]

## <a name="next-steps"></a>后续步骤

* [快速入门：创建第一个逻辑应用工作流](../logic-apps/quickstart-create-first-logic-app-workflow.md)