---
title: 单租户与多租户 Azure 逻辑应用
description: 了解适用于 Azure 逻辑应用的单租户、多租户和集成服务环境 (ISE) 之间的差异。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 61dbf2f83ad135cfdef6fffcc3a8c162d0a4c0cd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111448"
---
# <a name="single-tenant-versus-multi-tenant-and-integration-service-environment-for-azure-logic-apps"></a>适用于 Azure 逻辑应用的单租户与多租户和集成服务环境的比较情况

Azure 逻辑应用是一个基于云的平台，用于创建和运行集成应用、数据、服务和系统的自动化逻辑应用工作流。 借助此平台，可以快速为企业和企业到企业 (B2B) 方案开发高度可缩放的集成解决方案。 若要创建逻辑应用，请使用逻辑应用（消耗版）资源类型或逻辑应用（标准版）资源类型 。 消耗版资源类型在多租户 Azure 逻辑应用或集成服务环境中运行，而标准版资源类型在单租户 Azure 逻辑应用环境中运行  。

在选择要使用的资源类型之前，请阅读本文以了解资源类型和服务环境之间的比较方式。 然后可根据场景需求，解决方案要求以及要在其中部署、托管和运行工作流的环境决定最适合使用的类型。

如果你刚接触 Azure 逻辑应用，请查看以下文档：

* [什么是 Azure 逻辑应用？](logic-apps-overview.md)
* [什么是逻辑应用工作流？](logic-apps-overview.md#logic-app-concepts)

<a name="resource-environment-differences"></a>

## <a name="resource-types-and-environments"></a>资源类型和环境

若要创建逻辑应用工作流，请根据你的方案、解决方案要求、所需功能以及要在其中运行工作流的环境来选择逻辑应用资源类型。

下表简要概述了逻辑应用（标准版）资源类型和逻辑应用（消耗版）资源类型之间的差异 。 你还将了解单租户环境与多租户和集成服务环境 (ISE) 在部署、托管和运行逻辑应用工作流方面的比较情况  。

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="resource-type-introduction"></a>

## <a name="logic-app-standard-resource"></a>逻辑应用（标准版）资源

逻辑应用（标准版）资源类型由重新设计的单租户 Azure 逻辑应用运行时提供支持。 此运行时采用 [Azure Functions 扩展性模型](../azure-functions/functions-bindings-register.md)，并作为扩展托管在 Azure Functions 运行时上。 此设计为逻辑应用工作流提供了可移植性、灵活性和更高的性能，以及继承自 Azure Functions 平台和 Azure 应用服务生态系统的其他功能和优势。

例如，可以在 Azure 函数应用及其函数可以运行的任何位置运行基于单租户的逻辑应用及其工作流。 标准版资源类型引入了一个可托管多个工作流的资源结构，与 Azure 函数应用托管多个函数的方式类似。 使用一对多映射，同一逻辑应用和租户中的工作流可以共享计算和处理资源，从而基于其邻近性而提供更好的性能。 此结构不同于逻辑应用（消耗版）资源，在消耗版资源中，逻辑应用资源和工作流之间具有一对一的映射关系。

若要了解有关可移植性、灵活性和性能改进的详细信息，请继续阅读以下各节。 或者，有关单租户 Azure 逻辑应用运行时和 Azure Functions 扩展性的详细信息，请查看以下文档：

* [Azure 逻辑应用随处运行 - 运行时深度探索](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)
* [Azure Functions 简介](../azure-functions/functions-overview.md)
* [Azure Functions 触发器和绑定](../azure-functions/functions-triggers-bindings.md)

<a name="portability"></a>
<a name="flexibility"></a>

### <a name="portability-and-flexibility"></a>可移植性和灵活性

如果你使用逻辑应用（标准版）创建逻辑应用，则可以在可运行 Azure 函数应用及其函数的任何位置运行工作流，而不仅仅是单租户服务环境中。

例如，将 Visual Studio Code 与 Azure 逻辑应用（标准版）扩展一起使用时，可以在开发环境中本地开发、生成并运行工作流，而无需将其部署到 Azure。 如果你的方案需要容器，请[使用已启用 Azure Arc 的逻辑应用创建基于单租户的逻辑应用](azure-arc-enabled-logic-apps-create-deploy-workflows.md)。 有关详细信息，请参阅[什么是已启用 Azure Arc 的逻辑应用？](azure-arc-enabled-logic-apps-overview.md)

与多租户模型相比，这些功能提供了重大改进以及实质性的好处，因为多租户模式要求你针对 Azure 中现有的正在运行的资源进行开发。 此外，用于自动执行逻辑应用（消耗版）资源部署的多租户模型完全基于 Azure 资源管理器模板（ARM 模板），这些模板组合并处理逻辑应用和基础结构的资源预配。

使用逻辑应用（标准版）资源类型，部署会容易一些，因为可以将应用部署与基础结构部署相分离。 例如，可以将单租户 Azure 逻辑应用运行时和工作流一起打包为逻辑应用的一部分。 可以使用一般步骤或任务来生成、组合逻辑应用资源，并将其压缩到随时可部署的项目中。 若要部署基础结构，仍可以使用 ARM 模板单独预配这些资源以及用于这些目的的其他进程和管道。

若要部署应用，请将项目复制到主机环境，然后启动应用以运行工作流。 或者，使用你了解并使用过的工具和进程将项目集成到部署管道中。 这样，无论用于开发的技术堆栈如何，都可以使用自己选择的工具进行部署。

通过使用标准生成和部署选项，你可以将应用开发与基础结构部署分开进行关注。 因此，你将获得更通用的项目模型，你可以在其中应用多个用于通用应用的类似或相同的部署选项。 你还可以从更一致的体验中获益，为你的应用项目生成部署管道，以及在发布到生产环境之前运行所需的测试和验证。

<a name="performance"></a>

### <a name="performance"></a>性能

使用逻辑应用（标准版）资源类型，可以在同一个逻辑应用和租户中创建和运行多个工作流。 通过这种一对多的映射，这些工作流共享计算、处理、存储和网络等资源，从而基于其邻近性而提供更好的性能。

逻辑应用（标准版）资源类型和单租户 Azure 逻辑应用运行时将更常用的托管连接器作为内置操作提供，从而实现了另一项显著的改进。 例如，你可以将内置操作用于 Azure 服务总线、Azure 事件中心、SQL 等。 同时，托管连接器版本仍然可用并且可以继续工作。

使用新的内置操作时，将创建称为“内置连接”或“服务提供程序连接”的连接 。 它们对应的托管连接称为 API 连接，该连接作为随后必须使用 ARM 模板进行部署的 Azure 资源单独创建并运行。 内置操作及其连接在本地运行于与工作流相同的进程中。 两者都托管在单租户 Azure 逻辑应用运行时上。 因此，由于与工作流的邻近性，内置操作及其连接提供的性能更好。 此设计还适用于部署管道，因为服务提供程序连接被打包到相同的生成项目中。

## <a name="create-build-and-deploy-options"></a>创建、生成和部署选项

若要基于所需环境创建逻辑应用，可以使用多个选项，例如：

单租户环境

| 选项 | 资源和工具 | 更多信息 |
|--------|---------------------|------------------|
| Azure 门户 | 逻辑应用（标准版）资源类型 | [为单租户逻辑应用创建集成工作流 - Azure 门户](create-single-tenant-workflows-azure-portal.md) |
| Visual Studio Code | [Azure 逻辑应用（标准版）扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurelogicapps) | [为单租户逻辑应用创建集成工作流 - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md) |
| Azure CLI | 逻辑应用 Azure CLI 扩展 | 目前不可用 |
||||

**多租户环境**

| 选项 | 资源和工具 | 更多信息 |
|--------|---------------------|------------------|
| Azure 门户 | 逻辑应用（消耗版）资源类型 | [快速入门：在多租户 Azure 逻辑应用中创建集成工作流 - Azure 门户](quickstart-create-first-logic-app-workflow.md) |
| Visual Studio Code | [Azure 逻辑应用（消耗版）扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) | [快速入门：在多租户 Azure 逻辑应用中创建集成工作流 - Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
| Azure CLI | [逻辑应用 Azure CLI 扩展](https://github.com/Azure/azure-cli-extensions/tree/master/src/logic) | - [快速入门：在多租户 Azure 逻辑应用中创建和管理集成工作流 - Azure CLI](quickstart-logic-apps-azure-cli.md) <p><p>- [az logic](/cli/azure/logic) |
| Azure 资源管理器 | [创建逻辑应用 ARM 模板](https://azure.microsoft.com/resources/templates/logic-app-create/) | [快速入门：在多租户 Azure 逻辑应用中创建和部署集成工作流 - ARM 模板](quickstart-create-deploy-azure-resource-manager-template.md) |
| Azure PowerShell | [Az.LogicApp 模块](/powershell/module/az.logicapp) | [Azure PowerShell 入门](/powershell/azure/get-started-azureps) |
| Azure REST API | [Azure 逻辑应用 REST API](/rest/api/logic) | [Azure REST API 参考入门](/rest/api/azure) |
||||

集成服务环境

| 选项 | 资源和工具 | 更多信息 |
|--------|---------------------|------------------|
| Azure 门户 | 带有现有 ISE 资源的逻辑应用（消耗版）资源类型 | 与[快速入门：在多租户 Azure 逻辑应用中创建集成工作流 - Azure 门户](quickstart-create-first-logic-app-workflow.md)相同，但选择的是 ISE，而不是多租户区域。 |
||||

尽管开发体验因创建的逻辑应用资源类型（消耗版或标准版）而异，但可以在 Azure 订阅下找到并访问所有已部署的逻辑应用 。

例如，在 Azure 门户中，逻辑应用页同时显示消耗版和标准版逻辑应用资源类型  。 在 Visual Studio Code 中，已部署的逻辑应用显示在 Azure 订阅下，但按所使用的扩展进行分组，即“Azure：逻辑应用（消耗版）”和“Azure：逻辑应用（标准版）” 。

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>有状态和无状态工作流

使用逻辑应用（标准版）资源类型，可在同一逻辑应用中创建这些工作流类型：

* 有状态

  如果需要保留、查看或引用来自先前事件的数据，则创建有状态工作流。 这些工作流将每个操作的所有输入和输出及其状态保存并传输到外部存储，这样就可以在每个运行完成后查看运行详细信息和历史记录。 如果服务中断，有状态工作流提供高复原能力。 在服务和系统还原后，你可从已保存的状态重新构造中断的运行，并再次运行工作流来完成操作。 有状态工作流可以比无状态工作流持续运行更长的时间。

* *无状态*

  如果不需要在每个运行完成后保留、查看或引用外部存储中的先前事件的数据（供以后查看），请创建无状态工作流。 这些工作流将每个操作的所有输入和输出及其状态仅保存在内存中，而不保存在外部存储中。 结果就是，由于运行详细信息和历史记录未保存在外部存储中，因此无状态工作流的运行时间更短（通常不到 5 分钟）、性能更高、响应时间更短、吞吐量更高且运行成本更低。 不过，如果服务中断，已中断的运行不会自动还原，因此调用方需要重新手动提交已中断的运行。 这些工作流只能同步运行。

  > [!IMPORTANT]
  > 无状态工作流在处理总大小不超过 64 KB 的数据或内容（如文件）时提供最佳性能。 内容较大（例如，多个大型附件）可能会显著降低工作流的性能，甚至会由于内存不足异常而导致工作流崩溃。 如果工作流可能需要处理较大的内容，请改用有状态工作流。

  为了简化调试，你可为无状态工作流启用运行历史记录（这对性能有一些影响），然后在完成时再禁用运行历史记录。 有关详细信息，请参阅[在 Visual Studio Code 中创建基于单租户的工作流](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless)或[在 Azure 门户中创建基于单租户的工作流](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless)。

  > [!NOTE]
  > 对于部署在 Azure 中的[托管连接器](../connectors/managed.md)，无状态工作流目前仅支持操作，不支持触发器。 若要启动工作流，请选择[内置的请求、事件中心或服务总线触发器](../connectors/built-in.md)。 这些触发器在 Azure 逻辑应用运行时中以本机方式运行。 若要详细了解受限、不可用或不受支持的触发器、操作和连接器，请查看[已更改、受限、不可用或不受支持的功能](#limited-unavailable-unsupported)。

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>有状态和无状态工作流之前的嵌套行为差异

可使用[请求触发器](../connectors/connectors-native-reqres.md)、[HTTP Webhook 触发器](../connectors/connectors-native-webhook.md)，或者具有 [ApiConnectionWebhook 类型](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)且可接收 HTTPS 请求的托管连接器触发器，将工作流设置为可从同一“逻辑应用（标准版）”资源中存在的其他工作流[调用](../logic-apps/logic-apps-http-endpoint.md)。

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

<a name="other-capabilities"></a>

## <a name="other-single-tenant-model-capabilities"></a>其他单租户模型功能

单租户模型和逻辑应用（标准版）资源类型包括许多当前功能和新功能，例如：

* 通过[超过 400 个托管连接器](/connectors/connector-reference/connector-reference-logicapps-connectors)，针对本地系统的服务型软件 (SaaS) 应用、平台即服务 (PaaS) 和服务加连接器创建逻辑应用及其工作流。

  * 现在，更多托管连接器作为内置操作提供，并以类似于其他内置操作（如 Azure Functions）的方式运行。 内置操作在单租户 Azure 逻辑应用运行时上以本机方式运行。 例如，新的内置操作包括 Azure 服务总线、Azure 事件中心、SQL Server 和 MQ。

    > [!NOTE]
    > 对于内置 SQL Server 版本，只有“执行查询”操作可以直接连接到 Azure 虚拟网络，而不需要使用[本地数据网关](logic-apps-gateway-connection.md)。

  * 可以使用[单租户 Azure 逻辑应用扩展性框架](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)为所需的所有服务创建自己的内置连接器。 自定义内置连接器与 Azure 服务总线和 SQL Server 等内置操作类似，但不同于[自定义托管连接器](../connectors/apis-list.md#custom-apis-and-connectors)（当前不受支持），它们提供更高吞吐量、低延迟和本地连接，因为它们在与单租户运行时相同的进程中运行。

    目前仅可在 Visual Studio Code 中使用创作功能，该功能默认情况下不启用。 若要创建这些连接器，请[将项目从基于扩展捆绑 (Node.js) 切换到基于 NuGet 包 (.NET)](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)。 有关详细信息，请查看 [Azure 逻辑应用随处运行 - 内置连接器扩展性](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)。

  * 如果没有集成帐户，可对 Liquid 操作和 XML 操作使用以下操作。 这些操作包括以下操作：

    * XML：转换 XML 和 XML 验证 

    * Liquid：将 JSON 转换为 JSON、将 JSON 转换为文本、将 XML 转换为 JSON 以及将 XML 转换为文本   

    > [!NOTE]
    > 若要在单租户 Azure 逻辑应用（标准版）中使用这些操作，需要有 Liquid 映射、XML 映射或 XML 架构。 可以在 Azure 门户中，从逻辑应用的资源菜单的“项目”（下其中包括“架构”和“映射”部分），上传这些项目  。 或者，可以使用相应的“映射”和“架构”文件夹，将这些项目添加到 Visual Studio Code 项目的“项目”文件夹  。 然后，可以在同一逻辑应用资源中跨多个工作流使用这些项目。

  * 逻辑应用（标准版）资源可以在任何位置运行，因为 Azure 逻辑应用可生成共享访问签名 (SAS) 连接字符串，而这些逻辑应用可使用这些字符串将请求发送到云连接运行时终结点。 Azure 逻辑应用服务会将这些连接字符串随其他应用程序设置一起保存，这样你在 Azure 中部署时，可在 Azure Key Vault 中轻松存储这些值。

    > [!NOTE]
    > 默认情况下，逻辑应用（标准版）资源具有[系统分配的托管标识](../logic-apps/create-managed-service-identity.md)，该标识已自动启用以在运行时对连接进行身份验证。 该标识与你在创建连接时使用的身份验证凭据或连接字符串不同。 如果禁用该标识，则运行时连接无效。 若要查看此设置，请在逻辑应用菜单的“设置”下，选择“标识” 。

* 可在 Visual Studio Code 开发环境中本地运行、测试和调试逻辑应用及其工作流。

  在运行和测试逻辑应用之前，可在工作流的 workflow.json 文件中添加和使用断点来简化调试。 不过，断点目前仅可用于操作，不可用于触发器。 有关详细信息，请参阅[在 Visual Studio Code 中创建基于单租户的工作流](create-single-tenant-workflows-visual-studio-code.md#manage-breakpoints)。

* 将逻辑应用及其工作流从 Visual Studio Code 直接发布或部署到各种托管环境中，例如 Azure 和已启用 Azure Arc 的逻辑应用。

* 如果你的 Azure 订阅和逻辑应用设置支持，可使用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 为逻辑应用启用诊断日志记录和跟踪功能。

* 访问网络功能（例如连接功能）并以专用方式与 Azure 虚拟网络集成，这与你在使用 [Azure Functions 高级计划](../azure-functions/functions-premium-plan.md)创建和部署逻辑应用时的 Azure Functions 很相似。 有关详细信息，请查看以下文档：

  * [Azure Functions 网络选项](../azure-functions/functions-networking-options.md)

  * [随处运行的 Azure 逻辑应用 - Azure 逻辑应用进行网络连接的可能性](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* 为逻辑应用（标准版）资源中单个工作流使用的托管连接重新生成访问密钥。 在此任务中，请[针对逻辑应用（消耗版）资源在单独工作流级别（而不是逻辑应用资源级别）执行相同步骤](logic-apps-securing-a-logic-app.md#regenerate-access-keys)。

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>已更改、受限、不可用或不受支持的功能

对于逻辑应用（标准版）资源，下列功能已经过更改，它们目前功能受限、不可用或不受支持：

* **触发器和操作**：内置触发器和操作在单租户 Azure 逻辑应用运行时中以本机方式运行，而托管连接器在 Azure 中托管和运行。 某些内置触发器不可用，例如“滑动窗口”和“批处理”。 若要启动有状态或无状态工作流，请使用[内置的重复周期、请求HTTP、HTTP Webhook、事件中心或服务总线触发器](../connectors/apis-list.md)。 在设计器中，内置触发器和操作显示在“内置”选项卡下。

  对于有状态工作流，除了下面列出的不可用操作外，[托管连接器触发器和操作](../connectors/managed.md)显示在“Azure”选项卡下。 对于无状态工作流，选择触发器时不会显示“Azure”选项卡。 仅可选择[托管连接器操作，不可选择触发器](../connectors/managed.md)。 虽然可为无状态工作流启用 Azure 托管的托管连接器，但设计器不会显示任何托管连接器触发器供你添加。

  > [!NOTE]
  > 若要在 Visual Studio Code 中本地运行，基于 Webhook 的触发器和操作需要额外设置。 有关详细信息，请参阅[在 Visual Studio Code 中创建基于单租户的工作流](create-single-tenant-workflows-visual-studio-code.md#webhook-setup)。

  * 以下触发器和操作已经过更改，或者目前功能受限、不受支持或不可用：

    * [本地数据网关触发器](../connectors/managed.md#on-premises-connectors)不可用，但网关操作可用 。

    * [Azure Functions - 选择 Azure 函数](logic-apps-azure-functions.md)这一内置操作现在名为“Azure 函数操作 - 调用 Azure 函数”。 此操作目前仅适用于通过 HTTP 触发器模板创建的函数。

      在 Azure 门户中，可选择一个 HTTP 触发器函数，你可通过用户体验创建连接来访问此函数。 如果使用 Visual Studio Code 在代码视图或 workflow.json 文件中检查函数操作的 JSON 定义，则操作会使用 `connectionName` 引用来引用函数。 此版本会将函数的信息抽象化处理为一个连接，可在逻辑应用项目的 connections.json 文件中找到它，这是在 Visual Studio Code 中创建连接后提供的。

      > [!NOTE]
      > 在单租户模型中，函数操作仅支持查询字符串身份验证。 Azure 逻辑应用会在建立连接时从函数中获取默认密钥、将该密钥存储在应用的设置中，并在调用函数时使用该密钥来进行身份验证。
      >
      > 与多租户模型一样，如果你续订此密钥（例如通过门户中的 Azure Functions 体验），且由于密钥无效，函数操作会失效。 若要解决此问题，需要重新创建与你想要调用的函数的连接，或者使用新密钥更新应用的设置。

    * 内置操作[内联代码](logic-apps-add-run-inline-code.md)被重命名为“内联代码操作”，不再需要集成帐户，并且具有[更新后的限制](logic-apps-limits-and-config.md)。

    * [Azure 逻辑应用 - 创建逻辑应用工作流](logic-apps-http-endpoint.md)这一内置操作现在名为“工作流操作 - 在此工作流应用中调用工作流”。

    * [集成帐户的某些内置触发器和操作](../connectors/managed.md#integration-account-connectors)不可用，例如平面文件编码和解码操作。

    * 当前不支持[自定义托管连接器](../connectors/apis-list.md#custom-apis-and-connectors)。 但是，在使用 Visual Studio Code 时，可以创建自定义内置操作。 有关详细信息，请查看[使用 Visual Studio Code 创建基于单租户的工作流](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)。

* 对于 XML 转换，对通过映射引用程序集的支持当前不可用。 此外，当前仅支持 XSLT 1.0。

* **Visual Studio Code 中的断点调试**：虽然可在工作流的 workflow.json 文件中添加和使用断点，但目前仅可对操作使用断点，不可将其用于触发器。 有关详细信息，请参阅[在 Visual Studio Code 中创建基于单租户的工作流](create-single-tenant-workflows-visual-studio-code.md#manage-breakpoints)。

* **触发器历史记录和运行历史记录**：对于“逻辑应用（标准版）”资源类型，Azure 门户中的触发器历史记录和运行历史记录显示在工作流级别中，而非逻辑应用级别中。 有关详细信息，请查看[使用 Azure 门户创建基于单租户的工作流](create-single-tenant-workflows-azure-portal.md)。

* **缩放控件**：目前不可在设计器上使用缩放控件。

* **部署目标**：无法将逻辑应用（标准版）资源类型部署到 [集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 或 Azure 部署槽位。

* **Azure API 管理**：当前无法将逻辑应用（标准版）资源类型导入 Azure API 管理。 但可以导入逻辑应用（消耗版）资源类型。

<a name="firewall-permissions"></a>

## <a name="strict-network-and-firewall-traffic-permissions"></a>严格的网络和防火墙流量权限

如果你的环境设有限制流量的严格网络要求或防火墙，则必须允许访问以在逻辑应用工作流中实现任何触发器或操作连接。 若要查找这些连接的完全限定的域名 (FQDN)，请查看以下主题中的相应部分：

* [单租户逻辑应用的防火墙权限 - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)
* [单租户逻辑应用的防火墙权限 - Azure 门户](create-single-tenant-workflows-azure-portal.md#firewall-setup)

## <a name="next-steps"></a>后续步骤

* [在 Azure 门户中创建基于单租户的工作流](create-single-tenant-workflows-azure-portal.md)
* [在 Visual Studio Code 中创建基于单租户的工作流](create-single-tenant-workflows-visual-studio-code.md)

我们还希望你就单租户 Azure 逻辑应用体验提供反馈！

* 如果发现 bug 或问题，请[在 GitHub 中创建问题](https://github.com/Azure/logicapps/issues)。
* 如有问题、请求、意见和其他反馈，请[使用此反馈表单](https://aka.ms/lafeedback)。
