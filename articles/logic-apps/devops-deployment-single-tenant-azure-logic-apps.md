---
title: 单租户 Azure 逻辑应用的 DevOps 部署
description: 了解单租户 Azure 逻辑应用的 DevOps 部署。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: a94abb3b2c640dbd0bcd372e83844d660af514cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548180"
---
# <a name="devops-deployment-for-single-tenant-azure-logic-apps"></a>单租户 Azure 逻辑应用的 DevOps 部署

随着分布式和本机云应用的趋势，组织需要在更多环境中处理更多分布式组件。 为了保持控制和一致性，可使用 DevOps 工具和流程来自动部署环境，更快，更自信地部署更多组件。

本文介绍并概述了单租户 Azure 逻辑应用当前的持续集成和持续部署 (CI/CD) 体验。

<a name="single-tenant-versus-multi-tenant"></a>

## <a name="single-tenant-versus-multi-tenant"></a>单租户与多租户

在多租户 Azure 逻辑应用中，资源部署基于 Azure 资源管理器模板（ARM 模板），这些模板组合并处理逻辑应用和基础结构的资源预配。 在单租户 Azure 逻辑应用中，部署变得更加容易，因为你可以在应用与基础结构之间分离资源预配。

使用“逻辑应用(标准)”资源类型创建逻辑应用时，工作流由重新设计的单租户 Azure 逻辑应用运行时提供支持。 此运行时使用 [Azure Functions 扩展性模型](../azure-functions/functions-bindings-register.md)的可扩展性，并[作为扩展托管在 Azure Functions 运行时上](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)。 此设计为逻辑应用提供了可移植性、灵活性和更高的性能，以及继承自 Azure Functions 平台和 Azure 应用服务生态系统的其他功能和优势。

例如，可以将重新设计的容器化运行时和工作流一起打包为逻辑应用的一部分。 可以使用一般步骤或任务来生成、组合逻辑应用资源，并将其压缩到随时可部署的项目中。 若要部署应用，请将项目复制到主机环境，然后启动应用以运行工作流。 或者，使用你了解并使用过的工具和进程将项目集成到部署管道中。 例如，如果方案要求容器，则可以容器化逻辑应用并将其集成到现有管道中。

若要设置并部署基础结构资源（如虚拟网络和连接），可以继续使用 ARM 模板，并单独预配这些资源以及用于这些目的的其他进程和管道。

通过使用标准生成和部署选项，你可以将应用程序开发与基础结构部署分开进行关注。 因此，你将获得更通用的项目模型，你可以在其中应用多个用于通用应用的类似或相同的部署选项。 你还可以从更一致的体验中获益，为你的应用项目生成部署管道，以及在发布到生产环境之前运行所需的测试和验证。 无论使用哪种技术堆栈，都可以使用自己选择的工具部署逻辑应用。

<a name="devops-deployment-features"></a>

## <a name="devops-deployment-capabilities"></a>DevOps 部署功能

单租户 Azure 逻辑应用从 Azure Functions 平台和 Azure App Service 生态系统继承了许多功能和优势。 这些更新包括全新的部署模型，以及将 DevOps 用于逻辑应用工作流的更多方法。

<a name="local-development-testing"></a>

### <a name="local-development-and-testing"></a>本地开发和测试

将 Visual Studio Code 与 Azure 逻辑应用（标准）扩展一起使用时，可以在开发环境中本地开发、生成并运行基于单租户的逻辑应用工作流，而无需将其部署到 Azure。 还可以在 Azure Functions 可运行的位置运行工作流。 例如，如果方案要求容器，则可以容器化逻辑应用并将其部署为容器。

与多租户模型相比，这是一项重大改进，并提供了实质性的好处，因为多租户模式要求你针对 Azure 中现有的和正在运行的资源进行开发。

<a name="separate-concerns"></a>

### <a name="separate-concerns"></a>分离关注事项

单租户模型使你能够将应用与底层基础结构之间的关注事项隔离开来。 例如，可以将应用作为不可变项目单独开发、生成、压缩并部署到不同的环境。 逻辑应用工作流通常具有“应用程序代码”，与基础结构相比，需要更频繁地对该代码进行更新。 通过分隔这些层，你可以将更多精力用在生成逻辑应用的工作流上，而无需花太多的精力来跨多个环境部署所需资源。

![显示应用和基础结构的单独部署管道的概念图。](./media/devops-deployment-single-tenant/deployment-pipelines-logic-apps.png)

<a name="resource-structure"></a>

### <a name="logic-app-resource-structure"></a>逻辑应用资源结构

[!INCLUDE [Visual Studio Code - logic app resource structure](../../includes/logic-apps-single-tenant-resource-structure.md)]

### <a name="logic-app-project-structure"></a>逻辑应用项目结构

[!INCLUDE [Visual Studio Code - logic app project structure](../../includes/logic-apps-single-tenant-project-structure-visual-studio-code.md)]

<a name="deployment-containers"></a>

### <a name="container-deployment"></a>容器部署

单租户 Azure 逻辑应用支持部署到容器，这意味着可以将逻辑应用工作流容器化，并可在容器可运行的位置运行它们。 容器化应用之后，部署的工作原理与你部署和管理的任何其他容器都大致相同。

有关包括 Azure DevOps 的示例，请查看[容器的 CI/CD](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/)。

<a name="app-settings-parameters"></a>

### <a name="app-settings-and-parameters"></a>应用设置和参数

在多租户 Azure 逻辑应用中，当必须跨各种开发、测试和生产环境维护逻辑应用的环境变量时，ARM 模板会带来挑战。 ARM 模板中所有内容在部署时就已定义。 如果只需更改单个变量，必须重新部署所有内容。

在单租户 Azure 逻辑应用中，可以使用应用设置和参数在运行时调用和引用环境变量，这样你就不必经常重新部署。

<a name="managed-connectors-built-in-operations"></a>

## <a name="managed-connectors-and-built-in-operations"></a>托管的连接器和内置操作

此 Azure 逻辑应用生态系统提供[数百个 Microsoft 托管的连接器](/connectors/connector-reference/connector-reference-logicapps-connectors)和内置操作，这些连接器和内置操作是不断增长的集合的一部分，可用于单租户 Azure 逻辑应用。 Microsoft 维护这些连接器和内置操作的方式在单租户 Azure 逻辑应用中基本保持不变。

最重要的改进是单租户服务使更流行的托管连接器也作为内置操作提供。 例如，你可以将内置操作用于 Azure 服务总线、Azure 事件中心、SQL 等。 同时，托管连接器版本仍然可用并且可以继续工作。

使用内置操作创建的连接称为内置连接或服务提供程序连接。 内置操作及其连接在本地运行于与工作流相同的进程中。 两者都托管在重新设计的逻辑应用运行时中。 与此相反，托管连接（或 API 连接）作为使用 ARM 模板部署的 Azure 资源单独创建并运行。 因此，由于与工作流的邻近性，内置操作及其连接提供的性能更好。 此设计还适用于部署管道，因为服务提供程序连接被打包到相同的生成项目中。

在 Visual Studio Code 中，当你使用设计器来开发或更改工作流时，逻辑应用引擎会在项目的 connections.js 文件中自动生成任何必需的连接元数据。 以下各节介绍了可在工作流中创建的三种连接。 每种连接类型都有不同的 JSON 结构，理解这一点很重要，因为在环境之间移动时终结点会发生变化。

<a name="service-provider-connections"></a>

### <a name="service-provider-connections"></a>服务提供程序连接

当对单租户 Azure 逻辑应用中的服务（例如 Azure 服务总线或 Azure 事件中心）使用内置操作时，将创建一个服务提供程序连接，该连接在与工作流相同的进程中运行。 此连接基础结构作为逻辑应用资源的一部分进行托管和管理，并且应用设置存储工作流使用的任何基于服务提供程序的内置操作的连接字符串。

在逻辑应用项目中，每个工作流都有一个 workflow.json 文件，其中包含工作流的基础 JSON 定义。 然后，此工作流定义在项目的 connections.json 文件中引用必要的连接字符串。

以下示例展示了内置服务总线操作的服务提供程序连接如何显示在项目的 connections.json 文件中：

```json
"serviceProviderConnections": {
   "{service-bus-connection-name}": {
      "parameterValues": {
         "connectionString": "@appsetting('servicebus_connectionString')"
      },
      "serviceProvider": {
         "id": "/serviceProviders/serviceBus"
      },
      "displayName": "{service-bus-connection-name}"
   },
   <...>
}
```

<a name="managed-connections"></a>

### <a name="managed-connections"></a>托管连接

在工作流中首次使用托管连接器时，系统会提示你为目标服务或系统创建托管 API 连接，并验证标识。 这些连接器由 Azure 中的共享连接器生态系统管理。 API 连接在 Azure 中作为单独的资源存在并运行。

在 Visual Studio Code 中，当继续使用设计器创建和开发工作流时，逻辑应用引擎会自动在 Azure 中为工作流中的托管连接器创建必要的资源。 该引擎会自动将这些连接资源添加到你设计来包含逻辑应用的 Azure 资源组中。

以下示例展示了托管服务总线连接器的 API 连接如何显示在项目的 connections.json 文件中：

```json
"managedApiConnections": {
   "{service-bus-connection-name}": { 
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{region}/managedApis/servicebus"
      },
      "connection": { 
         "id": "/subscriptions/{subscription-ID}/resourcegroups/{resource-group-name}/providers/Microsoft.Web/connections/servicebus"
      }, 
      "connectionRuntimeUrl": "{connection-runtime-URL}",
      "authentication": { 
         "type": "Raw",
         "scheme": "Key",
         "parameter": "@appsetting('servicebus_1-connectionKey')"
      },
   },
   <...>
}
```

<a name="azure-functions-connections"></a>

### <a name="azure-functions-connections"></a>Azure Functions 连接

若要调用在 Azure Functions 中创建和托管的函数，请使用内置 Azure Functions 操作。 Azure Functions 调用的连接元数据与其他内置连接不同。 此元数据存储在逻辑应用项目的 connections.json 文件中，但看起来不同：

```json
"functionConnections": {
   "{function-operation-name}": {
      "function": { 
         "id": "/subscriptions/{subscription-ID}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{function-app-name}/functions/{function-name}"
      },
      "triggerUrl": "{function-url}",
      "authentication": {
        "type": "QueryString",
         "name": "Code",
         "value": "@appsetting('azureFunctionOperation_functionAppKey')"
      }, 
      "displayName": "{functions-connection-display-name}"
   },
   <...>
}
```

## <a name="authentication"></a>身份验证

在单租户 Azure 逻辑应用中，逻辑应用工作流的托管模型是单租户，与多租户模型相比，工作负载可从更多的隔离中受益。 此外，单租户 Azure 逻辑应用运行时是可移植的，这意味着可以在 Azure Functions 可运行的地方运行工作流。 不过，此设计要求逻辑应用通过一种方法来验证其标识，以便它们可以访问 Azure 中的托管连接器生态系统。 使用托管连接时，应用还需要正确的权限才能运行操作。

默认情况下，每个基于单租户的逻辑应用都有一个自动启用的、系统分配的托管标识。 该标识与创建连接时使用的身份验证凭据或连接字符串不同。 在运行时，逻辑应用使用此标识通过 Azure 访问策略对连接进行身份验证。 如果禁用该标识，则运行时连接无效。

以下各节提供了更多关于身份验证类型的详细信息，你可以根据逻辑应用的运行位置，使用这些身份验证类型来对托管连接进行身份验证。 对于每个托管连接，逻辑应用项目的 connections.js 文件都有一个 `authentication` 对象，该对象指定逻辑应用可以用来验证该托管连接的身份验证类型。

### <a name="managed-identity"></a>托管标识

对于在 Azure 中托管和运行的逻辑应用，[托管标识](create-managed-service-identity.md)是默认的和推荐的身份验证类型，用于对 Azure 中托管和运行的托管连接进行身份验证。 在逻辑应用项目的 connections.json 文件中，托管连接有一个 `authentication` 对象，该对象指定 `ManagedServiceIdentity` 为身份验证类型：

```json
"authentication": {
   "type": "ManagedServiceIdentity"
}
```

### <a name="raw"></a>原始

对于使用 Visual Studio Code 在本地开发环境中运行的逻辑应用，将使用原始身份验证密钥来验证在 Azure 中托管和运行的托管连接。 这些密钥仅用于开发用途，不能用于生产，并且有效期为 7 天。 在逻辑应用项目的 connections.json 文件中，托管连接具有一个 `authentication` 对象，该对象指定以下身份验证信息：

```json
"authentication": {
   "type": "Raw", 
   "scheme": "Key", 
   "parameter": "@appsetting('connectionKey')"
 }
```

## <a name="next-steps"></a>后续步骤

- [为单租户 Azure 逻辑应用设置 DevOps 部署](set-up-devops-deployment-single-tenant-azure-logic-apps.md)
