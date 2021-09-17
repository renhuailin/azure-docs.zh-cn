---
title: Azure Spring Cloud 简介
description: 了解 Azure Spring Cloud 的功能和优势，以便在 Azure 中部署和管理 Java Spring 应用程序。
author: karlerickson
ms.service: spring-cloud
ms.topic: overview
ms.date: 03/09/2021
ms.author: karler
ms.custom: devx-track-java, contperf-fy21q2
customer intent: As an Azure Cloud user, I want to deploy, run, and monitor Spring Boot microservices.
ms.openlocfilehash: ce56ac8e85e727cd4efa88b8f59ea7ec3871a8dc
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179333"
---
# <a name="what-is-azure-spring-cloud"></a>Azure Spring Cloud 是什么？

借助 Azure Spring Cloud，可以轻松地将 Spring Boot 微服务应用程序部署到 Azure，不需更改任何代码。  该服务管理 Spring Cloud 应用程序的基础结构，因此开发人员可以专注于其代码。  Azure Spring Cloud 可以通过以下方法提供生命周期管理：综合性监视和诊断、配置管理、服务发现、CI/CD 集成、蓝绿部署等。

以下视频显示了一个应用，该应用由使用 Azure Spring Cloud 在 Azure 上运行的 Spring Boot 应用程序组成。

<br>

> [!VIDEO https://www.youtube.com/embed/1jOXMFc1oRg]

## <a name="why-use-azure-spring-cloud"></a>为何要使用 Azure Spring Cloud？

将应用程序部署到 Azure Spring Cloud 有很多好处。 方法：

* 有效地迁移现有 Spring 应用并管理云缩放和成本。
* 使用 Spring Cloud 模式实现应用现代化，以提高敏捷性和交付速度。
* 以云规模运行 Java，并在不使用复杂基础结构的情况下提高利用率。
* 无需依赖容器化即可快速开发和部署。
* 有效轻松地监视生产工作负载。

Azure Spring Cloud 同时支持 Java [Spring Boot](https://spring.io/projects/spring-boot) 和 ASP.NET Core [Steeltoe](https://steeltoe.io/) 应用。 Steeltoe 支持目前以公共预览版的形式提供。 使用公共预览版产品/服务，你可以在产品/服务正式发布之前体验新功能。

## <a name="service-overview"></a>服务概述

Azure Spring Cloud 是 Azure 生态系统的一部分，用户可以通过它轻松绑定到其他 Azure 服务，其中包括存储、数据库、监视等。

![Azure Spring Cloud 概述](media/spring-cloud-principles/azure-spring-cloud-overview.png)

* Azure Spring Cloud 是适用于 Spring Boot 应用的完全托管服务，可让你专注于构建和运行应用，而无需管理基础结构。

* 只需为 Spring Boot 应用部署 JAR 或代码，或为 Steeltoe 应用部署 Zip，Azure Spring Cloud 就会自动将应用与 Spring 服务运行时和内置的应用生命周期关联起来。

* 监视也很简单。 部署后，可以监视应用性能，修复错误以及快速改进应用程序。

* 与 Azure 的生态系统和服务的完全集成。

* Azure Spring Cloud 具有完全托管的基础结构和内置的生命周期管理且易于监视，可供企业使用。

## <a name="documentation-overview"></a>文档概述

本文档包括说明如何入门和利用 Azure Spring Cloud 服务的部分。

* 入门

    * [启动你的第一个应用](./quickstart.md)
    * [预配 Azure Spring Cloud 服务](./quickstart-provision-service-instance.md)
    * [设置配置服务器]()
    * [构建和部署应用](./quickstart-deploy-apps.md)
    * [使用日志指标和跟踪](./quickstart-logs-metrics-tracing.md)

* 操作说明

    * [开发](how-to-prepare-app-deployment.md)：准备现有的需要部署到 Azure Spring Cloud 的 Java Spring 应用程序。 正确配置后，Azure Spring Cloud 可以提供强大的服务来监视、缩放和更新 Java Spring Cloud 应用程序。
    * [部署](./how-to-staging-environment.md)：如何使用 Azure Spring Cloud 中的蓝绿部署模式来设置暂存部署。 蓝/绿部署是一种 Azure DevOps 持续交付模式，它依赖于在部署新（绿色）版本时保持现有（蓝色）版本的活动性。
    * [配置应用](./how-to-start-stop-delete.md)：启动、停止和删除 Azure Spring Cloud 应用程序。 使用 Azure 门户或 Azure CLI 在 Azure Spring Cloud 中更改应用程序的状态。
    * [缩放](./how-to-scale-manual.md)：使用 Azure 门户中的 Azure Spring Cloud 仪表板或使用自动缩放设置来缩放任何微服务应用程序。 公共 IP 可用于与外部资源（例如数据库、存储和密钥保管库）进行通信。
    * [监视应用](./how-to-distributed-tracing.md)：分布式跟踪工具，可轻松调试和监视复杂问题。 Azure Spring Cloud 将 Spring Cloud Sleuth 与 Azure 的 Application Insights 集成。 这种集成可以通过 Azure 门户提供强大的分布式跟踪功能。
    * [安全应用](./how-to-enable-system-assigned-managed-identity.md)：Azure 资源在 Azure Active Directory 中提供自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。
    * [与其他 Azure 服务集成](./how-to-bind-cosmos.md)：可以将所选 Azure 服务自动绑定到应用程序（例如，将应用程序绑定到 Azure Cosmos DB 数据库），而不必手动配置 Spring Boot 应用程序。
    * [自动化](./how-to-cicd.md)：借助持续集成和持续交付工具，能够以最少的工作量将更新快速部署到现有应用程序，同时将风险降至最低。 Azure DevOps 有助于组织和控制这些关键任务。
    * [故障排除](./how-to-self-diagnose-solve.md)：Azure Spring Cloud 诊断提供的交互式体验可帮助对应用进行故障排除。 不需要任何配置。 如果发现问题，Azure Spring Cloud 诊断会识别问题并指导你获取有助于排查和解决问题的信息。
    * [迁移](/azure/developer/java/migration/migrate-spring-boot-to-azure-spring-cloud)：如何迁移现有的 Spring Cloud 应用程序或 Spring Boot 应用程序，使其在 Azure Spring Cloud 上运行。

如要入门，请参阅：

> [!div class="nextstepaction"]
> [Spring Cloud 快速入门](./quickstart.md)

可在 GitHub 上找到示例：[Azure Spring Cloud 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/)。
