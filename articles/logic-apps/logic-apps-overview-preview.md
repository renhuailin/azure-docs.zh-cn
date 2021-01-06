---
title: Azure 逻辑应用预览版概述
description: Azure 逻辑应用预览版是一种云解决方案，用于构建自动有状态和无状态的工作流，这些工作流集成应用、数据、服务和系统，并对企业级方案使用最少的代码。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 7c15b3a854b533d93bc05f7e5302671711da75c2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936118"
---
# <a name="overview-azure-logic-apps-preview"></a>概述： Azure 逻辑应用预览

> [!IMPORTANT]
> 此功能现为公共预览版，在提供时不附带服务级别协议，建议不要用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure 逻辑应用预览版，你可以通过使用新的 **逻辑应用 (预览)** 资源类型，通过创建并运行包含有 [*状态* 和 *无状态* 工作流](#stateful-stateless)的逻辑应用来跨应用、数据、云服务和系统生成自动化和集成解决方案。 利用这一新的逻辑应用类型，你可以构建由重新设计的 Azure 逻辑应用预览运行时提供支持的多个工作流，该运行时提供可移植性、更好的性能和灵活性，可用于在各种宿主环境中进行部署和运行，而不仅限于 Azure，还支持 Docker 容器。

这是如何实现的？ 重新设计的运行时使用 [Azure Functions 扩展性模型](../azure-functions/functions-bindings-register.md) ，并作为 Azure Functions 运行时的扩展托管。 此体系结构意味着可以在 Azure Functions 运行的任何位置运行新的逻辑应用类型。 你可以在几乎所有网络拓扑中托管经过重新设计的运行时，并选择任何可用的计算大小来处理工作流所需的必要工作负荷。 有关详细信息，请参阅 [Azure Functions 简介](../azure-functions/functions-overview.md) 和 [Azure Functions 触发器和绑定](../azure-functions/functions-triggers-bindings.md)。

可以通过以下方式创建 **逻辑应用 (预览)** 资源： [从 Azure 门户开始，](create-stateful-stateless-workflows-azure-portal.md) 或者通过 [使用 Azure 逻辑应用 (预览版) 扩展在 Visual Studio Code 中创建项目](create-stateful-stateless-workflows-visual-studio-code.md)。 此外，在 Visual Studio Code 中，你可以在开发环境中生成 *并本地运行* 工作流。 无论是使用门户还是 Visual Studio Code，都可以在相同类型的宿主环境中部署和运行新的逻辑应用类型。

本概述包含以下几个方面：

* [Azure 逻辑应用预览、Azure 逻辑应用多租户环境和 integration service 环境之间的差异](#preview-differences)。

* 有[状态和无状态工作流之间的差异](#stateful-stateless)，包括[嵌套有状态和无状态工作流](#nested-behavior)之间的行为差异。

* [此公共预览版中的功能](#public-preview-contents)。

* [定价模型的工作方式](#pricing-model)。

* [更改、受限、不可用或不支持的功能](#limited-unavailable-unsupported)。

* [Azure 逻辑应用预览中的限制](#limits)。

有关详细信息，请参阅以下文章：

* [运行时的 Azure 逻辑应用-运行时深入探讨](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [逻辑应用公共预览版已知问题 (GitHub) ](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Azure 逻辑应用预览有何不同？

Azure 逻辑应用预览运行时使用 [Azure Functions](../azure-functions/functions-overview.md) 扩展性，并作为 Azure Functions 运行时的扩展托管。 此体系结构意味着可以在 Azure Functions 运行的任何位置运行新的逻辑应用类型。 你可以在几乎任何所需的网络拓扑中托管 Azure 逻辑应用预览运行时，并选择任何可用计算大小来处理工作流所需的必要工作负荷。 有关 Azure Functions 扩展性的详细信息，请参阅 [Web 作业 SDK：创建自定义输入和输出绑定](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)。

利用这种新方法，Azure 逻辑应用预览运行时和工作流都是你的应用程序的一部分，你可以将这些应用程序打包在一起。 通过此功能，只需将项目复制到宿主环境并启动应用即可部署和运行工作流。 在将更改部署到生产环境之前，此方法还提供了更标准化的方法来围绕工作流项目构建 DevOps 管道，以运行所需的测试和验证。 有关详细信息，请参阅 [运行时的 Azure 逻辑应用-运行时深入探讨](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)。

下表简要概述了工作流共享资源的方式的差异，这取决于运行工作流的环境。 有关限制的差异，请参阅 [Azure 逻辑应用预览中的限制](#limits)。

| 环境 | 资源共享和消耗 |
|-------------|----------------------------------|
|  (多租户) 的 Azure 逻辑应用 | *来自多个租户的客户* 的工作流共享相同的处理 (计算) 、存储、网络等。 |
| Azure 逻辑应用 (预览)  | *同一逻辑应用中* 的工作流共享相同的处理 (计算) 、存储、网络等。 |
| 集成服务环境 (预览版中不可用)  | *相同环境* 中的工作流 (计算) 、存储、网络等共享相同的处理。 |
||||

同时，仍可以使用原始的 Azure 逻辑应用扩展在 Azure 门户和 Visual Studio Code 中创建原始逻辑应用类型。 尽管开发体验在原始和新的逻辑应用类型之间有所不同，但 Azure 订阅可以同时包含这两种类型。 可以在 Azure 订阅中查看和访问所有已部署的逻辑应用，但这些应用会组织到它们自己的类别和分区中。

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>有状态和无状态工作流

* *有状态*

  需要保留、查看或引用以前事件中的数据时，可以创建有状态工作流。 这些工作流保存每个操作的输入和输出，并保存其在外部存储中的状态，这样就可以在每次运行完成后查看运行详细信息和历史记录。 如果发生中断，有状态工作流提供高复原能力。 在还原服务和系统后，您可以从已保存状态重新构造中断的运行，然后重新运行工作流完成。 有状态工作流的运行时间最多可达一年。

* *无状态*

  如果不需要保存、查看或引用外部存储中以前事件的数据以供以后查看，请创建无状态工作流。 这些工作流将每个操作及其状态的输入和输出 *仅保存在内存中*，而不是将此数据传输到外部存储。 因此，无状态工作流的运行时间通常不超过5分钟，更快的响应时间更快，吞吐量更高，降低了运行成本，因为运行详细信息和历史记录不会保存在外部存储中。 但是，如果发生中断，中断的运行不会自动还原，因此调用方需要手动重新提交中断的运行。 这些工作流只能同步运行。

  为了更轻松地进行调试，你可以为无状态工作流启用运行历史记录，这会对性能产生影响，然后在完成后禁用运行历史记录。 有关详细信息，请参阅 [在 Visual Studio Code 中创建有状态和无状态的工作流](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) ，或者 [在 Azure 门户中创建有状态和无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)。

  > [!NOTE]
  > 无状态工作流目前只支持 [托管连接器](../connectors/apis-list.md#managed-api-connectors)的操作，这些 *操作* 部署在 Azure 中，而不支持触发器。 若要启动工作流，请选择 "内置请求"、" [事件中心" 或 "服务总线触发器](../connectors/apis-list.md#built-ins)"。 这些触发器在 Azure 逻辑应用预览运行时中以本机方式运行。 有关受限、不可用或不受支持的触发器、操作和连接器的详细信息，请参阅 [更改、受限、不可用或不支持的功能](#limited-unavailable-unsupported)。

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>有状态和无状态工作流之间的嵌套行为差异

可以通过使用 [请求触发器](../connectors/connectors-native-reqres.md)、 [HTTP Webhook 触发器](../connectors/connectors-native-webhook.md)或具有 [APICONNECTIONWEBHOOK 类型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)并且可以接收 HTTPS 请求的托管连接器触发器，[使工作流](../logic-apps/logic-apps-http-endpoint.md)可从同一逻辑应用中存在的其他工作流调用 **(预览)** 资源。

下面是嵌套工作流在调用子工作流之后可遵循的行为模式：

* 异步轮询模式

  父项不会等待对初始调用的响应，但会持续检查子的运行历史记录，直到子节点完成运行。 默认情况下，有状态工作流遵循此模式，这非常适合于可能超出 [请求超时限制](../logic-apps/logic-apps-limits-and-config.md)的长时间运行的子工作流。

* 同步模式 ( "火灾和遗忘" ) 

  子元素通过立即返回响应来确认调用 `202 ACCEPTED` ，而父级将继续执行下一个操作，而不会等待子中的结果。 相反，父项在子节点完成运行时接收结果。 不包括响应操作的子状态工作流始终遵循同步模式。 对于子状态工作流，可以使用运行历史记录查看。

  若要启用此行为，请在工作流的 JSON 定义中，将 `operationOptions` 属性设置为 `DisableAsyncPattern` 。 有关详细信息，请参阅 [触发器和操作类型-操作选项](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)。

* 触发器并等待

  对于子无状态工作流，父代等待返回子的结果的响应。 此模式的工作方式类似于使用内置 [HTTP 触发器或操作](../connectors/connectors-native-http.md) 来调用子工作流。 不包含响应操作的子无状态工作流立即返回 `202 ACCEPTED` 响应，但父代等待子项完成，然后继续下一个操作。 这些行为仅适用于子无状态工作流。

此表指定子工作流的行为，具体取决于父级和子级是有状态、无状态还是混合工作流类型：

| 父工作流 | 子工作流 | 子行为 |
|-----------------|----------------|----------------|
| 有状态 | 有状态 | 带有设置的异步或同步 `"operationOptions": "DisableAsyncPattern"` |
| 有状态 | 无状态 | 触发器并等待 |
| 无状态 | 有状态 | Synchronous |
| 无状态 | 无状态 | 触发器并等待 |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>功能

Azure 逻辑应用预览包括许多当前功能和其他功能，例如：

* 通过 [390 + 连接器](/connectors/connector-reference/connector-reference-logicapps-connectors) 为软件即服务 (SaaS) 和平台即服务 (PaaS) 应用和服务以及本地系统的连接器创建逻辑应用及其工作流。

  * 某些托管连接器（如 Azure 服务总线、Azure 事件中心和 SQL Server）的运行方式类似于 Azure 逻辑应用预览运行时的本机内置触发器和操作，例如请求触发器和 HTTP 操作。 有关详细信息，请参阅 [运行任意位置的 Azure 逻辑应用-内置连接器扩展性](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)。

  * 在没有集成帐户的情况下，你可以对液体操作和 XML 操作使用 B2B 操作。 若要使用这些操作，你需要具有液体地图、XML 地图或 XML 架构，你可以通过 Azure 门户中的相应操作上传这些架构，或者使用相应的 "**映射**" 和 "**架构**" 文件夹将添加到 Visual Studio Code **项目的 "项目"** 文件夹。

  * 创建和部署可在任何位置运行的逻辑应用，因为 Azure 逻辑应用服务会生成 (SAS) 连接字符串的共享访问签名，这些逻辑应用可使用这些连接字符串向云连接运行时终结点发送请求。 逻辑应用服务会将这些连接字符串与其他应用程序设置一起保存，以便在 Azure 中部署时，可以轻松地将这些值存储在 Azure Key Vault 中。

    > [!NOTE]
    > 默认情况下， **逻辑应用 (预览)** 资源已自动启用 [系统分配的托管标识](../logic-apps/create-managed-service-identity.md) ，以在运行时对连接进行身份验证。 此标识不同于在创建连接时使用的身份验证凭据或连接字符串。 如果禁用此标识，则连接在运行时不起作用。 若要查看此设置，请在逻辑应用的菜单上的 " **设置**" 下，选择 " **标识**"。

* 创建具有仅在内存中运行的无状态工作流的逻辑应用，以便这些应用程序能够更快地完成、更快地响应、更高的吞吐量和运行成本，因为不会在外部存储中保留运行历史记录和数据。 还可以选择启用运行历史记录，以便更轻松地进行调试。 有关详细信息，请参阅有 [状态和无状态逻辑应用](#stateful-stateless)。

* 在 Visual Studio Code 开发环境中本地运行、测试和调试逻辑应用及其工作流。

  在运行和测试逻辑应用之前，可以通过在工作流的 **workflow.js** 文件中添加和使用断点来更轻松地进行调试。 然而，此时仅支持操作，而不支持断点。 有关详细信息，请参阅 [在 Visual Studio Code 中创建有状态和无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)。

* 直接将逻辑应用及其工作流从 Visual Studio Code 发布或部署到各种宿主环境，例如 Azure 和 [Docker 容器](/dotnet/core/docker/introduction)。

* 当你的 Azure 订阅和逻辑应用设置支持时，通过使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 为逻辑应用启用诊断日志记录和跟踪功能。

> [!NOTE]
> 有关当前已知问题的信息，请查看 [GitHub 中的逻辑应用公共预览版已知问题页](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)。

<a name="pricing-model"></a>

## <a name="pricing-model"></a>定价模型

在 Azure 门户中创建新的逻辑应用类型或从 Visual Studio Code 部署时，必须选择要使用的逻辑应用的托管计划（ [应用服务或高级](../azure-functions/functions-scale.md)）。 此计划确定适用于运行逻辑应用的定价模型。 如果选择 "应用服务计划"，则还必须选择 [定价层](../app-service/overview-hosting-plans.md)。

有 *状态* 工作流使用 [外部存储](../azure-functions/storage-considerations.md#storage-account-requirements)，因此 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)适用于 azure 逻辑应用预览运行时执行的存储事务。 例如，队列用于计划，而表和 blob 用于存储工作流状态。

> [!NOTE]
> 公共预览期间，在应用服务上运行逻辑应用不会在所选计划的顶部产生 *额外* 的费用。

有关适用于此新资源类型的定价模型的详细信息，请参阅以下主题：

* [Azure Functions 的缩放和托管](../azure-functions/functions-scale.md)
* [在 Azure App Service 中纵向扩展应用](../app-service/manage-scale-up.md)
* [Azure Functions 定价详细信息](https://azure.microsoft.com/pricing/details/functions/)
* [应用服务定价详细信息](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure 存储定价详细信息](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>更改、受限、不可用或不支持的功能

在 Azure 逻辑应用预览中，这些功能已更改，或者它们当前受限、不可用或不受支持：

* **触发器和操作**：某些内置触发器不可用，例如滑动窗口和批处理。 若要启动工作流，请使用内置的 " [重复周期"、"请求"、"http"、"事件中心" 或 "服务总线触发器](../connectors/apis-list.md)"。 内置触发器和操作在 Azure 逻辑应用预览运行时中以本机方式运行，而托管连接器则部署在 Azure 中。 在设计器中，内置触发器和操作显示在 " **内置** " 选项卡下，而 "托管连接器触发器" 和 "操作" 显示在 " **Azure** " 选项卡下。

  > [!NOTE]
  > 若要在 Visual Studio Code 中本地运行，基于 webhook 的触发器和操作需要额外的设置。 有关详细信息，请参阅 [在 Visual Studio Code 中创建有状态和无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)。

  * 对于 *无状态工作流*，当你选择触发器时， **Azure** 选项卡不会显示，因为你可以仅选择 [托管连接器 *操作*，而不是触发器](../connectors/apis-list.md#managed-api-connectors)。 尽管可以为无状态工作流启用 Azure 部署的托管连接器，但设计器不会显示任何托管的连接器触发器供你添加。

  * 对于不在下面列出的触发器和操作以外的有 *状态工作流*，可使用 [托管连接器触发器和操作](../connectors/apis-list.md#managed-api-connectors) 。

  * 这些触发器和操作已更改或当前受限、不受支持或不可用：

    * [本地数据网关 *触发器*](../connectors/apis-list.md#on-premises-connectors) 不可用，但网 *关操作可用* 。

    * [自定义连接器](../connectors/apis-list.md#custom-apis-and-connectors) 不可用。

    * 内置操作 [Azure Functions-选择 azure](logic-apps-azure-functions.md) 函数现为 Azure 函数 **操作-调用 azure 函数**。 此操作当前仅适用于从 **HTTP 触发器** 模板创建的函数。

      在 Azure 门户中，可以选择一个 HTTP 触发器函数，通过它可以通过用户体验创建连接来获得访问权限。 如果在代码视图或文件的 **workflow.js** 中检查函数操作的 JSON 定义，则操作通过使用引用来引用函数 `connectionName` 。 此版本将函数的信息作为连接进行抽象化，你可以在项目的 **connections.js** 文件中找到该文件，这是在创建连接后提供的。

      > [!NOTE]
      > 在预览版本中，函数操作仅支持查询字符串身份验证。 Azure 逻辑应用预览在建立连接时从函数获取默认密钥，将该密钥存储在应用的设置中，并在调用函数时使用该密钥进行身份验证。
      >
      > 与原始版本一样，如果你续订此密钥（例如，通过门户中的 Azure Functions 体验），则由于无效的密钥，因此该函数操作不再有效。 若要解决此问题，需要重新创建与要调用的函数的连接，或用新密钥更新应用的设置。

    * 内置操作（ [内联代码执行 JavaScript 代码](logic-apps-add-run-inline-code.md) ）现为 **内联代码操作-运行内联 JavaScript**。

      * 内联代码操作操作不再需要集成帐户。

      * 如果你使用的是 macOS 或 Linux，则在 Visual Studio Code (预览) 扩展时， **内联代码操作** 当前不可用。

      * 如果对内联代码操作操作进行更改，则需要重新启动逻辑应用。

      * 内联代码操作操作具有 [更新的限制](logic-apps-overview-preview.md#inline-code-limits)。

    * 某些 [内置 B2B 触发器和集成帐户的操作](../connectors/apis-list.md#integration-account-connectors) 不可用，例如 **平面文件** 编码和解码操作。

    * 内置操作 [Azure 逻辑应用-选择逻辑应用工作流](logic-apps-http-endpoint.md) 现在为 **工作流操作-在此工作流应用中调用工作流**。

* **托管计划可用性**：无论你是在 Azure 门户中创建新的 **逻辑应用 (预览)** 资源类型，还是从 Visual Studio Code 部署，你只能在 Azure 中使用高级服务托管计划或应用服务托管计划。 消耗托管计划不可用，因此不支持部署此资源类型。 你可以从 Visual Studio Code 部署到 Docker 容器，但不能部署到 [ (ISE) 的 integration service 环境 ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

* **并行分支**：当前不能通过新的设计器体验添加并行分支。 不过，您仍可以通过原始设计器体验添加这些分支，并使它们出现在新的设计器中。

  1. 在设计器的底部，通过选择 " **新建画布** " 控件禁用新的体验。

  1. 向工作流添加并行分支。

  1. 再次选择 " **新建画布** " 控件，启用新的体验。

* **缩放控件**：当前在设计器上无法使用缩放控件。

* **Visual Studio Code 中的断点调试**：尽管你可以在工作流的文件 **workflow.js** 中添加和使用断点，但此时仅支持操作的断点，而非触发器。 有关详细信息，请参阅 [在 Visual Studio Code 中创建有状态和无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)。

<a name="limits"></a>

## <a name="updated-limits"></a>更新的限制

尽管 Azure 逻辑应用预览的许多限制与 [多租户 Azure 逻辑应用的限制](logic-apps-limits-and-config.md)保持一致，但 Azure 逻辑应用预览已更改这些限制。

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>HTTP 超时限制

对于单个入站调用或出站调用，超时限制为230秒，为这些触发器和操作 (3.9 分钟) ：

* 出站请求： HTTP 触发器、HTTP 操作
* 入站请求：请求触发器，HTTP Webhook 触发器，HTTP Webhook 操作

相比之下，以下是逻辑应用及其工作流运行时的其他环境中这些触发器和操作的超时限制：

* 多租户 Azure 逻辑应用：120秒 (2 分钟) 
* Integration service 环境：240秒 (4 分钟) 

有关详细信息，请参阅 [HTTP 限制](logic-apps-limits-and-config.md#http-limits)。

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>托管的连接器

托管连接器每个连接每分钟仅限50请求。 若要处理连接器阻止问题，请参阅 [在 Azure 逻辑应用中) 处理 (429-"请求过多" 错误](handle-throttling-problems-429-errors.md#connector-throttling)。

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>内联代码操作 (执行 JavaScript 代码) 

对于单个逻辑应用定义，内联代码操作操作 [**执行 JavaScript 代码**](logic-apps-add-run-inline-code.md)具有以下更新的限制：

* 最大代码字符数从1024个字符增加到100000个字符。

* 运行代码的最长持续时间为5秒到15秒。

有关详细信息，请参阅 [逻辑应用定义限制](logic-apps-limits-and-config.md#definition-limits)。

## <a name="next-steps"></a>后续步骤

* [在 Azure 门户中创建有状态和无状态工作流](create-stateful-stateless-workflows-azure-portal.md)
* [在 Visual Studio Code 中创建有状态和无状态工作流](create-stateful-stateless-workflows-visual-studio-code.md)
* [GitHub 中的逻辑应用公共预览版已知问题页](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

此外，我们还希望收到有关 Azure 逻辑应用预览的体验！

* 对于 bug 或问题，请 [在 GitHub 中创建问题](https://github.com/Azure/logicapps/issues)。
* 对于问题、请求、评论和其他反馈，请 [使用此反馈表单](https://aka.ms/lafeedback)。