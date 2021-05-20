---
title: Azure 逻辑应用预览版概述
description: Azure 逻辑应用预览版是一种用于构建自动化、单租户、有状态和无状态工作流的云解决方案，这些工作流针对企业级方案用最低代码将应用、数据、服务和系统集成在一起。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 27889e8309c0efaf1e2869fc39d099f38f64f7c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764812"
---
# <a name="overview-azure-logic-apps-preview"></a>概述：Azure 逻辑应用预览版

> [!IMPORTANT]
> 此功能现为公共预览版，在提供时不附带服务级别协议，建议不要用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

借助 Azure 逻辑应用预览版，你可使用新的“逻辑应用（预览版）”资源类型创建和运行单租户逻辑应用，从而跨应用、数据、云服务和系统构建自动化和集成解决方案。 通过这种单租户逻辑应用类型，你可构建多个[有状态和无状态工作流](#stateful-stateless)，它们由经过重新设计的 Azure 逻辑应用预览版运行时提供支持，后者为在各种托管环境（包含 Azure 和 Docker 容器）中的部署和运行提供可移植性、更佳的性能和灵活性 。

那么如何做好这一点呢？ 经过重新设计的运行时采用 [Azure Functions 扩展性模型](../azure-functions/functions-bindings-register.md)，作为扩展托管在 Azure Functions 运行时上。 这种体系结构意味着你可在 Azure Functions 运行的任何位置运行单租户逻辑应用类型。 你可在几乎任何网络拓扑上托管经过重新设计的运行时，还可选择任何可用的计算大小来处理你的工作流所需的必要工作负载。 有关详细信息，请查看 [Azure Functions 简介](../azure-functions/functions-overview.md)和 [Azure Functions 触发器和绑定](../azure-functions/functions-triggers-bindings.md)。

若要创建“逻辑应用（预览版）”资源，可[在 Azure 门户中开始操作](create-stateful-stateless-workflows-azure-portal.md)，也可[使用 Azure 逻辑应用（预览版）扩展在 Visual Studio Code 中创建项目](create-stateful-stateless-workflows-visual-studio-code.md)。 此外，在 Visual Studio Code 中，你可在开发环境中构建并本地运行你的工作流。 无论是使用门户还是 Visual Studio Code，都可在相同类型的托管环境中部署和运行单租户逻辑应用类型。

本概述介绍以下几个方面：

* [Azure 逻辑应用预览版、Azure 逻辑应用多租户环境和集成服务环境之间的区别](#preview-differences)。

* [有状态工作流和无状态工作流之间的区别](#stateful-stateless)，包括[嵌套有状态和无状态工作流](#nested-behavior)之间的行为区别。

* [此公共预览版中的功能](#public-preview-contents)。

* [定价模型的工作方式](#pricing-model)。

* [已更改、受限、不可用或不受支持的功能](#limited-unavailable-unsupported)。

* [Azure 逻辑应用预览版中的限制](#limits)。

有关详细信息，请查看下列主题：

* [Azure 逻辑应用随处运行 - 运行时深度探索](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [逻辑应用公共预览版已知问题 (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Azure 逻辑应用预览版有何不同？

Azure 逻辑应用预览版运行时采用 [Azure Functions](../azure-functions/functions-overview.md) 扩展性，作为扩展托管在 Azure Functions 运行时上。 这种体系结构意味着你可在 Azure Functions 运行的任何位置运行单租户逻辑应用类型。 你可在所需的几乎任何网络拓扑上托管 Azure 逻辑应用预览版运行时，还可选择任何可用计算大小来处理你的工作流所需的必要工作负载。 有关 Azure Functions 扩展性的详细信息，请查看 [WebJobs SDK：创建自定义输入和输出绑定](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)。

凭借这种新方法，Azure 逻辑应用预览版运行时和你的工作流是你的应用的一部分，你可将它们打包在一起。 通过此功能，你只需将项目复制到托管环境中并启动你的应用，即可部署和运行工作流。 该方法还提供了一种更加标准化的体验，便于你围绕工作流项目构建部署管道，在将更改部署到生产环境之前运行所需的测试和验证。 有关详细信息，请查看 [Azure 逻辑应用随处运行 - 运行时深度探讨](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)。

下表简要汇总了工作流根据其运行的环境共享资源的方式的差异。 有关限制方面的差别，请查看 [Azure 逻辑应用预览版中的限制](#limits)。

| 环境 | 资源共享和消耗 |
|-------------|----------------------------------|
| Azure 逻辑应用（多租户） | 来自跨多个租户的客户的工作流共享相同的处理（计算）、存储和网络等。 |
| Azure 逻辑应用（预览版，单租户） | 同一逻辑应用和单个租户中的工作流共享相同的处理（计算）、存储和网络等。 |
| 集成服务环境（在预览版中不可用） | 同一环境中的工作流共享相同的处理（计算）、存储和网络等。 |
|||

同时，你仍可使用多租户 Azure 逻辑应用扩展在 Azure 门户和 Visual Studio Code 中创建多租户逻辑应用类型。 虽然多租户和单租户逻辑应用类型之间的开发体验不同，但你的 Azure 订阅仍可同时包含这两种类型。 你可查看和访问 Azure 订阅中所有已部署的逻辑应用，但这些应用整理在其自己的类别和部分中。

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>有状态和无状态工作流

借助单租户逻辑应用类型，你可在同一逻辑应用中创建下列工作流状态：

* 有状态

  如果需要保留、查看和引用来自先前事件的数据，则创建有状态工作流。 这些工作流会在外部存储中保存每项操作及其状态的输入和输出，这使得你可在每次运行完成后查看运行详细信息和历史记录。 如果服务中断，有状态工作流提供高复原能力。 在服务和系统还原后，你可从已保存的状态重新构造中断的运行，并再次运行工作流来完成操作。 有状态工作流可持续运行长达一年。

* *无状态*

  如果不需要在外部存储中保存先前事件中的数据供稍后查看，也无需查看或引用这些数据，那么请创建无状态工作流。 这些工作流仅在内存中保存每项操作及其状态的输入和输出，而不是将此数据传输到外部存储。 结果是，由于运行详细信息和历史记录不会保留在外部存储，因此无状态工作流具有时间更短的运行（通常不超过 5 分钟）、更快的速度和更短的响应时间、更高的吞吐量，以及更低的运行成本。 不过，如果服务中断，已中断的运行不会自动还原，因此调用方需要重新手动提交已中断的运行。 这些工作流只能同步运行。

  为了简化调试，你可为无状态工作流启用运行历史记录（这对性能有一些影响），然后在完成时再禁用运行历史记录。 有关详细信息，请查看[在 Visual Studio Code 中创建有状态和无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)或[在 Azure 门户中创建有状态和无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)。

  > [!NOTE]
  > 对于部署在 Azure 中的[托管连接器](../connectors/managed.md)，无状态工作流目前仅支持操作，不支持触发器。 若要启动工作流，请选择[内置的请求、事件中心或服务总线触发器](../connectors/built-in.md)。 这些触发器在 Azure 逻辑应用预览版运行时中本机运行。 若要详细了解受限、不可用或不受支持的触发器、操作和连接器，请查看[已更改、受限、不可用或不受支持的功能](#limited-unavailable-unsupported)。

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>有状态和无状态工作流之前的嵌套行为差异

可使用[请求触发器](../connectors/connectors-native-reqres.md)、[HTTP Webhook 触发器](../connectors/connectors-native-webhook.md)，或者具有 [ApiConnectionWebhook 类型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)且可接收 HTTPS 请求的托管连接器触发器，将工作流设置为可从同一“逻辑应用（预览版）”资源中存在的其他工作流[调用](../logic-apps/logic-apps-http-endpoint.md)。

下面是在父工作流调用子工作流之后，嵌套工作流可遵循的行为模式：

* 异步轮询模式

  父工作流不会等待对其初始调用的响应，而是会继续检查子工作流的运行历史记录，直到子工作流完成运行为止。 默认情况下，有状态工作流采用这种模式，它非常适合可能会超出[请求超时限制](../logic-apps/logic-apps-limits-and-config.md)的长期运行的子工作流。

* 同步模式（“触发并忘记”）

  子工作流通过立即返回 `202 ACCEPTED` 响应来确认调用，而父工作流继续执行下一操作，不等待从子工作流返回结果， 而是在子工作流完成运行时接收结果。 不包含响应操作的有状态子工作流始终采用同步模式。 对于有状态子工作流，你可查看其运行历史记录。

  若要启用此行为，请在工作流的 JSON 定义中，将 `operationOptions` 属性设置为 `DisableAsyncPattern`。 有关详细信息，请查看[触发器和操作类型 - 操作选项](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)。

* 触发并等待

  对于无状态子工作流，父工作流会等待响应，即子工作流返回结果。 此模式的工作方式类似于使用内置的 [HTTP 触发器或操作](../connectors/connectors-native-http.md)来调用子工作流。 不包含响应操作的无状态子工作流会立即返回 `202 ACCEPTED` 响应，但父工作流会等待子工作流完成，然后才继续到下一操作。 这些行为仅适用于无状态子工作流。

下表根据父工作流和子工作流是有状态、无状态还是混合工作流类型，指定子工作流的行为：

| 父工作流 | 子工作流 | 子工作流行为 |
|-----------------|----------------|----------------|
| 有状态 | 有状态 | 同步或异步，带有 `"operationOptions": "DisableAsyncPattern"` 设置 |
| 有状态 | 无状态 | 触发并等待 |
| 无状态 | 有状态 | 同步 |
| 无状态 | 无状态 | 触发并等待 |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>功能

Azure 逻辑应用预览版目前有很多功能，还有很多附加功能，例如：

* 通过[超过 400 个连接器](/connectors/connector-reference/connector-reference-logicapps-connectors)，针对本地系统的服务型软件 (SaaS) 应用、平台即服务 (PaaS) 和服务加连接器创建逻辑应用及其工作流。

  * 某些托管连接器现在作为内置版本提供，它们的运行方式类似于内置的触发器和操作（如请求触发器和 HTTP 操作），它们在 Azure 逻辑应用预览运行时上原生运行。 例如，这些新的内置连接器包括 Azure 服务总线、Azure 事件中心、SQL Server 和 MQ。

    > [!NOTE]
    > 对于内置 SQL Server 连接器，只有“执行查询”操作可以直接连接到 Azure 虚拟网络，而不需要[本地数据网关](logic-apps-gateway-connection.md)。

  * 使用[预览版的扩展性框架](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)为所需的任何服务创建自己的内置连接器。 这些连接器与 Azure 服务总线和 SQL Server 等内置连接器很相似，但与预览版目前不支持的[自定义连接器不同](../connectors/apis-list.md#custom-apis-and-connectors)，它们提供更高的吞吐量、低延迟和本地连接，并且与预览版运行时在同一进程中本地运行。

    目前仅可在 Visual Studio Code 中使用创作功能，该功能默认情况下不启用。 若要创建这些连接器，请[将项目从基于扩展捆绑 (Node.js) 切换到基于 NuGet 包 (.NET)](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring)。 有关详细信息，请查看 [Azure 逻辑应用随处运行 - 内置连接器扩展性](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)。

  * 如果没有集成帐户，可对 Liquid 操作和 XML 操作使用 B2B 操作。 若要使用 B2B 操作，需要具有 Liquid 映射、XML 映射或 XML 架构；你可在 Azure 门户中通过各自的操作上传这些内容，或者使用各自的“映射”和“架构”文件夹将其添加到 Visual Studio Code 项目的“项目”文件夹中  。

  * 创建和部署可随处运行的逻辑应用，理由是 Azure 逻辑应用服务会生成共享访问签名 (SAS) 连接字符串，而这些逻辑应用可使用这些字符串将请求发送到云连接运行时终结点。 逻辑应用服务会将这些连接字符串随其他应用程序设置一起保存，这样你在 Azure 中部署时，可在 Azure Key Vault 中轻松存储这些值。

    > [!NOTE]
    > 默认情况下，“逻辑应用（预览版）”资源自带[系统分配的托管标识](../logic-apps/create-managed-service-identity.md)，该标识已自动启用来在运行时对连接进行身份验证。 该标识与你在创建连接时使用的身份验证凭据或连接字符串不同。 如果禁用该标识，则运行时连接无效。 若要查看此设置，请在逻辑应用菜单的“设置”下，选择“标识” 。

* 创建带有仅在内存中运行的无状态工作流的逻辑应用；这样的话，由于操作之间的运行历史记录和数据不会保留在外部存储中，这些应用会更快完成、更快响应、具有更高的吞吐量且运行成本更低。 或者，你也可启用运行历史记录来简化调试。 有关详细信息，请查看[有状态与无状态逻辑应用](#stateful-stateless)。

* 在 Visual Studio Code 开发环境中本机运行、测试和调试逻辑应用及其工作流。

  在运行和测试逻辑应用之前，可在工作流的 workflow.json 文件中添加和使用断点来简化调试。 不过，断点目前仅可用于操作，不可用于触发器。 有关详细信息，请查看[在 Visual Studio Code 中创建有状态和无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)。

* 将逻辑应用及其工作流从 Visual Studio Code 直接发布或部署到各种托管环境中，例如 Azure 和 [Docker 容器](/dotnet/core/docker/introduction)。

* 如果你的 Azure 订阅和逻辑应用设置支持，可使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 为逻辑应用启用诊断日志记录和跟踪功能。

* 访问网络功能（例如连接功能）并以专用方式与 Azure 虚拟网络集成，这与你在使用 [Azure Functions 高级计划](../azure-functions/functions-premium-plan.md)创建和部署逻辑应用时的 Azure Functions 很相似。 有关详细信息，请查看以下主题：

  * [Azure Functions 网络选项](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps Running Anywhere - Networking possibilities with Azure Logic Apps Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)（随处运行的 Azure 逻辑应用 - Azure 逻辑应用预览版进行网络连接的可能性）

* 为单租户“逻辑应用（预览版）”资源中单个工作流使用的托管连接重新生成访问密钥。 在此任务中，请[针对多租户“逻辑应用”资源在单独工作流级别（而不是逻辑应用资源级别）执行相同步骤](logic-apps-securing-a-logic-app.md#regenerate-access-keys)。

* 按照多租户设计器采用的步骤在单租户设计器中添加并行分支。

有关详细信息，请查看[已更改、受限、不可用和不受支持的功能](#limited-unavailable-unsupported)以及 [GitHub 中的逻辑应用公共预览版已知问题页面](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

<a name="pricing-model"></a>

## <a name="pricing-model"></a>定价模型

在 Azure 门户中创建单租户逻辑应用类型，或者从 Visual Studio Code 进行部署时，必须为要使用的逻辑应用选择一个托管计划（[应用服务计划或高级计划](../azure-functions/functions-scale.md)）。 此计划确定了适用于运行你的逻辑应用的定价模型。 如果选择应用服务计划，则还必须选择一个[定价层](../app-service/overview-hosting-plans.md)。

有状态工作流使用[外部存储](../azure-functions/storage-considerations.md#storage-account-requirements)，因此 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)适用于 Azure 逻辑应用预览版运行时执行的存储事务。 例如，使用队列来进行计划，而使用表和 Blob 来存储工作流状态。

> [!NOTE]
> 在公共预览版期间，除了所选计划的费用外，在应用服务上运行逻辑应用不会产生额外费用。

若要详细了解适用于单租户资源类型的定价模型，请查看下列主题：

* [Azure Functions 的缩放和托管](../azure-functions/functions-scale.md)
* [在 Azure 应用服务中纵向扩展应用](../app-service/manage-scale-up.md)
* [Azure Functions 定价详细信息](https://azure.microsoft.com/pricing/details/functions/)
* [应用服务定价详细信息](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure 存储定价详细信息](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>已更改、受限、不可用或不受支持的功能

在 Azure 逻辑应用预览版中，下列功能已经过更改，它们目前功能受限、不可用或不受支持：

* **OS 支持**：当前，Visual Studio Code 中的设计器不可用于 Linux OS，但你仍可部署对基于 Linux 的虚拟机使用逻辑应用预览版运行时的逻辑应用。 目前，你可在 Windows 或 macOS 上的 Visual Studio Code 中构建逻辑应用，然后将其部署到基于 Linux 的虚拟机上。

* **触发器和操作**：内置触发器和操作在 Azure 逻辑应用预览版运行时中本机运行，而托管连接器部署在 Azure 中。 某些内置触发器不可用，例如“滑动窗口”和“批处理”。

  若要启动工作流，请使用[内置的重复周期、请求HTTP、HTTP Webhook、事件中心或服务总线触发器](../connectors/apis-list.md)。 在设计器中，内置触发器和操作显示在“内置”选项卡下，而托管连接器触发器和操作显示在“Azure”选项卡下 。

  > [!NOTE]
  > 若要在 Visual Studio Code 中本地运行，基于 Webhook 的触发器和操作需要额外设置。 有关详细信息，请查看[在 Visual Studio Code 中创建有状态和无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)。

  * 对于无状态工作流，选择触发器时不会显示“Azure”选项卡，原因是你仅可选择托管[连接器操作，不可选择触发器](../connectors/managed.md)。 虽然可为无状态工作流启用 Azure 部署的托管连接器，但设计器不会显示任何托管连接器触发器供你添加。

  * 对于有状态工作流，除了在下方列为不可用的触发器和操作，其他[托管连接器触发器和操作](../connectors/managed.md)均可供你使用。

  * 以下触发器和操作已经过更改，或者目前功能受限、不受支持或不可用：

    * [本地数据网关触发器](../connectors/managed.md#on-premises-connectors)不可用，但网关操作可用 。

    * [Azure Functions - 选择 Azure 函数](logic-apps-azure-functions.md)这一内置操作现在名为“Azure 函数操作 - 调用 Azure 函数”。 此操作目前仅适用于通过 HTTP 触发器模板创建的函数。

      在 Azure 门户中，可选择一个 HTTP 触发器函数；你可通过用户体验创建连接来访问此函数。 如果在代码视图或 workflow.json 文件中检查函数操作的 JSON 定义，则操作会使用 `connectionName` 引用来引用函数。 此版本会将函数的信息抽象化处理为一个连接；可在项目的 connections.json 文件中找到它，而创建连接后就会提供该文件。

      > [!NOTE]
      > 在单租户版本中，函数操作仅支持查询字符串身份验证。 Azure 逻辑应用预览版会在建立连接时从函数中获取默认密钥、将该密钥存储在应用的设置中，并在调用函数时使用该密钥来进行身份验证。
      >
      > 与多租户版本一样，如果你续订此密钥（例如通过门户中的 Azure Functions 体验），且由于密钥无效，函数操作会失效。 若要解决此问题，需要重新创建与你想要调用的函数的连接，或者使用新密钥更新应用的设置。

    * [内联代码 - 执行 JavaScript 代码](logic-apps-add-run-inline-code.md)这一内置操作现在名为“内联代码操作 - 运行内联 JavaScript”。

      * “内联代码操作”操作不再需要集成帐户。

      * 对于 macOS 和 Linux，在 Visual Studio Code 中使用 Azure 逻辑应用（预览版）扩展时，现支持“内联代码操作”。

      * 如果在“内联代码操作”操作中进行更改，无需再重启逻辑应用。

      * “内联代码操作”操作具有[更新的限制](logic-apps-overview-preview.md#inline-code-limits)。

    * [集成帐户的某些内置 B2B 触发器和操作](../connectors/managed.md#integration-account-connectors)不可用，例如平面文件编码和解码操作。

    * [Azure 逻辑应用 - 创建逻辑应用工作流](logic-apps-http-endpoint.md)这一内置操作现在名为“工作流操作 - 在此工作流应用中调用工作流”。

* 预览版目前不支持[自定义连接器](../connectors/apis-list.md#custom-apis-and-connectors)。

* **托管计划可用性**：无论是在 Azure 门户中创建单租户“逻辑应用（预览版）”资源类型，还是从 Visual Studio Code 中进行部署，都只能在 Azure 中使用高级托管计划或应用服务托管计划。 消耗托管计划不可用，且不适用于部署此资源类型。 你可从 Visual Studio Code 部署到 Docker 容器，但不能部署到[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

* **Visual Studio Code 中的断点调试**：虽然可在工作流的 workflow.json 文件中添加和使用断点，但目前仅可对操作使用断点，不可将其用于触发器。 有关详细信息，请查看[在 Visual Studio Code 中创建有状态和无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)。

* **缩放控件**：目前不可在设计器上使用缩放控件。

* **触发器历史记录和运行历史记录**：对于“逻辑应用（预览版）”资源类型，Azure 门户中的触发器历史记录和运行历史记录显示在工作流级别中，而非逻辑应用级别中。 若要查找此历史数据，请执行以下步骤：

   * 若要查看运行历史记录，请在逻辑应用中打开工作流。 在工作流菜单的“开发人员”下，选择“监视器” 。

   * 若要查看触发器历史记录，请在逻辑应用中打开工作流。 在工作流菜单的“开发人员”下，选择“触发器历史记录” 。

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>在严格网络和防火墙方案中允许流量

如果你的环境设有限制流量的严格网络要求或防火墙，则必须允许访问以在逻辑应用工作流中实现任何触发器或操作连接。

若要查找这些连接的完全限定的域名 (FQDN)，请查看以下主题中的相应部分：

* [单租户逻辑应用的防火墙权限 - Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [单租户逻辑应用的防火墙权限 - Azure 门户](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>更新后的限制

虽然 Azure 逻辑应用预览版的很多限制仍然与[多租户 Azure 逻辑应用的限制](logic-apps-limits-and-config.md)相同，但下列限制针对 Azure 逻辑应用预览版进行了更改。

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>HTTP 超时限制

对于单个入站或出站调用，下列触发器和操作的超时限制为 230 秒（3.9 分钟）：

* 出站请求：HTTP 触发器，HTTP 操作
* 入站请求：请求触发器，HTTP Webhook 触发器，HTTP Webhook 操作

相对地，这些触发器和操作在运行逻辑应用及其工作流的其他环境中的超时限制如下所示：

* 多租户 Azure 逻辑应用：120 秒（2 分钟）
* 集成服务环境：240 秒（4 分钟）

有关详细信息，请查看 [HTTP 限制](logic-apps-limits-and-config.md#http-limits)。

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>托管的连接器

对于托管连接器，限制是每个连接每分钟 50 个请求。 若要解决连接器限制问题，请查看[处理 Azure 逻辑应用中的限制问题（429 -“请求过多”错误）](handle-throttling-problems-429-errors.md#connector-throttling)。

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>内联代码操作（执行 JavaScript 代码）

对于单个逻辑应用定义，[执行 JavaScript 代码](logic-apps-add-run-inline-code.md)这一内联代码操作的限制进行了如下更新：

* 最大代码字符数从 1,024 个增加到了 100,000 个。

* 运行代码的最大持续时间从 5 秒增加到了 15 秒。

有关详细信息，请查看[逻辑应用定义限制](logic-apps-limits-and-config.md#definition-limits)。

## <a name="next-steps"></a>后续步骤

* [在 Azure 门户中创建有状态和无状态工作流](create-stateful-stateless-workflows-azure-portal.md)
* [在 Visual Studio Code 中创建有状态或无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md)
* [GitHub 中的逻辑应用公共预览版已知问题页面](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

此外，我们还希望你就 Azure 逻辑应用预览版体验提供反馈！

* 如果发现 bug 或问题，请[在 GitHub 中创建问题](https://github.com/Azure/logicapps/issues)。
* 如有问题、请求、意见和其他反馈，请[使用此反馈表单](https://aka.ms/lafeedback)。
