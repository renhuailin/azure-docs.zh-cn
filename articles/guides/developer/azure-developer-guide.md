---
title: 面向 Azure 开发人员的入门指南 | Microsoft 文档
description: 本文面向希望开始使用 Microsoft Azure 平台以满足自己开发需求的开发人员提供了相关的基本信息。
author: ggailey777
ms.service: azure
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: ae3f62141fdcdb1185f493eceeef7124fe11f869
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727852"
---
# <a name="get-started-guide-for-azure-developers"></a>Azure 开发人员入门指南

## <a name="what-is-azure"></a>什么是 Azure？

Azure 是一个完整的云平台，可以托管用户现有的应用程序并简化新应用程序的开发。 Azure 甚至可以增强本地应用程序的功能。 Azure 集成了开发、测试、部署和管理应用程序所需的各种云服务，同时充分利用云计算的效率。

通过在 Azure 中托管应用程序，你可以随着客户需求的增长，从小规模开始轻松扩展应用程序。 另外，Azure 还可以针对高可用性应用程序提供所需的可靠性，甚至包括在两个不同区域之间的故障转移。 通过 [Azure 门户](https://portal.azure.com)，可让你轻松管理所有的 Azure 服务。 同时，你还可以通过使用特定于服务的 API 和模板以编程方式管理你的服务。

本指南介绍面向应用程序开发人员的 Azure 平台。 针对在 Azure 中开始生成新的应用程序或将现有应用程序迁移到 Azure 所需的操作，提供相关指导和说明。

## <a name="where-do-i-start"></a>从哪里开始？

面对 Azure 提供的众多服务，想要从中找出需要哪些服务来支持你的解决方案体系结构，可能会是一项很棘手的任务。 本部分将重点介绍开发人员通常使用的 Azure 服务。 有关所有 Azure 服务列表，请参阅 [Azure 文档](../../index.yml)。

首先，你必须确定在 Azure 中托管应用程序的方式。 是否需要将整个基础结构作为一台虚拟机 (VM) 来管理？ 是否可以使用 Azure 提供的平台管理功能？ 或许你只需要一个无服务器框架来托管代码执行？

应用程序需要云存储，Azure 为此提供了若干选项。 你可以利用 Azure 的企业身份验证。 此外，还有用于基于云开发和监视的各种工具，并且大多数托管服务都提供 DevOps 集成。

现在，让我们先看一下我们建议的用于应用程序调查的特定服务。

### <a name="application-hosting"></a>应用程序托管

Azure 提供了多个基于云的计算服务来运行你的应用程序，因此，你无需担心基础结构详细信息。 随着应用程序使用率的增长，可轻松纵向扩展或横向扩展你的资源。

Azure 提供了支持应用程序开发和托管需求的服务。 Azure 还提供了基础结构即服务 (IaaS)，以便让你完全控制应用程序托管。 Azure 的平台即服务 (PaaS) 产品提供了支持应用所需的完全托管的服务。 在 Azure 中甚至还有真正的无服务器托管，在其中需要做的就是编写代码。

![Azure 应用程序托管选项](./media/azure-developer-guide/azure-developer-hosting-options.png)

#### <a name="azure-app-service"></a>Azure 应用服务

如果你希望以最快路径发布基于 Web 的项目，则可以考虑使用 Azure 应用服务。 通过应用服务，可以轻松扩展 Web 应用以支持移动客户端，并发布易于使用的 REST API。 此平台通过使用社交提供程序、基于流量的自动缩放、在生产中测试和基于容器的持续部署来提供身份验证。

可以创建 Web 应用、移动应用后端和 API 应用。

由于所有上述三个应用类型均共享应用服务运行时，因此，你可以托管网站，支持移动客户端，并在 Azure 中公开你的 API，所有这些均可在同一个项目或解决方案中完成。 若要了解有关应用服务的详细信息，请参阅[什么是 Azure Web 应用](../../app-service/overview.md)。

应用服务在设计之初就考虑到了 DevOps。 它支持用于发布和持续集成部署的各种工具。 这些工具包括 GitHub webhook、Jenkins、Azure DevOps、TeamCity 等。

你可以通过使用[联机迁移工具](https://appmigration.microsoft.com/)将现有应用程序迁移到应用服务。

> 何时使用：在将现有 Web 应用迁移到 Azure 时，以及在 Web 应用需要完全托管的托管平台时，使用应用服务。 另外，当需要支持移动客户端或者使用你的应用公开 REST API时，也可以使用应用服务。
>
> **开始使用**：通过应用服务，可以轻松创建和部署你的第一个 [Web 应用](../../app-service/quickstart-dotnetcore.md)、[移动应用](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started)，或 [API 应用](../../app-service/app-service-web-tutorial-rest-api.md)。
>
> **立即试用**：通过应用服务，可以设置一个临时应用来试用该平台，而无需注册 Azure 帐户。 试用平台并[创建 Azure 应用服务应用](https://tryappservice.azure.com/)。

#### <a name="azure-virtual-machines"></a>Azure 虚拟机

作为一个基础结构即服务 (IaaS) 提供程序，Azure 可让你部署到或将应用程序迁移到 Windows 或 Linux VM。 Azure 虚拟机连同 Azure 虚拟网络一起，可支持将 Windows 或 Linux VM 部署到 Azure。 通过 VM，你可以全面控制计算机的配置。 在使用 VM 时，用户负责所有服务器软件的安装、配置、维护和操作系统修补。

由于用户对 VM 所具有的控制级别，用户可以在 Azure 上运行大量不适合 PaaS 模型的服务器工作负荷。 这些工作负载可包括数据库服务器、Windows Server Active Directory 和 Microsoft SharePoint。 有关详细信息，请参阅 [Linux](../../virtual-machines/index.yml) 或 [Windows](../../virtual-machines/index.yml) 的虚拟机文档。

> **何时使用**：在你需要完全控制应用程序基础结构，或将本地应用程序工作负载迁移到 Azure 而无需进行更改的情况下，可以使用虚拟机。
>
> **开始使用**：从 Azure 门户创建 [Linux VM](../../virtual-machines/linux/quick-create-portal.md) 或 [Windows VM](../../virtual-machines/windows/quick-create-portal.md)。

#### <a name="azure-functions-serverless"></a>Azure Functions（无服务器）

如果用户只需编写代码，然后让代码在响应事件时运行或按计划运行，无需担忧为了运行代码要如何构建和管理整个应用程序或基础结构，结果会怎样？  [Azure Functions](../../azure-functions/functions-overview.md) 是一个“无服务器”样式的产品，可让你仅编写所需的代码。 借助 Functions，可以通过 HTTP 请求、webhook、云服务事件，或按计划触发代码执行。 你可以使用所选的开发语言开发编码，如 C\#、F\#、Node.js、Python 或 PHP。 使用基于消耗的计费方式，只需要支付代码执行的时间，并且 Azure 可根据需要进行扩展。

> **何时使用**：在你具有由其他 Azure 服务、基于 Web 的事件，或按计划触发的代码的情况下，可以使用 Azure Functions。 此外，当你不需要完整托管项目的开销或者只想支付代码运行时间时，也可以使用 Functions。 有关详细信息，请参阅 [Azure Functions 概览](../../azure-functions/functions-overview.md)。
>
> **开始使用**：按照 Functions 快速入门教程，从门户 [创建你的第一个函数](../../azure-functions/functions-get-started.md)。
>
> **立即试用**：Azure Functions 可让你运行代码而无需注册 Azure 帐户。 立即试用并[创建你的第一个 Azure Function](https://tryappservice.azure.com/)。

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric 是分布式系统平台。 利用此平台，可以轻松生成、打包、部署和管理可缩放并且可靠的微服务。 它还提供了全面的应用程序管理功能，例如：

* 设置
* 正在部署
* 监视
* 升级/修补
* 正在删除

在共享计算机池上运行的应用可以从小规模开始，再根据需要扩展为成百上千个计算机。

Service Fabric 支持具有 Open Web Interface for .NET (OWIN) 和 ASP.NET Core 的 WebAPI。 它提供了用于在 Linux 上使用 .NET Core 和 Java 构建服务的 SDK。 若要了解有关 Service Fabric 的详细信息，请参阅 [Service Fabric 文档](../../service-fabric/index.yml)。

> 何时使用：在创建应用程序或重新编写现有的应用程序以便使用微服务体系结构时，Service Fabric 是一个不错的选择。 当你需要更好地控制或直接访问底层基础结构时，也可以使用 Service Fabric。
>
> **开始使用：** [创建第一个 Azure Service Fabric 应用程序](../../service-fabric/service-fabric-tutorial-create-dotnet-app.md)。

#### <a name="azure-spring-cloud"></a>Azure Spring Cloud

Azure Spring Cloud 是无服务器的微服务平台，利用该平台，可以在云中构建、部署、缩放和监视应用程序。 使用 Spring Cloud 将新式微服务模式引入 Spring Boot 应用，消除样板代码以便快速构建可靠的 Java 应用。

* 利用 Spring Cloud Service Discovery 和 Config Server 的托管版本，而同时我们会确保这些关键组件在最佳条件下运行。
* 用户只需专注于构建业务逻辑，我们会利用安全修补程序、符合性标准和高可用性来管理服务运行时。
* 在 Azure Kubernetes 服务的上方管理应用程序生命周期（例如，部署、启动、停止、缩放）。
* 在应用与 Azure 服务（例如 Azure Database for MySQL 和 Azure Cache for Redis）之间轻松绑定连接。
* 使用企业级的统一监视工具来监视微服务和应用程序并排查相关问题，并深入了解应用程序依赖项和操作遥测。

> 何时使用：当尽可能降低在 Azure 上运行基于 Spring Boot/Spring Cloud 的微服务的操作成本时，Azure Spring Cloud 作为一项完全托管的服务，是个不错的选择。 
>
> 开始使用：[部署第一个 Azure Spring Cloud 应用程序](../../spring-cloud/quickstart.md)。


### <a name="enhance-your-applications-with-azure-services"></a>使用 Azure 服务增强应用程序

除了应用程序托管，Azure 还提供可增强功能的服务产品。 另外，Azure 在云中和本地都可以改进应用程序的开发和维护。

#### <a name="hosted-storage-and-data-access"></a>托管存储和数据访问

大多数应用程序都必须存储数据，因此，无论决定如何在 Azure 中托管应用程序，都请考虑以下一个或多个存储和数据服务。

* Azure Cosmos DB：一种全球分布式多模型数据库服务。 利用此数据库，用户可以通过综合性 SLA 跨任意数量的地理区域以弹性方式缩放吞吐量和存储空间。

  > **何时使用：** 当应用程序需要文档、表或图形数据库（包括具有多个妥善定义的一致性模型的 MongoDB 数据库）时。
  >
  > **开始使用**：[构建 Azure Cosmos DB Web 应用](../../cosmos-db/create-sql-api-dotnet.md)。 如果是 MongoDB 开发者，请参阅[构建使用 Azure Cosmos DB 的 MongoDB Web 应用](../../cosmos-db/create-mongodb-dotnet.md)。

* Azure 存储：可针对 blob、队列、文件和其他类型非关系数据提供持久且高可用性的存储。 存储服务为 VM 提供了存储基础。

  > **何时使用**：在应用存储非关系数据，如键值对（表）、blob、文件共享，或消息（队列）的情况下使用。
  >
  > 开始使用：请选择这些存储类型之一：[Blob](../../storage/blobs/storage-quickstart-blobs-dotnet.md)、[表](../../cosmos-db/tutorial-develop-table-dotnet.md)、[队列](../../storage/queues/storage-dotnet-how-to-use-queues.md)或[文件](../../storage/files/storage-dotnet-how-to-use-files.md)。

* Azure SQL 数据库：基于 Azure 版本的 Microsoft SQL Server 引擎，用于在云中存储关系表格数据。 SQL 数据库可提供可预测的性能、在不停机情况下进行缩放、业务连续性和数据保护功能。

  > **何时使用**：在应用程序需要具有引用完整性、事务支持和 TSQL 查询支持的数据存储时，可以考虑使用。
  >
  > 开始使用：[通过使用 Azure 门户几分钟之内在 Azure SQL 数据库中创建数据库](../../azure-sql/database/single-database-create-quickstart.md)。


你可以使用 [Azure 数据工厂](../../data-factory/introduction.md)将现有本地数据移到 Azure。 如果还没有准备好将数据移到云中，可以利用 Azure 应用服务中的[混合连接](../../app-service/app-service-hybrid-connections.md)将应用服务托管的应用连接到本地资源。 此外，你还可以从本地应用程序连接到 Azure 数据和存储服务。

#### <a name="docker-support"></a>Docker 支持

Docker 容器是操作系统虚拟化的一种形式，可让你以更高效且可预见的方式部署应用程序。 容器化应用程序在生产环境中的工作方式与在开发和测试系统上的工作方式相同。 你可以通过使用标准的 Docker 工具来管理容器。 可以使用现有技能和常用的开源工具在 Azure 上部署和管理基于容器的应用程序。

Azure 提供了多种方式以便在应用程序中使用容器。


* Azure Kubernetes 服务：可用于创建、配置和管理虚拟机群集，该群集为运行容器化应用程序进行了预配置。 如果要详细了解 Azure Kubernetes 服务，请参阅 [Azure Kubernetes 服务简介](../../aks/intro-kubernetes.md)。

  > 何时使用：在需要构建可提供其他计划和管理工具的生产就绪型可缩放环境时，或者在部署 Docker Swarm 群集时。
  >
  > 开始使用：[部署 Kubernetes 服务群集](../../aks/tutorial-kubernetes-deploy-cluster.md)。

* Docker 计算机：允许使用 docker 计算机命令安装和管理虚拟主机上的 Docker 引擎。

  >**何时使用**：当需要通过创建一个 Docker 主机快速原型化应用时使用。

* 应用服务的自定义 Docker 映像：在 Linux 上部署 Web 应用时，可以使用容器注册表或客户容器中的 Docker 容器。

  > **何时使用**：在 Linux 上将 Web 应用部署到 Docker 映像时使用。
  >
  > **开始使用**：[在 Linux 上使用应用服务的自定义 Docker 映像](../../app-service/quickstart-custom-container.md?pivots=platform-linux%253fpivots%253dplatform-linux)。

### <a name="authentication"></a>身份验证

重要的是：不仅要知道谁在使用你的应用程序，而且还要防止对资源的未授权访问。 Azure 提供了多种方式来对你的应用客户端进行身份验证。

* Azure Active Directory (Azure AD)：Microsoft 基于云的多租户标识和访问管理服务。 你可以通过与 Azure AD 集成将单一登录 (SSO) 添加到你的应用程序。 可以通过直接使用 Azure AD Graph API 或 Microsoft Graph API 访问目录属性。 可以通过使用本机 HTTP/REST 终结点和多平台 Azure AD 身份验证库与支持 OAuth2.0 授权框架的 Azure AD 和 Open ID Connect 相集成。

  > **何时使用**：当想要提供 SSO 体验、使用基于图形的数据，或者对基于域的用户进行身份验证时使用。
  >
  > **开始使用**：若要了解详细信息，请参阅 [Azure Active Directory 开发人员指南](../../active-directory/develop/v2-overview.md)。

* 应用服务身份验证：当选择应用服务来托管应用时，还将获得对 Azure AD 以及社交标识提供程序（包括 Facebook、Google、Microsoft 和 Twitter）的内置身份验证支持。

  > **何时使用**：当想要通过使用 Azure AD、社交标识提供程序或两者来启用应用服务应用中的身份验证时使用。
  >
  > **开始使用**：若要了解有关应用服务中身份验证的详细信息，请参阅 [Azure 应用服务中的身份验证和授权](../../app-service/overview-authentication-authorization.md)。

若要了解有关 Azure 中安全最佳实践的详细信息，请参阅 [Azure 安全最佳实践和模式](../../security/fundamentals/best-practices-and-patterns.md)。

### <a name="monitoring"></a>监视

当应用程序在 Azure 中正常运行时，需要监视性能、关注问题，并了解客户使用应用的方式。 为此，Azure 提供了几个监视选项。

* Application Insights：与 Visual Studio 集成以监视实时 Web 应用的 Azure 托管的可扩展分析服务。 该服务为用户提供所需数据，以持续提高应用的性能和可用性。 无论用户是否在 Azure 上托管应用程序，都会获得这方面的改进。

  > **开始使用**：按照 [Application Insights 教程](../../azure-monitor/app/app-insights-overview.md)。

* Azure Monitor：这项服务有助于对使用 Azure 基础结构和资源生成的指标和日志进行可视化、查询、路由、存档，并对其执行操作。 监视器是用于监视 Azure 资源的单一源，并提供在 Azure 门户中看到的数据视图。

  > **开始使用**：[Azure Monitor 入门](../../azure-monitor/overview.md)。

### <a name="devops-integration"></a>DevOps 集成

无论是预配虚拟机还是发布持续集成的 Web 应用，Azure 都将与大部分常用的 DevOps 工具集成。 用户可以使用已有的工具，并利用对以下工具的支持来最大限度地提升现有体验：

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **开始使用**：如需查看应用服务应用的 DevOps 选项，请参阅 [对 Azure 应用服务的持续部署](../../app-service/deploy-continuous-deployment.md)。
>
> **立即试用：** [尝试几个 DevOps 集成](https://azure.microsoft.com/try/devops/)。

## <a name="azure-regions"></a>Azure 区域

Azure 是一个全球性云平台，在世界各地的许多区域都可以使用。 在 Azure 中预配服务、应用程序或 VM 时，系统会提示选择区域。 此区域表示运行应用程序或存储数据的特定数据中心。 这些区域对应于在 [Azure 区域](https://azure.microsoft.com/regions/)页上发布的特定位置。

### <a name="choose-the-best-region-for-your-application-and-data"></a>选择应用程序和数据的最佳区域

使用 Azure 的好处之一是，你可以将应用程序部署到全球范围内的各种数据中心。 你选择的区域可能会影响应用程序的性能。 例如，最好选择更接近于大部分客户的区域，以减少网络请求中的延迟。 也可以选择你的地区，以符合在某些国家/地区分发应用的法律要求。 最佳的做法是，始终将应用程序数据存储在相同的数据中心，或尽可能靠近托管应用程序的数据中心的数据中心。

### <a name="multi-region-apps"></a>多区域应用

尽管整个数据中心可能会因自然灾害或 Internet 故障等事件而脱机，不过这种情况极少发生。 最佳做法是，将至关重要的商业应用程序托管在多个数据中心，以提供最大的可用性。 另外，使用多区域也可以减少全球用户的延迟，并在更新应用程序时提供更多的弹性。

某些服务，如虚拟机和应用服务，使用 [Azure 流量管理器](../../traffic-manager/traffic-manager-overview.md)启用在两个区域之间进行故障转移的多区域支持，以支持高可用性的企业应用程序。 有关示例，请参阅 [Azure 参考体系结构：在多个区域中运行 Web 应用程序](/azure/architecture/reference-architectures/app-service-web-app/multi-region)。

>**何时使用**：当拥有企业和高可用性应用程序受益于故障转移和复制时使用。

## <a name="how-do-i-manage-my-applications-and-projects"></a>如何管理我的应用程序和项目？

Azure 提供了一套丰富的体验，可让你同时以编程方式和在 [Azure 门户](https://portal.azure.com/)中创建并管理你的 Azure 资源、应用程序和项目。

### <a name="command-line-interfaces-and-powershell"></a>命令行接口和 PowerShell

Azure 提供了两种方法来通过命令行管理应用程序和服务。 可以使用 Bash、终端、命令提示符等工具，也可以使用所选的命令行工具。 通常情况下，可以从命令行执行与在 Azure 门户中一样任务，例如，创建和配置虚拟机、虚拟网络、Web 应用以及其他服务。

* [Azure 命令行接口 (CLI)](/cli/azure/install-azure-cli)：允许你连接到 Azure 订阅，并从命令行根据 Azure 资源对各种任务进行编程。

* [Azure PowerShell](/powershell/azure/)：提供了一组带 cmdlet 的模块，可让你通过使用 Windows PowerShell 来管理 Azure 资源。

### <a name="azure-portal"></a>Azure 门户

[Azure 门户](https://portal.azure.com)是一种基于 Web 的应用程序。 Azure 门户可用于创建、管理和删除 Azure 资源和服务。 其中包括：

* 可配置的仪表板
* Azure 资源管理工具
* 对订阅设置和计费信息的访问权限

有关详细信息，请参阅 [Azure 门户概述](https://azure.microsoft.com/features/azure-portal/)。

### <a name="rest-apis"></a>REST API

Azure 是基于一组支持 Azure 门户 UI 的 REST API 构建的。 其中大多数 REST API 还支持通过编程方式设置和管理来自任何启用 Internet 设备的 Azure 资源和应用程序。 有关完整的 REST API 文档集，请参阅 [Azure REST SDK 参考](/rest/api/)。

### <a name="apis"></a>API

除了 REST API 之外，还有许多 Azure 服务也允许通过使用特定于平台的 Azure SDK 以编程方式管理应用程序中的资源，包括用于以下开发平台的 SDK：

* [.NET](/dotnet/api/)
* [Node.js](/azure/developer/javascript/)
* [Java](/java/azure)
* [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
* [Python](/azure/python/)
* [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
* [Go](/azure/go)

[移动应用](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library)和 [Azure 媒体服务](../../media-services/previous/media-services-dotnet-how-to-use.md)等服务均提供客户端 SDK，可让你从 Web 和移动客户端应用访问服务。

### <a name="azure-resource-manager"></a>Azure 资源管理器

在 Azure 上运行应用有可能会涉及使用多个 Azure 服务。 这些服务遵循相同的生命周期，并且可以被视为一个逻辑单元。 例如，Web 应用可以使用 Web 应用、SQL 数据库、存储、Azure Redis 缓存，以及 Azure 内容分发网络服务。 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)能够使你以组的方式处理应用程序中的资源。 你可以通过单个协调的操作来部署、更新或删除所有资源。

除了逻辑分组和管理相关资源之外，Azure 资源管理器还包括可用于自定义相关资源部署和配置的部署功能。 例如，可以使用资源管理器来部署和配置应用程序。 此应用程序可以包含多个虚拟机、负载均衡器和 Azure SQL 数据库中的数据库作为一个单元。

可使用 Azure 资源管理器模板（JSON 格式的文档）来开发这些部署。 借助这些模板，可使用声明性模板（而不是脚本）来定义应用程序的部署和管理。 模板可用于测试、暂存和生产等不同的环境。 例如，可以使用模板来将按钮添加到 GitHub 存储库，然后只需单击该按钮即可将存储库中的代码部署到一组 Azure 服务中。

> **何时使用**：在希望对通过使用 REST API、Azure CLI 和 Azure PowerShell 以编程方式进行管理的应用执行基于模板的部署时，可以使用资源管理器模板。
>
> **开始使用**：若要开始使用模板，请参阅 [创作 Azure 资源管理器模板](../../azure-resource-manager/templates/syntax.md)。

## <a name="understanding-accounts-subscriptions-and-billing"></a>了解帐户、订阅和计费

作为开发人员，我们都喜欢直接深入了解代码，并尽快开始运行我们的应用程序。 当然，我们也非常希望你尽可能轻松地在 Azure 中开始工作。 为了简化这一过程，Azure 提供了[免费试用版](https://azure.microsoft.com/free/)。 某些服务还具有“免费试用”功能，如 [Azure 应用服务](https://tryappservice.azure.com/)，甚至不需要你创建帐户。 深入了解编码并将应用程序部署到 Azure 固然十分有趣，但花些时间了解 Azure 的工作原理同样至关重要。 具体来说，应该从用户帐户、订阅和计费的角度来了解它的工作原理。

### <a name="what-is-an-azure-account"></a>什么是 Azure 帐户？

如果要创建或使用 Azure 订阅，必须有 Azure 帐户。 Azure 帐户只是 Azure AD 或 Azure AD 信任的某个目录（如某个工作或学校组织）中的标识。 如果你不属于此类组织，则始终可以通过使用受 Azure AD 信任的 Microsoft 帐户来创建订阅。 若要了解有关将本地 Windows Server Active Directory 与 Azure AD 集成的详细信息，请参阅[将本地标识与 Azure Active Directory 集成](../../active-directory/hybrid/whatis-hybrid-identity.md)。

每个 Azure 订阅都与某个 Azure AD 实例存在信任关系。 这意味着，此订阅信任该目录对用户、服务和设备执行身份验证。 多个订阅可以信任同一个目录，但一个订阅只能信任一个目录。 有关详细信息，请参阅 [Azure 订阅与 Azure Active Directory 的关联方式](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

除了定义单个 Azure 帐户标识（也称为“用户”）之外，还可以定义 Azure AD 中的“组” 。 创建用户组是通过使用基于角色的访问控制 (RBAC) 来管理订阅中资源的一种好方式。 若要了解如何创建组，请参阅[在 Azure Active Directory 预览版中创建组](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 此外，也可以通过[使用 PowerShell](../../active-directory/enterprise-users/groups-settings-v2-cmdlets.md) 创建和管理组。

### <a name="manage-your-subscriptions"></a>管理订阅

订阅是链接到 Azure 帐户的 Azure 服务的逻辑分组。 一个 Azure 帐户可包含多个订阅。 Azure 服务计费是按照订阅量进行计算的。 对于按类型提供的可用订阅优惠列表，请参阅 [Microsoft Azure 优惠详细信息](https://azure.microsoft.com/support/legal/offer-details/)。 Azure 订阅有帐户管理员，该角色可以完全控制订阅。 这些订阅还有服务管理员，该角色可以控制订阅中的所有服务。 有关经典订阅管理员的信息，请参阅[添加或更改 Azure 订阅管理员](../../cost-management-billing/manage/add-change-subscription-administrator.md)。 可以使用 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 对单个帐户授予对 Azure 资源的详细控制权限。

#### <a name="resource-groups"></a>资源组

预配新的 Azure 服务时，可在给定订阅中执行此操作。 各个 Azure 服务（也称为资源）在资源组的上下文中创建。 通过资源组，可更加轻松地部署和管理应用程序的资源。 资源组应包含要作为一个单元使用的应用程序的所有资源。 可在资源组之间移动资源，甚至可将资源移到不同的订阅。 若要了解有关移动资源的信息，请参阅[将资源移到新资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

Azure 资源浏览器是一款非常出色的工具，可用于可视化已在订阅中创建的资源。 若要了解详细信息，请参阅[使用 Azure 资源浏览器查看和修改资源](/rest/api/)。

#### <a name="grant-access-to-resources"></a>授予对资源的访问权限

在允许对 Azure 资源进行访问时，最佳做法是，始终为用户提供执行某个给定任务所需的最小特权。

* Azure 基于角色的访问控制 (Azure RBAC)：在 Azure 中，可以在如下指定范围内授予对用户帐户（主体）的访问权限：订阅、资源组或单个资源。 通过 Azure RBAC，可将资源部署到资源组，并为特定用户或组授予权限。 通过它还可限制仅访问属于目标资源组的资源。 此外，还可以授予对单个资源的访问权限，例如虚拟机或虚拟网络。 若要授予访问权限，请将角色分配给用户、组或服务主体。 预定义角色有很多，但也可定义自己的自定义角色。 如果要详细了解，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)。

  > 何时使用：当需要对用户和组进行细致的访问管理，或者当需要使用户成为订阅的所有者时使用。
  >
  > 开始使用：如果要详细了解，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

- 服务主体对象：除了为用户主体和组提供访问权限之外，还可以向服务主体授予相同的访问权限。

  > 何时使用：在以编程方式管理 Azure 资源时，或在授予应用程序访问权限时。 有关详细信息，请参阅[创建 Active Directory 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。

#### <a name="tags"></a>Tags

通过 Azure 资源管理器，你可以将自定义标记分配给单个资源。 标记为键值对，在出于计费或监视目的而需要组织资源时，标记可能会十分有用。 标记提供了一种跨多个资源组跟踪的资源方法。 可以通过以下方式来分配标记：

* 在门户中
* 在 Azure 资源管理器模板中
* 使用 REST API
* 使用 Azure CLI
* 使用 PowerShell

你可以对每个资源分配多个标记。 若要了解详细信息，请参阅[使用标记来组织 Azure 资源](../../azure-resource-manager/management/tag-resources.md)。

### <a name="billing"></a>计费

在从本地计算移动到云托管服务的过程中，对服务使用情况以及相关的成本进行跟踪和估计都是非常重要的问题。 对每月运行的新资源成本进行估算至关重要。 还可以根据当前的支出情况来规划某个给定月份的计费情况。

#### <a name="get-resource-usage-data"></a>获取资源使用情况数据

Azure 提供了一组允许访问资源消耗和 Azure 订阅的元数据信息的计费 REST API。 使用这些计费 API，可让你能够更好地预测和管理 Azure 费用。 可以按小时增量跟踪和分析支出，并创建支出警报。 还可以根据当前使用情况趋势预测将来的计费。

>开始使用：如果要详细了解如何使用计费 API，请参阅 [Azure 消耗 API 概述](../../cost-management-billing/manage/consumption-api-overview.md)

#### <a name="predict-future-costs"></a>预测未来成本

尽管提前估算成本非常困难，但 Azure 提供了可以帮助估算的工具。 它有一个[定价计算器](https://azure.microsoft.com/pricing/calculator/)，可帮助估算部署的资源的成本。 此外，用户也可以使用门户中的计费资源和计费 REST API，根据当前消耗情况来估计未来成本。

>开始使用：要了解详细信息，请参阅 [Azure 使用情况 API 概述](../../cost-management-billing/manage/consumption-api-overview.md)。
